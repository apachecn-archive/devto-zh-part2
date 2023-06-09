# 每年重新启动生成独立代码

> [https://dev . to/juananuiz/gener-cdigo-autonomic-con-restart-annual-42n 6](https://dev.to/juananruiz/generar-cdigo-autonumrico-con-reinicio-anual-42n6)

我始终建议关系数据库中的表具有整数类型的独立主键字段，该字段应完全独立于包含该表的任何其他字段以及属于的任何其他字段尽管任何其他数据，例如 DNI、车辆登记或发票代码，看起来都是一成不变的，但最好有一个额外的密钥，在数据库中独立地识别该记录，这与□无关

有时，除了此字段之外，您还需要生成一个单独的密钥，该密钥部分加密，部分自行生成。一个典型的例子通常是发票代码。大多数公司使用某种显示年份的代码和一个在一年中不断增加的数字，以便在下一年恢复为零。

想象一下，您必须生成一张发票，该发票的编号如下所示:□您将看到如何生成一个 SQL 查询来查找一年中最高的发票代码并返回下一个发票代码。如果这是一年中的第一张发票，或者您可以要求他提供任何特定年份的下一张发票号码，那么我还能返回正确的号码就太好了。问得不多吗？

假设发票表将发票编号存储在一个字段中，并且还具有一个字段来存储发票签发日期。为简化查询，我们现在将生成本年度的发票。

```
SELECT CONCAT('F-',LPAD(SUBSTR(codigo,3,4)+1,4,'0'), '/',   
 YEAR(NOW())) AS codigo   
 FROM factura WHERE YEAR(fecha_emision) = YEAR(NOW())  
UNION  
SELECT CONCAT('F-0001', '/', YEAR(NOW())) AS codigo  
ORDER BY codigo DESC LIMIT 1; 
```

Enter fullscreen mode Exit fullscreen mode

第一次查询，在 **UNION** 之前，查找今年的最高代码，并通过加上≤并在左侧填入零将其返回给你。

如果今年还没有记录的话，那么这次的第一次查询什么也没有传回，但是第二部分，在 **UNION** 之后来救援，产生当年的第一张发票。

如果需要生成前几年的记录，只需通过将年作为参数来修改此查询，而不必使用短语“**year(now())]**

这样做的另一个类似方法是使用这个不使用 unionT2】的查询

```
SELECT CASE WHEN COUNT(*) > 0 THEN   
CONCAT('F-',LPAD(SUBSTR(codigo,3,4)+1,4,'0'), '/', YEAR(NOW()))  
ELSE   
CONCAT('F-0001', '/', YEAR(NOW()))  
END AS codigo   
FROM factura WHERE YEAR(fecha_emision) = YEAR(NOW()) 
ORDER BY codigo DESC LIMIT 1; 
```

Enter fullscreen mode Exit fullscreen mode

### 从你的 PHP 代码

您可以先从 PHP 执行此查询，然后再储存完整的记录。或者，更好的是，如果您需要填写表单中的任何内容，请在 PHP 脚本中运行此程序，该脚本将在表单上绘制表单，并将其作为表单本身的打印输出。然后与其他字段一起发送以写入数据库。

```
<?php  
global $dbh;  

$query = $dbh->prepare(  
    "SELECT CONCAT('F-',LPAD(SUBSTR(codigo,3,4)+1,4,'0'), '/',   
                YEAR(NOW())) AS codigo   
                FROM factura WHERE year(fecha_emision) = YEAR(NOW())  
              UNION  
                SELECT CONCAT('F-0001', '/', YEAR(NOW())) AS codigo  
              ORDER BY codigo DESC LIMIT 1;  
    ");  
// Establece la forma de devolver los resultados, en este caso devolverá un array asociativo 
$query->setFetchMode(PDO::FETCH_ASSOC);  
$query->execute();  

echo "<!DOCTYPE html><html lang='en'><head><meta charset='UTF-8'></head><body>";  
while ($row = $query->fetch()){  
    echo '<form method="post" action="index.php?p=factura_grabar">';  
    echo '<input type="text" name="codigo" value="' . $row["codigo"] . '">';  
    echo '<input type="date" name="fecha_emision">';  
    echo '<input type="submit" value="Grabar factura">';  
    echo '</form>';  
}  
echo "</body></html>"; 
```

Enter fullscreen mode Exit fullscreen mode

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【galar】](https://github.com/galatar)/[【生成-自动编码-年度重启】](https://github.com/galatar/generar-codigo-autonumerico-con-reinicio-anual)

### 教程代码:生成每年重新启动的独立代码

<article class="markdown-body entry-content container-lg" itemprop="text">

# 每年重新启动生成独立代码

教程代码[【http://galatar . com/generate-code-autonomial-con-restart-annual】](http://galatar.com/generar-codigo-autonumerico-con-reinicio-anual)

</article>

[View on GitHub](https://github.com/galatar/generar-codigo-autonumerico-con-reinicio-anual)