# 使用谷歌表单创建网站

> 原文：<https://dev.to/gauthamzz/create-websites-using-google-forms-1o59>

*改变谷歌表单的 CSS，不用代码创建网站*

> 最好的代码是根本没有代码

### **第一步**

导航到 [google forms](https://www.google.com/forms/about/) 并创建一个包含必填字段的表单。

### **第二步**

创建一个 html 表单，使用 google 表单中提到的相同字段，这样您就可以在其中连接您的 google 表单。

### **第三步**

导航回您创建的 google 表单，并检查 Google 表单的 action 属性。

[![](img/199418c6352b2ca9b74db019197b8b05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wWG_OQey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/981/1%2AfLHT-AFfskKBOtWB2fgw-Q.png)

### **第三步(一)**

将相同的动作复制到 html 表单中。

[![](img/f477a7424a0a92867c940dfa1d2e808b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UElEOUt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWHbQ-ruNSP6XWoZ8it_aeA.png)

### **第四步**

检查并查找谷歌表单中提到的每个字段的属性名称值。

[![](img/dfaa810182ba4a6141549fba131adf27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b_mBBkOy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7GwU6eNxyMaJSUMwgmA3lA.png)

### **第四步(一)**

为您的 html 表单项提供相同的名称值。这些值看起来有点像 entry。36860 . 68686868686

使用标记将该值放入 html 表单字段中。

### **第五步**

在 html 表单中填写一个测试输入，并验证您的 google 表单是否得到了响应。

既然是本地人

element, apply CSS however you want to :)

嵌入了 google 表单的 html 表单示例。

```
<form action=”https://docs.google.com/forms/u/1/d/edjffbsffefEg/formResponse" method=”POST” id=”contact\_form” name=”” class=”feedback-form”>

<input class=”form\_\_email” type=”text” placeholder=”Name” name=”entry.1432404007" id=”name\_input” required=”” />

<textarea class=”form\_\_message” cols=”30" type=”text” placeholder=”complaint” name=”entry.1580494895" id=”email\_input” required=”” rows=”5"></textarea>

<input class=”form\_\_email” type=”text” placeholder=”hostel” name=”entry.178242508" id=”telephone\_input” required=”” />

<input class=”form\_\_email” type=”text” placeholder=”room number” name=”entry.791188540" id=”email\_input” required=”” />

<button class=”form\_\_submit”>Submit</button>

</form> 
```

与谷歌表单链接的样本表单。

[![](img/9afb47f2c4b399ca978d13edad9a345a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QhsxZ88i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/688/1%2ABwbXKHEZQH_4Kpczesmjww.png)

### 接下来呢

您可以使用 zapier 与 Airtable 的集成，并创建一个平台。[https://zapier.com/apps/airtable/integrations/google-sheets](https://zapier.com/apps/airtable/integrations/google-sheets)

* * *