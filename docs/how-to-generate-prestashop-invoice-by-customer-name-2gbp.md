# 如何根据客户名称生成售前发票

> 原文：<https://dev.to/franken/how-to-generate-prestashop-invoice-by-customer-name-2gbp>

[![](../Images/3def0eb6bfd1466eabc2c257b37de9ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GtxXRVRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.com/images/stories/generate-prestashop-invoice-by-customer-name.jpg)

## 想按客户名称而不是枯燥的数字生成发票？

这很简单——在这个 Prestashop 教程中，我将向你展示如何去做。

*   第一步:修改 your-website/classes/pdf/html template invoice . PHP 中的函数 getFilename()。
*   第二步:通过添加这一行得到客户对象:$ Customer = new Customer((int)$ this-> order-> id _ Customer)；
*   第三步:返回姓名和号码。

你准备好了吗？跟我来。

在你做任何事情之前，请确保先备份你的网站/文件。你做完了，我们走。

## I -修改函数 getFilename()

使用您最喜欢的编辑器打开并编辑您的-website/classes/pdf/html template invoice . PHP 中的函数 getFilename()。

## II -获取客户对象。

### 查找 getFilename()函数(第 500 行):

公共函数 getFilename()
{
$ id _ lang = Context::get Context()->language->id；
$ id _ shop =(int)$ this->order->id _ shop；
$ format = ' % 1 $ s % 2 $ 06d '；

将其更改为:

公共函数 get filename()
{
$ id _ lang = Context::get Context()->language->id；
$ id _ shop =(int)$ this->order->id _ shop；
$ format = ' % 1 $ s % 2 $ 06d '；
$customer =新客户((int)$ this->order->id _ Customer)；

## III -返回姓名和号码。

### 看这段代码:

返回 sprintf(
$format，
Configuration::get(' PS _ INVOICE _ PREFIX '，$id_lang，null，$id_shop)，
$ this->order _ INVOICE->number，
date('Y '，strtotime($ this->order _ INVOICE->date _ add))
。。' pdf '；

将其更改为:

返回 sprintf(
$format，
Configuration::get(' PS _ INVOICE _ PREFIX '，$id_lang，null，$id_shop)，
$ this->order _ INVOICE->number，
date('Y '，strtotime($ this->order _ INVOICE->date _ add))【T4)。*’。$customer- >名字。*’。$customer- >姓氏。。' pdf '；

## 决赛-享受你的结果

发票名称将为 IN_number_customername.pdf。例如:IN000001_Natalia_Eva.pdf

很酷吧？别忘了我们的博客，找到有用的 [Prestashop 教程](https://www.prestasoo.com/Blog/?utm_source=devto&utm_medium=article&utm_campaign=invoice_customer_name)。

周末愉快！