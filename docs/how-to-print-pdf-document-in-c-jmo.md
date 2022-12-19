# 如何用 C#打印 PDF 文档

> 原文：<https://dev.to/eiceblue/how-to-print-pdf-document-in-c-jmo>

Spire.PDF 有强大的打印 PDF 文档的功能。本文展示了如何通过以下打印情况使用 C#打印 PDF 文件:

*   将 PDF 打印到默认打印机。
*   将 PDF 打印到虚拟打印机。
*   将 PDF 打印到指定的打印机，并在 PDF 文件中选择一些页面进行打印。
*   以灰度打印 PDF。
*   每张纸上打印多个 PDF 页面
*   将单个 PDF 页面打印到多张纸上

将 PDF 打印到默认打印机，并打印 PDF 文档中的所有页面。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
pdf.Print(); 
```

将 PDF 打印到虚拟打印机(Microsoft XPS Document Writer)。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
pdf.PrintSettings.PrinterName = "Microsoft XPS Document Writer";   
pdf.PrintSettings.PrintToFile("PrintToXps.xps");
pdf.Print(); 
```

将 PDF 打印到指定的打印机，并在 PDF 文件中选择一些页面进行打印。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
//Set the printer 
pdf.PrintSettings.PrinterName = "HP LasterJet P1007";
//Only print the second and fourth page
pdf.PrintSettings.SelectSomePages(new int[] { 2,4 });
//Print the pages from 1 to 15
pdf.PrintSettings.SelectPageRange(1,15);
pdf.Print(); 
```

以灰度打印 PDF。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
pdf.PrintSettings.Color = false;
pdf.Print(); 
```

每张纸上打印多个 PDF 页面。用户可以调用 PdfPrintSettings 类的 SelectMultiPageLayout(int rows，int columns)方法在每张纸上打印多个 PDF 页面。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
//Print two PDF pages per sheet
pdf.PrintSettings.SelectMultiPageLayout(1, 2);
pdf.Print(); 
```

将单个 PDF 页面打印到多张纸上。将大型 PDF 页面打印到多张纸上时，可以使用 PdfPrintSettings 类的 SelectSplitPageLayout()方法。此方法根据标准 A4 纸张尺寸:595pt*842pt，将 PDF 页面分割成多页。

```
PdfDocument pdf = new PdfDocument();
pdf.LoadFromFile("Sample.pdf");
//Print the PDF page to multiple sheets
pdf.PrintSettings.SelectSplitPageLayout();
pdf.Print(); 
```