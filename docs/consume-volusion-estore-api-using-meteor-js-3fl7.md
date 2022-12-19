# 使用 Meteor JS 消费卷 eStore API

> 原文：<https://dev.to/cfleischhacker/consume-volusion-estore-api-using-meteor-js-3fl7>

本例中的\server\main.js 对 Volusion eStore 执行 API 调用，将响应转换为 JSON 并插入到 MongoDB 中。

\client\main.js 进行调用并显示来自 MongoDB 的数据。

> 流星创造了 volusionapi
> 
> cd volusionapi
> 
> 流星添加 http peerlibrary:xml2js

复制这些文件[https://github.com/cjfleischhacker/MeteorVolusionAPI.git](https://github.com/cjfleischhacker/MeteorVolusionAPI.git)或者创建这些文件:

**\ client \ main . html**

`

volusion

# 欢迎来到流星！

{{> vapi}}

### 列出课程

{ { error message } }

{{#each courses}} {{/each}}

| 身份证明 | 密码 | 名字 |
| --- | --- | --- |
| {{ProductID}} | {{ProductCode}} | {{ProductName}} |

**\ client \ main . js**

 `import { Template } from 'meteor/templating';
import { ReactiveVar } from 'meteor/reactive-var';
import './main.html';
Courses = new Mongo.Collection('courses');`

Meteor.call('getJSONFromAPI '，function (err，RES){

if(err){

console . log(err)；

} else {

console . log(RES . XML data . products)；

}

})；

Template.vapi.onCreated(函数 helloOnCreated(){

console . log(' oncreate…')；

})；

template . vapi . helpers({

courses:function(){

return courses . find()；
}

})；

**\ server \ main . js**

 `import { Meteor } from 'meteor/meteor';
import { xml2js } from 'meteor/peerlibrary:xml2js';
Courses = new Mongo.Collection('courses');`

meteor . methods({

' getJSONFromAPI ':function(){

this . unblock()；
var API URL = '[http://store.volusion.com/net/WebService.aspx?
Login=email@mystore.com&encrypted password = yourvolusionpiencrypted password&EDI _ Name = Generic](http://store.volusion.com/net/WebService.aspx?Login=email@mystore.com&EncryptedPassword=YourVolusionAPIEncryptedPassword&EDI_Name=Generic)\ Products&SELECT _ Columns = p . product code，p.ProductName，pd。'产品描述'

；

var response = meteor . wrap async(API call)(API URL)；
T3】const result = XML 2 js . parsetringsync(response，{ explicitArray: false，empty tag:undefined })；

为(var I = 0；I<result . XML data . products . length；i++){

courses . insert(result . XML data . products[I])；

}

返回结果；
T13)}
T15)})；

var apiCall = function (apiUrl，callback) {

// try…catch 允许您处理错误

try {

var response = http . get(API URL)。内容；

//成功的 API 调用返回无错误

回调(null，response)；

} catch(error){

//如果 API 响应了错误消息和有效载荷

If(error . response){

var error code = error . response . data . code；

var error message = error . response . data . message；

//否则使用通用错误消息

} else {

var error code = 500；

var errorMessage = '无法访问 API '；

}

//创建一个错误对象，通过回调

var myError = new Meteor 返回。错误(errorCode，error message)；

回调(myError，null)；

}

}

> 流星