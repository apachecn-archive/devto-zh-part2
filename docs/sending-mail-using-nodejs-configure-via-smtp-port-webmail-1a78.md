# 使用 Nodejs 发送邮件，通过 SMTP 端口 WebMail 配置

> 原文：<https://dev.to/ishwar/sending-mail-using-nodejs-configure-via-smtp-port-webmail-1a78>

/*
Nodemailer 是 Node.js 应用程序的一个模块，可以轻松发送电子邮件。该项目始于 2010 年，当时没有发送电子邮件消息的合理选项

const node mailer = require(' node mailer ')；

/*
SMTP 是 Nodemailer 中传递消息的主要传输方式。SMTP 也是不同电子邮件主机之间使用的协议，所以它是真正通用的。几乎每个电子邮件发送提供商都支持基于 SMTP 的发送
*/

var SMTP transport = require(' node mailer-SMTP-transport ')；

var transporter = node mailer . create transport(smtpTransport({
TLS:{
}，
host:' '，
secureConnection: false，
port: 587，
auth: {
user:' '，
pass:' '
}
})；

var mailOptions = {
from: ' '，
to: ' '，
主题:'这是一个测试'，
正文:'你好这个测试消息'
}；

transporter . sendmail(mail options，function(error，info){
console.log("error，info "，错误，信息)；
})；

放入 tls 选项 rejectUnauthorized: false

/*
rejectUnauthorized:如果为 true，则根据提供的 ca 列表验证服务器证书。如果验证失败，将发出错误事件；err.code 包含 OpenSSL 错误代码。默认值:true。
*/