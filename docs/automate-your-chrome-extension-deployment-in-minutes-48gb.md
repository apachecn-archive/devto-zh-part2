# 几分钟内自动完成 chrome 扩展部署！

> 原文：<https://dev.to/gokatz/automate-your-chrome-extension-deployment-in-minutes-48gb>

###### 【西蒙·施密特在 [Unsplash](https://unsplash.com/photos/dDCf0-c4RTU) 上的封面照片】

**注:**最近(2018 年 9 月 2 日)改写了这篇博客，使用了*‘chrome-web store-upload’*(之前为*‘chrome-web store-upload-CLI’*)，使其更加直白。

构建 chrome 扩展很有趣！但是部署有点乏味，因为它需要很少的手动过程。在这篇文章中，我将向你展示如何在几分钟内自动完成 chrome 扩展的构建、上传和发布过程🔥🔥

首先，感谢以下 NPM 软件包的作者，我将用它们来实现自动化。

*   [chrome-网络商店-上传](https://www.npmjs.com/package/chrome-webstore-upload)
*   [压缩文件夹](https://www.npmjs.com/package/zip-folder)

让我们开始吧。自动化部署过程的最佳位置之一是 CI/CD 管道。我将在`gitlab-ci.yml`文件的帮助下使用 GitLab 的内置 CI 环境进行演示。这同样适用于 Github 的任何 CI 服务，如 Travis、Circle-CI 等。,

作为 chrome 扩展的作者，你可能知道下面的发布步骤。

*   压缩扩展文件夹(确保删除清单版本)
*   上传到 chrome 网上商店。
*   发布扩展的上传版本。

我将使用 node 编写自动化脚本。因此，让我们在根目录中创建一个节点文件(`deploy.js`)来使用`gitlab-ci.yml`文件从 CI 环境中调用它。

### 压缩扩展文件夹:

使用 [zip-folder](https://www.npmjs.com/package/zip-folder) 包，压缩将只需要一个命令！

```
const zipFolder = require('zip-folder');
let folderName = 'path/to/folder'; 
// I too hate placeholders! will be attaching a sample file at the end of this writeup
let zipName = '/path/to/archive.zip';

zipFolder(folderName, zipName, function(err) {
    if(err) {
        console.log('oh no! ', err);
    } else {
        console.log(`Successfully zipped the ${folderName} directory and store as ${zipName}`);
        // will be invoking upload process 
    }
}); 
```

呜哇！你的分机拉好了吗🤟🏻将此添加到您的`deploy.js`文件中。

### 上传至 chrome 网络商店:

又来了一个包， [chrome-webstore-upload](https://github.com/DrewML/chrome-webstore-upload) 。在这个包的帮助下，我们可以将压缩的扩展上传到 chrome 网络商店。

使用以下命令在您的扩展项目上安装软件包:

`yarn add chrome-webstore-upload -D`

要将文件上传到 webstore，这个包需要您的扩展项目的`client ID`、`client secret`和`refresh token`。如果你不熟悉这些术语，不要担心。要使用 webstore APIs，Google 需要一些标识符和凭证来授权您并识别您的扩展。

要获得所有这三个凭证，请按照这里提到的说明[进行操作](https://github.com/DrewML/chrome-webstore-upload/blob/master/How%20to%20generate%20Google%20API%20keys.md)。软件包作者 [Andrew](https://twitter.com/drewml) 在添加这个指南方面做得非常好。

*[花点时间生成所有三个 id 和凭证...]*

一旦您完成了获取所有这些 id 和凭证，将这些凭证存储在您的 CI 环境变量(GitLab)或其他 CI 服务的环境变量中，如 Travis 的 [env 变量](https://docs.travis-ci.com/user/environment-variables/)或 circle-ci 的 [env 变量](https://circleci.com/docs/2.0/env-vars/)或您选择的任何变量。⚠️避免将这些变量签入你的代码库，因为这些是你的 API 凭证(就像你的密码！)

现在，我们可以开始将 zip 文件上传到 webstore 上传包。该步骤必须在压缩过程成功时调用。

要使用 webstore API，请使用创建的凭证作为种子。植入凭证后，创建需要上传的压缩扩展名的文件流。然后，使用创建的流调用上传 API ( `uploadExisting`)。

```
// getting all the credentials and IDs from `gitlab-ci.yml` file
let REFRESH_TOKEN = process.env.REFRESH_TOKEN; 
let EXTENSION_ID = process.env.EXTENSION_ID;
let CLIENT_SECRET = process.env.CLIENT_SECRET;
let CLIENT_ID = process.env.CLIENT_ID;

const webStore = require('chrome-webstore-upload')({
  extensionId: EXTENSION_ID,
  clientId: CLIENT_ID,
  clientSecret: CLIENT_SECRET,
  refreshToken: REFRESH_TOKEN
});

function upload() {
  const extesnionSource = fs.createReadStream(zipName);
  webStore.uploadExisting(extesnionSource).then(res => {
    console.log('Successfully uploaded the ZIP');    

    // call publish API on success
  }).catch((error) => {
    console.log(`Error while uploading ZIP: ${error}`);
    process.exit(1);
  });
} 
```

`process.env`是将变量从`gitlab-ci.yml`文件传递到节点进程的一种方式。我将在这篇文章的后面展示它是如何工作的。

上述方法将把提到的 zip 文件作为草稿上传到网络商店。你可以在你的[开发者控制台](https://chrome.google.com/webstore/developer/dashboard)查看草稿。同样，确保你**撞上了清单版本**。否则，API 将无法上传 zip 文件！

一旦我们成功地将 zip 上传到 webstore，我们就可以使用 webstore 包的`publish` API 来发布它:

像上传 API 一样，我们可以用同样的方式调用发布 API。确保在上传命令成功时调用这个 API。

```
 publish() {
  // publish the uploaded zip
  webStore.publish().then(res => {
    console.log('Successfully published the newer version');
  }).catch((error) => {
    console.log(`Error while publishing uploaded extension: ${error}`);
    process.exit(1);
  });
} 
```

你猜怎么着您已经完成了扩展部署过程的自动化😍让我们把所有的片段放在一起，产生节点流程文件- `deploy.js`

正如我所承诺的，样本`deploy.js`文件如下！留作参考。用代码弄脏你的手💻