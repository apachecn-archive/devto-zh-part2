# 将 Cloudinary 与 Angular 6 和 Firebase Firestore 一起使用

> 原文：<https://dev.to/sirbootoo/using-cloudinary-with-angular-6-and-firebase-firestore-1f9j>

嘿，你好，

很抱歉延迟了，自从我发帖以来已经过了很长时间，但这将会改变。

今天我们从[第 1 部分](https://medium.com/@_BooToo_/using-cloudinary-with-angular-4-and-firebase-3db8314da3f1)教程中停止的地方继续。

这次我们将有一个简单直接的教程，下面是在[stack blitz]中进行的教程。

首先，我们将安装 firebase

```
npm install firebase angularfire2 --save 
```

Enter fullscreen mode Exit fullscreen mode

成功安装 firebase 库后，我们将几个 Angularfire 模块导入到我们的应用模块文件 app.module.ts

```
import { AngularFireDatabaseModule, AngularFireList } from 'angularfire2/database';
import { AngularFireModule } from 'angularfire2'; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的 app.module.ts

在进入我们的应用程序模块文件之前，我们需要设置系统将与之交互的环境变量，我们正在设置的变量是 firebase 和 cloudinary 的基本信息

在我们的环境文件中，这些文件位于 src/environment/

environment.ts 文件负责在开发过程中设置环境变量，而 environment-prod . TSI 用于生产模式。

在我们的 environment.ts 文件中有这样的内容。

```
export const environment = {
production: false,
firebase: {
apiKey: "YOUR API KEY",
authDomain: "YOUR AUTH DOMAIN",
databaseURL: "YOUR DATABASE URL",
projectId: "YOUR PROJECT ID",
storageBucket: "YOUR STORAGE BUCKET",
messagingSenderId: "YOUR MESSAGING SENDER ID"
}
}; 
```

Enter fullscreen mode Exit fullscreen mode

可以从 firebase 控制台获取配置。

在安装了节点库并用 firebase 信息配置了 emvironment 变量之后，我们需要将 AngularFire2 和 Firebase 导入到我们的 AppModule 中。因此，我们的 AppModule 文件将如下所示:

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';
import { AppComponent } from './app.component';
import { AngularFireDatabaseModule, AngularFireList } from 'angularfire2/database';
import { AngularFireModule } from 'angularfire2';
import { environment } from '../environments/environment';
import { Ng2CloudinaryModule } from 'ng2-cloudinary';
import { FileUploadModule } from 'ng2-file-upload';
@NgModule({
imports:      [ BrowserModule,
FormsModule,
AngularFireDatabaseModule,
AngularFireModule.initializeApp(environment.firebase),
Ng2CloudinaryModule, FileUploadModule ],
declarations: [ AppComponent ],
bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

导入 AngularFireModule，AngularFireDatabaseModule 后，我们用环境文件中的信息初始化我们的 AngularFireModule，就像我们上面所做的那样

angularfiremodule . initialize App(environment . firebase)
这就是 AppModule 文件的全部内容，现在让我们来看看我们的 App 组件文件或您想要在其中使用 Firebase 的任何自定义组件。

将我们的图像上传到 Cloudinary 后，我们需要在 Firebase firestore 数据库中存储一些数据，所以我们使用

```
this.af.list('/cloudinaryupload').push(this.res) 
```

Enter fullscreen mode Exit fullscreen mode

到 firebase firestore 数据库的数据。

在我们从数据库中检索数据之前，我们需要有一个数据库的引用。为了做到这一点，我们需要做的就是

```
const ref = this.af.database.ref('/cloudinaryupload'); 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个对我们试图从中检索的集合的引用，之后我们使用
从集合中获取数据

```
ref.on('value', (snapshot) => {}) 
```

Enter fullscreen mode Exit fullscreen mode

with snapshot，这是回调的响应。为了将快照用作视图中所需迭代的对象数组，我创建了一个函数 snapshottoarray，它返回快照的对象数组表示。

App.component.ts 的完整代码

```
import { Component } from '@angular/core';
import { AngularFireDatabase, AngularFireList } from 'angularfire2/database';
import { CloudinaryOptions, CloudinaryUploader } from 'ng2-cloudinary';
@Component({
selector: 'my-app',
templateUrl: './app.component.html',
styleUrls: ['./app.component.css']
})
export class AppComponent {
name = 'Angular 6';
data: any[] = [];
cloudName = 'YOUR CLOUD NAME';
image_id;
res;
uploader: CloudinaryUploader = new CloudinaryUploader(
new CloudinaryOptions({
cloudName: this.cloudName,
uploadPreset: 'YOUR UPLOAD PRESET'
})
);
loading: any;
constructor(private af: AngularFireDatabase) {
this.getImages();
}
upload() {
this.loading = true;
this.uploader.uploadAll(); // call for uploading the data to Cloudinary
/* Getting the success response from Cloudinary. */
this.uploader.onSuccessItem = (item: any, response: string, status: number, headers: any): any => {
this.res = JSON.parse(response);
this.loading = false;
this.image_id = this.res.public_id;
console.log(this.res);
this.af.list('/cloudinaryupload').push(this.res) // Storing the complete response from Cloudinary
}
/* Getting the Error message Cloudinary throws. */
this.uploader.onErrorItem = function (fileItem, response, status, headers) {
console.info('onErrorItem', fileItem, response, status, headers)
};
}
/* Listing all the images and their information from the cloudinaryupload collection in firebase */
getImages() {
const ref = this.af.database.ref('/cloudinaryupload');
/* Fat arrow functions which exist in ES6 it is the same as function(snapshot) {} */
ref.on('value', (snapshot) => {
this.data = this.snapshotToArray(snapshot);
});
}
/*
* Responsible for converting Firebase snapshot to an array.
*/
snapshotToArray(snapshot) {
var returnArr = [];
snapshot.forEach(function (childSnapshot) {
var item = childSnapshot.val();
item.key = childSnapshot.key;
returnArr.push(item);
});
return returnArr;
};
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是上面教程的演示和代码库

代码演示示例的 Stackblitz

[演示和代码](https://stackblitz.com/edit/cloudinary-firebase)