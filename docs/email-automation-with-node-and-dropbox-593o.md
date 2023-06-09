# 使用 Node 和 Dropbox 实现电子邮件自动化

> 原文：<https://dev.to/daviddeejjames/email-automation-with-node-and-dropbox-593o>

> 我选择一个懒惰的人去做艰苦的工作。因为一个懒惰的人会找到一个简单的方法去做。比尔·盖茨

我觉得这句话描述了我为什么喜欢做程序员。我们通常很懒，但却很擅长解决问题，包括我们自己的问题！有时，这甚至可能不涉及编写代码，但是大多数时候你可以假设我们已经这样做了🤓

所以，我最近的问题是:我妈妈被要求每两周给她的客户发一份发票，她是一个电脑盲。这是手写的，需要扫描成 PDF 格式，因为我已经不常在家了，所以我姐姐帮她扫描了，但是她懒得整理邮件，所以她把它放在了我的 Dropbox 里，这样我就可以把它发给客户了。

我讨厌这整个过程...并且接收到“你发送发票了吗？”短信。

#### 对我来说涉及的步骤是:

1.  从 Dropbox 下载文件
2.  登录妈妈的电子邮件帐户
3.  给客户打一封非常普通的电子邮件
4.  附加下载的文件
5.  发送电子邮件

**解决方案**:将所有事情自动化！

## Javacript/Node 来救援了！

Javascript 和 Node 似乎最适合我的解决方案，因为我知道我需要运行一个服务器端应用程序来定期检查我的 Dropbox 以找到文件。我也正在努力成为一名更全面的开发者，所以我知道这将是一次很好的学习机会。

