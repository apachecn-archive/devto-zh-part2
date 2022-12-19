# 允许在 Lightning 输入组件中有多个电子邮件地址

> 原文：<https://dev.to/ijason/allow-multiple-email-addresses-in-a-lightning-input-component-2mfk>

[lightning 输入组件](https://developer.salesforce.com/docs/component-library/bundle/lightning:input/example#lightningcomponentdemo:exampleInputEmail)内置了对电子邮件的支持，当你需要接受电子邮件地址作为输入时，这种支持非常有效。

```
<lightning:input aura:id="emailForm" label="To: " type="email"
                 name="toEmail"
                 value="{!v.toEmail}"
                 required="true"/> 
```

这将自动验证输入，以确保其格式正确。当您添加分号来添加多个电子邮件时，验证会失败。它只允许一个电子邮件输入。要解决这个问题，你不能使用电子邮件类型。您必须使用文本类型，并使用正则表达式自己进行验证，如:

```
<lightning:input aura:id="emailForm" label="To: " type="text"
                 name="toEmail"
                 value="{!v.toEmail}"
                 required="true"
                 messageWhenPatternMismatch="Please enter a valid email or valid emails separated by a semi-colon"
                 pattern="^(([a-zA-Z0-9_\-\.]+)@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.)|(([a-zA-Z0-9\-]+\.)+))([a-zA-Z]{2,4}|[0-9]{1,3})(\]?)(\s*;\s*|\s*$))*$" /> 
```

使用上面的代码片段，您现在可以在一个输入控件中接受多封电子邮件。它将正确地验证你是否使用一个单一的电子邮件或多个电子邮件。如果你能改进正则表达式，请在下面留下评论。

**控制器验证**

您可以使用以下代码片段进一步验证控制器中的输入:

```
var allValid = component.find('emailForm').reduce(function (validSoFar, inputCmp) {
     inputCmp.showHelpMessageIfInvalid();
     return validSoFar && inputCmp.get('v.validity').valid;
}, true);
if (allValid) {
    //proceed
} else {
    //display error
    component.set("v.error", 'Please update the invalid form entries and try again.');
} 
```