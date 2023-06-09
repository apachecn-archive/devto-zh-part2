# Excel 与 LAMP 服务器之间的数据交换

> 原文：<https://dev.to/abbrevia/data-exchange-between-excel-and-a-lamp-server-1k27>

# 前提

自 2011 年以来，我公司收集的数据存储在 Excel 电子表格中并进行管理。该公司已经发展壮大，现在数据在全国不同地区的三个办公室进行交换。

自 2013 年以来，采用了基于 VPN 的解决方案。

自 2017 年以来，Excel 宏已在 VB NET 应用程序中进行了转换。

不是他们要求实现一个 web 应用程序来管理数据。

# 如何喂野兽(web 应用)

## 最优解

最终的解决方案将是一个 web 服务，它交换来自收集客户机数据的 web 应用程序和存储并管理数据的新应用程序的数据。

## 每次插入/更新 Excel 文件时调用 web 服务

在集成还没有准备好的时候，该做些什么呢？

VB NET 应用程序调用 web 服务来插入和更新数据。

创建 REST web 服务很简单。

```
 Dim md5 As System.Security.Cryptography.MD5 = System.Security.Cryptography.MD5.Create()
        Dim request As System.Net.HttpWebRequest = System.Net.WebRequest.Create(Me.gatUrl & "?token=" & Me.GetMd5Hash(md5, token))
        request.AutomaticDecompression = System.Net.DecompressionMethods.Deflate Or Net.DecompressionMethods.GZip
        request.Method = "POST"
        request.ContentType = "application/x-www-form-urlencoded"
        request.Timeout = 5000

        Dim dataStream As IO.Stream = request.GetRequestStream()
        Dim postData As String = Newtonsoft.Json.JsonConvert.SerializeObject(rigaRichiesta)
        Dim enc As System.Text.UTF8Encoding = New System.Text.UTF8Encoding()
        Dim byteArray As Byte() = enc.GetBytes(postData)
        Using stream = request.GetRequestStream()
            stream.Write(byteArray, 0, byteArray.Length)
        End Using
        Dim responseString As String
        Try
            Dim result As System.Net.HttpWebResponse = request.GetResponse()
            Dim reader As New System.IO.StreamReader(result.GetResponseStream())
            responseString = reader.ReadToEnd()
            result.Close()
        Catch ex As Exception
            Me.Log.Text = "Errore nella chimata a " + Me.gatUrl + " " + ex.Message
            Me.Log.BackColor = Drawing.Color.Red

            Exit Sub
        End Try 
```

解决方案很慢:4k 行需要 10 分钟。

## 调用 Excel 中的 web 服务关闭传递所有数据

另一个解决方案是:

*   将 Excel 数据保存为 CSV 格式；
*   将 CSV 文件作为 REST 调用的内容传递。

```
 Dim currentWorkbook As String = Me.workbook.FullName
        Dim currentFormat As Long = Me.workbook.FileFormat

        Dim nomeFile = currentWorkbook + ".csv"
        Me.foglioRichieste.SaveAs(nomeFile, Microsoft.Office.Interop.Excel.XlFileFormat.xlCSV)
        Me.foglioRichieste.SaveAs(currentWorkbook, currentFormat)

        Dim contenuto As String = System.IO.File.ReadAllText(nomeFile)

        Dim dt As DateTime = DateTime.Now
        Dim token As String = Me.token & dt.Year & dt.Month.ToString.PadLeft(2, "0") & dt.Day.ToString.PadLeft(2, "0")

        Dim md5 As System.Security.Cryptography.MD5 = System.Security.Cryptography.MD5.Create()
        Dim request As System.Net.HttpWebRequest = System.Net.WebRequest.Create(Me.gatUrl & "?azione=file&token=" & Me.GetMd5Hash(md5, token))
        request.AutomaticDecompression = System.Net.DecompressionMethods.Deflate Or Net.DecompressionMethods.GZip
        request.Method = "POST"
        request.ContentType = "application/x-www-form-urlencoded"
        request.Timeout = 50000

        Dim dataStream As IO.Stream = request.GetRequestStream()

        Dim enc As System.Text.UTF8Encoding = New System.Text.UTF8Encoding()
        Dim byteArray As Byte() = enc.GetBytes(contenuto)
        Using stream = request.GetRequestStream()
            stream.Write(byteArray, 0, byteArray.Length)
        End Using
        Dim responseString As String
        Try
            Dim result As System.Net.HttpWebResponse = request.GetResponse()
            Dim reader As New System.IO.StreamReader(result.GetResponseStream())
            responseString = reader.ReadToEnd()
            result.Close()
        Catch ex As Exception
            Me.Log.Text = "Errore nella chimata a " + Me.gatUrl + " " + ex.Message
            Me.Log.BackColor = Drawing.Color.Red

            Exit Sub
End Try 
```

在 2 秒钟内，服务器中的数据已经更新。

额外的技巧:使用 Visual Basic 将 Excel 文件保存为 CSV 格式会产生一个高质量的文件，UTF8 编码，并使用彗形符作为分隔符。