最近，我完成了 [Wes Bos 的学习节点](https://learnnode.com/)课程，这极大地帮助了我最终解决方案的设计选择。这包括但不限于:Node、ES6、 [Promises](https://scotch.io/tutorials/javascript-promises-for-dummies) 、 [Nodemailer](https://github.com/nodemailer/nodemailer) 、 [Node Cron](https://github.com/kelektiv/node-cron) 以及用于持续部署的 shell 脚本(但我将在下一篇博文中进一步探讨这些内容- [在 Twitter 上关注我！](http://twitter.com/daviddeejjames))。

我不会对应用程序的细节进行过多的描述，因为你可以在 GitHub 上查看一下。但是，我想进一步解释我所面临的问题，我如何改进这个应用程序，以及哪些实用程序使编写这个应用程序成为一种乐趣！

## 承诺和 Dropbox API

之前，我曾使用 PHP 与 Dropbox API 一起创建了一个[应用程序](https://github.com/daviddeejjames/randophoto)，它会随机选取一组照片，并将其显示在网页上。这是相当基本的，只是感觉不太对，因为我们只是用 curl 函数调用 API，这些天我尽量少用 PHP。

当构建发票应用程序时，我发现 Dropbox 已经创建了一个[JavaScript SDK](https://github.com/dropbox/dropbox-sdk-js)来与 API 交互。这很令人兴奋，当我阅读文档发现它是基于承诺的时候，这更令人兴奋！承诺意味着您可以轻松地将几个 API 调用链接起来，以获得您需要的数据或执行您需要的操作，而几乎不用付出任何努力。

这是一个下载文件的承诺链的例子。它假设您正在传递文件的路径，您可以使用另一个 API 调用/promise 轻松获得该路径。

```
const Dropbox = require('dropbox');
const dbx = new Dropbox({ accessToken: process.env.ACCESS_TOKEN });

exports.getFile = function (path) {

  const file = dbx.filesDownload({ path: path })
    .then(function (response) {
      return response;
    })
    .catch(function (error) {
      console.log('Error downloading the file ❎');
      return Promise.reject(error);
    });

  return file;
}; 
```

Enter fullscreen mode Exit fullscreen mode

居然不是黄油！这么简单，多文件。🐕

为了证明我不是在虚张声势，我创建了另一个函数，在邮件发出后调用它。这会将 Dropbox 中的文件移动到另一个文件夹，以表明该发票已经发送。

```
exports.archiveFile = function (path, subFolderName) {
  const archivedFile = dbx.filesMove({
    from_path: path,
    to_path: '/sent/' + subFolderName + path,
    allow_shared_folder: true,
    autorename: true,
    allow_ownership_transfer: true
  })
    .then(function (fileMove) {
      console.log('File ' + fileMove.name + ' archived successfully! 🗳️');
      return fileMove;
    })
    .catch(function (error) {
      console.log('Error archiving the file 💥');
      return Promise.reject(error);
    });

  return archivedFile;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我将客户端名称作为`subFolderName`传递，这意味着您将获得一个组织良好的文件路径，如`/sent/client-name/INV0001.PDF`

### 但是邮件呢？

哦，对了，所以在我们归档文件之前，我们显然要发送电子邮件。这封电子邮件的创建涉及一些小的部分，但发送它是非常简单的。

由于我妈妈有多个客户端，该解决方案需要结合某种形式的可重用性和可伸缩性。我通过将每个客户端创建为一个 JSON 文件来实现这一点，如下所示:

```
{  "name":  "Recipient",  "email":  "test@email.com",  "subject":  "An interesting Email Subject",  "text":  "Hi John Doe,\n\nInvoice attached.\n\nKind Regards,\nJane Doe",  "file-prefix":  "INV"  } 
```

Enter fullscreen mode Exit fullscreen mode

这确保了从 Dropbox 发送的每个文件都将根据其文件名前缀发送出去，允许每个客户端在电子邮件中使用不同的名称、电子邮件、主题或文本。这也意味着，如果她有更多的客户，创建新的 JSON 文件也是自动化列车的一部分。🚂

使用上面的数据和对 Dropbox API 的调用，我们能够构建我们的电子邮件，并使用 [Nodemailer](https://github.com/nodemailer/nodemailer) 发送它。

通过 Nodemailer 发送电子邮件的代码是一个带有几个选项参数的函数调用(如下所示)。它们被传递，然后与传输函数一起使用，其大部分配置集使用环境变量(因为您不希望有人向您发送垃圾邮件或知道您的 SMTP 凭证)。

在这个应用程序中，我使用了一个二进制文件流/缓冲区来添加文件，这听起来比实际或需要的要复杂得多。实际上，这只是意味着我们从 Dropbox 中获取文件的二进制版本，将其保存为变量，将其传递到缓冲区，然后它就成为了文件附件。

```
 ...

 const sendInvoice = attachedFile.searchFilePath(filePrefix)
      .then(function (filePath){
        foundFilePath = filePath;
        const file = attachedFile.getFile(filePath);
        return file;
      })
      .then(function (file) {
        const mailPromise = mail.send({
          email: recipient.email,
          subject: recipient.subject,
          text: recipient.text,
          attachments: {   // binary buffer as an attachment
            filename: file.name,
            content: new Buffer(file.fileBinary, 'binary'),
            encoding: 'binary'
          }
        });
        return mailPromise;
      })

  ... 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。应用程序的大部分都在几个函数调用中。如果`mailPromise`解决了，我们将发送电子邮件。

为了在开发过程中测试电子邮件的发送，使用像 [Mailtrap](https://mailtrap.io/) 这样的服务是一个救命稻草，因为它是免费的，不会填满任何人的收件箱😆

一旦我开始制作，我就把它改成了 [Mailgun](https://www.mailgun.com/) ，因为你每个月可以免费发送多达 10，000 封电子邮件！

### 自动化🤖

看起来这个应用程序涵盖了你之前提到的所有步骤...但是是什么让它自动的呢？

实际上并不多，只是使用一个 [cron](https://github.com/kelektiv/node-cron) 每小时运行一次该函数(或者根据您的需要运行多次)。cron 是“给操作系统或服务器的命令，用于在指定时间执行的作业”。在这种情况下，应用程序检查是否有任何要发送的文件。如果有，执行应用程序的其余部分；如果没有，什么都不要做。如前所述，承诺链让这个过程变得轻而易举。

像所有事情一样，总有改进的余地。cron 可以通过在文件上传后运行该函数来移除，很明显你不能只通过 API 来移除，但是聪明的 cookies 会意识到你可以通过使用 webhooks 来移除。

## 结论

这个项目非常令人愉快！我学到了很多东西，从节点到 Shell 脚本，从 Cron jobs 到 Promises。像这样的小项目真的会推动你成为一名开发人员。它们让你成为你想成为的完美主义者，并创造一些东西以多种方式改善你的生活(有时也改善别人的生活)。