# Aspose 简介。CAD，第 2 部分

> 原文：<https://dev.to/nnevod/introduction-to-asposecad-part-2-2kgf>

如果你愿意使用完整版的[作为主题。CAD](https://products.aspose.com/cad/) API，您需要在使用 Aspose 的软件中应用您的许可证。CAD 库。在本文中，我将展示如何做到这一点。很简单！

有两种类型的许可证，一种是预付的传统许可证文件，另一种是按使用付费的服务密钥许可证，称为计量许可证。让我们再深入一点。

## 许可文件

这个实现起来非常简单。只需在项目中的某个地方包含这些行:

```
 License license = new Aspose.CAD.License();
            // Pass path to license file
            license.SetLicense("Aspose.CAD.lic"); 
```

Enter fullscreen mode Exit fullscreen mode

在执行完这几行之后，你的 Aspose。CAD 库将在许可模式下工作，没有文件复杂性限制，没有水印。因此，您可能希望在 Aspose.CAD 的任何其他使用之前，将这些行放在软件的启动代码中的某个位置。SetLicense 也接受 [FileStream](https://msdn.microsoft.com/en-us/library/system.io.filestream(v=vs.110).aspx) ，因此您可以在独立于许可证设置的代码中执行 IO。

## 计量许可

这是一个测量 Aspose 使用量的许可证。CAD libary，并与 Aspose 许可服务同步以按使用计费。它还可以让您测量注册的使用量。一切都是通过一个静态[计量](https://apireference.aspose.com/net/cad/aspose.cad/metered)类来完成的看看:

```
 // Set public and private keys in Metered class
            Aspose.CAD.Metered.SetMeteredKey("*****", "*****");

            // Get metered data amount before calling API
            decimal amountbefore = Aspose.CAD.Metered.GetConsumptionQuantity();

            // Display information
            Console.WriteLine("Amount Consumed Before: " + amountbefore.ToString());

            // Do processing
            //Aspose.CAD.FileFormats.Cad.CadImage image = (Aspose.CAD.FileFormats.Cad.CadImage)Aspose.CAD.Image.load("BlockRefDgn.dwg");

            // Get metered data amount After calling API
            decimal amountafter = Aspose.CAD.Metered.GetConsumptionQuantity();

            // Display information
            Console.WriteLine("Amount Consumed After: " + amountafter.ToString()); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它使用起来同样简单。

目前就这些，敬请关注！

如需更多示例，请访问[网站。CAD GitHub](https://github.com/aspose-cad) 页面。Aspose.CAD 上还有 [Twitter](https://twitter.com/Asposecad) 和[脸书](https://www.facebook.com/AsposeCAD)的新闻页面