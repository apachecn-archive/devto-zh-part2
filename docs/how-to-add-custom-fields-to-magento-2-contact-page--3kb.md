# 如何将自定义字段添加到 Magento 2 联系页面

> 原文：<https://dev.to/suthanalley/how-to-add-custom-fields-to-magento-2-contact-page--3kb>

首先创建一个模块

在 app/code/magentic ians/module contact/etc 中创建 module.xml 并放入代码:

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
<module name="Magenticians_Modulecontact" setup_version="1.0.1">
</module>
</config> 
```

Enter fullscreen mode Exit fullscreen mode

对于模块注册，在 app/code/magentic ians/module contact 中创建 registration.php，并放入代码:

```
<?php

\Magento\Framework\Component\ComponentRegistrar::register(
\Magento\Framework\Component\ComponentRegistrar::MODULE,
'Magenticians_Modulecontact',
__DIR__
); 
```

Enter fullscreen mode Exit fullscreen mode

现在在 Magento 2 联系人表单中覆盖并添加新字段。

从供应商/magento/module-contact/view/frontend/templates 中复制 form.phtml 文件，并粘贴到 app/code/magentic ians/module contact/view/frontend/templates 中。
要向联系人表单添加新字段，请打开 form.phtml 文件并添加以下代码:

```
<div class="field subject required">
             <label class="label" for="subject"><span><?php /* @escapeNotVerified */ echo __('Subject') ?></span></label>
             <div class="control">
                  <input name="subject" id="subject" title="<?php /* @escapeNotVerified */ echo __('Subject') ?>" value="" class="input-text" type="text" data-validate="{required:true}"/>
             </div>
        </div> 
```

Enter fullscreen mode Exit fullscreen mode

完整指南:[https://magen ticians . com/add-custom-field-in-magento-2-contact-page/](https://magenticians.com/add-custom-field-in-magento-2-contact-page/)