# 在 IndexedDB 中存储(和检索)照片

> 原文：<https://dev.to/raymondcamden/storing-and-retrieving-photos-in-indexeddb-2582>

嘿伙计们-欢迎来到十月的第一篇帖子！是的，我在发帖的时候还是很慢。找新工作让我的创造力受到了一点影响，所以我为这里缺乏内容而道歉。我今天做了一些有趣的事情，所以我想在这里分享一下。

我正在与一个客户合作，他有一个 Cordova 应用程序，该程序利用相机和设备文件系统进行存储。我添加了一些代码来处理表单数据和相关图片的存储，当设备离线时，这些数据将被发送到服务器。我决定像躲避瘟疫一样，避免对文件系统的任何额外使用，而是看看是否可以使用 IndexedDB (IDB)。IDB 现在有了相当不错的支持(谢谢苹果，真的，谢谢)，并且对存储二进制数据也有很好的支持。我决定做一个快速的网络演示，这样我就可以在我的 Android 设备上测试，看看效果如何。接下来是我的测试，这完全是*而不是*生产就绪代码(这就是为什么我没有通过谷歌测试)，但我希望它对其他人有用。我将分享一些代码并解释它们，最后我将分享整个文件。不过，还是要小心使用。

简单地说，我写了一本关于客户端存储的好(imho) [书](https://www.amazon.com/gp/product/1491935111/ref=as_li_tl?ie=UTF8&tag=raymondcamd06-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=1491935111&linkId=239944c4f3cbf1e35ce47f4eb857b2a7)和一个[视频](http://shop.oreilly.com/product/0636920043638.do)版本。但是他们现在都有点老了。与其买这本书(虽然我不会阻止你)，我建议读 MDN 指南:[使用 IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB) 。就像 MDN 上的所有东西一样，它太棒了，我今天用它来刷新我的记忆。

### 存储照片

为了处理照片，我使用了一个带有`capture`属性的简单输入字段:

```
<input type="file" id="pictureTest" capture> 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未见过这个，只是提醒你 HTML 充满了神奇，你不需要总是用 JavaScript 来做很酷的事情。我本可以在这个标签中添加更多的内容来限制图片的选择(当然，这不是你的服务器应该依赖的东西，因为 devtools 可以调整它)，但是我偷懒了。我在 2016 年写了更多关于这个的内容:[不用 PhoneGap 捕捉相机/图片数据-更新](https://www.raymondcamden.com/2016/06/03/capturing-camerapicture-data-without-phonegap-an-update)

我在这个字段中添加了一个`change`处理程序，这样当一张图片被选中时我就会注意到:

```
document.querySelector('#pictureTest').addEventListener('change', doFile); 
```

Enter fullscreen mode Exit fullscreen mode

好了，对于我的 IndexedDB 系统，我设置了以下代码来初始化数据库和 objectstore。为了简单起见，这有点“混淆”,作为提醒，我将在下面的一个文件中分享所有内容。

```
let db;
//stuff
let request = indexedDB.open('testPics', dbVersion);

request.onerror = function(e) {
    console.error('Unable to open database.');
}

request.onsuccess = function(e) {
    db = e.target.result;
    console.log('db opened');
}

request.onupgradeneeded = function(e) {
    let db = e.target.result;
    db.createObjectStore('cachedForms', {keyPath:'id', autoIncrement: true});
    dbReady = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你是 IndexedDB 的新手，我想更多的是有意义的，但如果没有，请在下面的评论中问我。我定义对象存储的最后一点，我已经告诉它添加一个`id`字段，并自动为它编号为主键。

好了，让我们来看看存储:

```
function doFile(e) {
    console.log('change event fired for input field');
    let file = e.target.files[0];
    var reader = new FileReader();
    //reader.readAsDataURL(file);
    reader.readAsBinaryString(file);

    reader.onload = function(e) {
        //alert(e.target.result);
        let bits = e.target.result;
        let ob = {
            created:new Date(),
            data:bits
        };

        let trans = db.transaction(['cachedForms'], 'readwrite');
        let addReq = trans.objectStore('cachedForms').add(ob);

        addReq.onerror = function(e) {
            console.log('error storing data');
            console.error(e);
        }

        trans.oncomplete = function(e) {
            console.log('data stored');
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是输入字段的`change`处理程序。请注意，我没有注意到从“我选择了一个文件”到“我清除了一个文件”的变化，但是正如我所说的，这是一个快速测试。我获取文件的句柄，创建一个`FileReader`，然后读取二进制数据。正如你所看到的注释行(我通常从博客文章中删除)，我最初使用了返回 Base64 字符串的`readAsDataURL`。理论上，二进制数据更小，但我认为你可以使用任何一种。唯一真正的区别是你以后如何处理数据。在我的演示中，我将它重新显示在屏幕上，这就有所不同了。如果您通过 POST 操作将它存储到服务器，那么您的服务器端代码也需要以不同的方式处理它。

当我读入二进制数据时，我创建了一个包含两个字段的对象，一个`created`字段和二进制数据。在我真正的应用程序中，我也会有一堆表单数据。然后，我打开 IndexedDB 数据库的一个事务，并存储我的文件。正如我所说的，我对 IDB 有点生疏了，但是我的天啊，我喜欢这种简单性。(如果这对你来说仍然很复杂，还有很多库，比如[德克谢](http://dexie.org/)。

正如我所说的，我的目的是加载和发布这些数据，但是对于我的测试，我决定只在 DOM 中呈现这些数据。我添加了一个小表格和空白图片:

```
<h2>Test Image Below</h2>
<input type="number" id="recordToLoad"> <button id="testImageBtn">Test</button>

<img id="testImage"> 
```

Enter fullscreen mode Exit fullscreen mode

我在那个按钮上添加了一个 click 处理程序，目的是让您输入要加载的数据的 PK。我用的是 Chrome，他们的 IDB 开发工具做得非常好。

```
function doImageTest() {
    let image = document.querySelector('#testImage');
    let recordToLoad = parseInt(document.querySelector('#recordToLoad').value,10);
    if(recordToLoad === '') recordToLoad = 1;

    let trans = db.transaction(['cachedForms'], 'readonly');

    let req = trans.objectStore('cachedForms').get(recordToLoad);
    req.onsuccess = function(e) {
        let record = e.target.result;
        console.log('get success', record);
        image.src = 'data:image/jpeg;base64,' + btoa(record.data);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您必须将二进制数据返回到 base64 进行渲染，这是底部的`btoa`部分。这是我从未用过的函数之一，直到我发现它是一个随机的 StackOverflow 问题。我也完全读懂了毛伊岛的声音:

[![](img/396da17320fe076fe0a00719316ab6ad.png "Closeup of Maui")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9pCRm-ET--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/10/maui.jpg)

这很有效。我在我的 Android 设备上的桌面和移动 Chrome 上进行了测试。

[![](img/cf5fac2938d23208602140d280ce0c6b.png "Chrome Devtools")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m2ClQ4_l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/10/maui2.jpg)

这可能有点难以理解，但如果你不知道，Chrome 可以“远程调试”通过 USB 连接的 Android 设备。你可以通过桌面打开 URL，打开开发工具，甚至获得浏览器的屏幕截图。它非常方便，虽然不是新的，但却是一个很好的工具。

哎呀！我忘了包括整个脚本。给你。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width">
    </head>
    <body>

        <input type="file" id="pictureTest" capture>

        <h2>Test Image Below</h2>
        <input type="number" id="recordToLoad"> <button id="testImageBtn">Test</button>

        <img id="testImage"> 

        <script>
            let db;
            let dbVersion = 1;
            let dbReady = false;

            document.addEventListener('DOMContentLoaded', () => {
                console.log('dom content loaded');

                document.querySelector('#pictureTest').addEventListener('change', doFile);

                document.querySelector('#testImageBtn').addEventListener('click', doImageTest);

                initDb();
            });

            function initDb() {
                let request = indexedDB.open('testPics', dbVersion);

                request.onerror = function(e) {
                    console.error('Unable to open database.');
                }

                request.onsuccess = function(e) {
                    db = e.target.result;
                    console.log('db opened');
                }

                request.onupgradeneeded = function(e) {
                    let db = e.target.result;
                    db.createObjectStore('cachedForms', {keyPath:'id', autoIncrement: true});
                    dbReady = true;
                }
            }

            function doFile(e) {
                console.log('change event fired for input field');
                let file = e.target.files[0];
                var reader = new FileReader();
//              reader.readAsDataURL(file);
                reader.readAsBinaryString(file);

                reader.onload = function(e) {
                    //alert(e.target.result);
                    let bits = e.target.result;
                    let ob = {
                        created:new Date(),
                        data:bits
                    };

                    let trans = db.transaction(['cachedForms'], 'readwrite');
                    let addReq = trans.objectStore('cachedForms').add(ob);

                    addReq.onerror = function(e) {
                        console.log('error storing data');
                        console.error(e);
                    }

                    trans.oncomplete = function(e) {
                        console.log('data stored');
                    }
                }
            }

            function doImageTest() {
                console.log('doImageTest');
                let image = document.querySelector('#testImage');
                let recordToLoad = parseInt(document.querySelector('#recordToLoad').value,10);
                if(recordToLoad === '') recordToLoad = 1;

                let trans = db.transaction(['cachedForms'], 'readonly');
                //hard coded id
                let req = trans.objectStore('cachedForms').get(recordToLoad);
                req.onsuccess = function(e) {
                    let record = e.target.result;
                    console.log('get success', record);
                    image.src = 'data:image/jpeg;base64,' + btoa(record.data);
                }
            }
        </script>

    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode