# 无声的突破时刻:JSON 对象数组访问

> 原文：<https://dev.to/jenc/dumb-breakthrough-moment-json-object-array-access-g8d>

在这个问题上坚持了半个小时后，一个朋友看了看我的 Vue 和服务器代码。

"你实际上并没有访问数组，你只是作为一个对象接收响应."

*宋飞音乐播放*

这是我在`console.log` -ed `this.response.data`时看到的

[![JSON response object from call to a Google Sheets endpoint. This object included an array of row objects, which contained arrays of info about cats that I couldn't access and thus iterate through in a v-for loop](../Images/fb9815d63432b44bd21275922ef12425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1R29yeAz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/to3vmvhaaa7x1fe6gjfz.png)

如果我想遍历行对象的数组，这就是我应该看到的:`this.response.data.rows`

[![JSON response object targetting its arrays of row-objects by dot-notation](../Images/a57a83ddd100c4ba8cbde049ed52bfce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L_1Gnj9B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5eg24as1z4ef62rep7r2.png)

感谢柯南赖澄清了这一点。