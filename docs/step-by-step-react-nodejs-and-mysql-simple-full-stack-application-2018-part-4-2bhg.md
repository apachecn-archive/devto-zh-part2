# 步步 React，NodejS 和 MySQL 简单全栈应用 2018(第四部分)

> 原文：<https://dev.to/kmaryam27/step-by-step-react-nodejs-and-mysql-simple-full-stack-application-2018-part-4-2bhg>

#### 在这篇文章中，我描述了使用 react 库开发简单的前端，以在浏览器中显示我们的后端数据

对于 react 库的开始工作，可以使用由脸书开发人员构建的 create-react-app 工具来帮助您构建 react 应用程序。欲了解更多信息，请访问:[https://github.com/facebook/create-react-app](https://github.com/facebook/create-react-app)

1-打开 GitBash，新建一个文件夹，运行这个命令:npm install-g express-generator on it:
[![](img/3be1569cb8247d112672a80fa170844b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OsYOhq8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61nueh1uwtqnntu77396.png)
with command:express nameofbackenfolder 默认为项目做一个后端文件夹，用 NPM install 命令， 安装您需要的所有模块:
[![](img/2de268ef41974de7403db1e64c6584a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DwXYVyMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j3u52bxvtn80el2tilkx.png) 
2-为前端创建 react 转到后端文件夹并运行 npm install -g create-react-app，安装后运行:create-react-app 客户端
3-打开 VSCode 项目，然后打开客户端文件夹的 package.json 文件并向其中添加代理密钥:
[![](img/f7ba3575afbb2f2f5eacf07f22199d77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7hsckgOK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3gs9ef98gkagr342zm5c.png) 
4-在后端文件夹中的 app.js 中，就像您在后端创建的 server.js 一样添加这些
const connection = MySQL . create connection({
主机:' localhost '，
用户:' root '，
密码:' myjs123 '，//您的 mysql db
数据库的密码:' simple-react-SQL-db '
})；

connection . connect(function(err){
(err)？console . log(err+' +++++++++++++++/////////'):console . log(' connection * * * * * * * * *)；
})；

要求('。/routes/html-routes')(app，连接)；

5-在终端中打开 app.js 并运行 npm install mysql 将 mysql 数据库添加到新项目中
6-将 html-routes.js 文件更改为您的 html-route.js，该文件写在您的后端
[![](img/735c671d03e7364c5a3aa0d21a2669a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JSUCGl61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10zdu9dwc22yd09y62b3.png) 
7-在 app.js 文件的客户端文件夹中移除标签:
[![](img/d48f422090704f98363ec113e0ca5a70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SyT-y0uV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fy4b3ou0rnc9qvc38brw.png) 
8-在 render 方法之前编写这些代码:
state = {
users:[]
}

}

getUsers = _ = > {
fetch('[http://localhost:3001 '](http://localhost:3001'))
。然后(response =>console . log(response))//response . JSON())
。然后(({ response })=>this . setstate({ users:' response . users ' }))
。catch(error =>console . log(error))；
}
showUsers = user = >

{user.username}

*   (在下一篇文章中将解释这些代码的概念)9-然后将 render 函数改为:render() {//building react 方法，该方法来自 od react 组件 const { users } = this.statereturn (//jsx 代码并且只能返回一个父标记{ users . map(this . show users)})；}

[![](img/e9711b80871d1ee0e45232254d204e6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NyFgFStD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gzzfb97gs2rp3ddhm9zi.png) 
10-运行 MySQL 命令行客户端添加密码并回车启动 Mysql
11-在 bin 文件夹中打开 www 文件并将端口 3000 改为 3001:

[![](img/d97c54f2757bffa9cc53edcec0bfe050.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AbpV9n-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lfk35i22lj9njn54hplv.png) 
12-在这一步结束时，您必须将后端文件夹的 app.js 打开到 VSCode 的终端，并运行 npm start 命令以在端口 3001 上运行服务器:
[![](img/f6bd55edcac26ca38f9bfdb763a78e0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LZnStQgQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dm33tp58udxy3mrg5ab7.png) 
13-现在打开 gitbash 并转到客户端 app.js 路由，然后运行命令:npm star 以运行 react:
[![](img/13f4f671837d18bf9e4ad441cd5a1f9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jrcw_JW3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nz77fnw5ofe40ooqqmzl.png) 
现在您必须在端口 3001 上看到您的后端，在端口上看到 fronend 由于“Cors”异常，我将在下一篇文章中继续解决这个问题。
[![](img/4df2648187404649853a12818f806fb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WtyL3Qlk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r2if9y7zfu37n4uj431g.png)
[![](img/7a5acbcc62708971e56e10cc9fccbad9.png)T21】](https://res.cloudinary.com/practicaldev/image/fetch/s--CsDnE-lc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w9vp4qv75q3ueejr3igj.png)