# 与您的亚马逊订单历史有趣

> 原文：<https://dev.to/btbytes/fun-with-your-amazon-order-history-jdg>

## 查询亚马逊购买数据

使用此处的[说明创建订单历史报告](https://www.amazon.com/gp/help/customer/display.html?nodeId=200131240)

将 CSV 加载到 [SQLite](https://www.sqlite.org/index.html)

```
sqlite> .mode csv
sqlite> .import ./01-Jan-2009_to_11-Sep-2018.csv orders 
```

Enter fullscreen mode Exit fullscreen mode

表格模式

```
sqlite> .sch
CREATE TABLE orders(
  "Order Date" TEXT,
  "Order ID" TEXT,
  "Title" TEXT,
  "Category" TEXT,
  "ASIN/ISBN" TEXT,
  "UNSPSC Code" TEXT,
  "Website" TEXT,
  "Release Date" TEXT,
  "Condition" TEXT,
  "Seller" TEXT,
  "Seller Credentials" TEXT,
  "List Price Per Unit" TEXT,
  "Purchase Price Per Unit" TEXT,
  "Quantity" TEXT,
  "Payment Instrument Type" TEXT,
  "Purchase Order Number" TEXT,
  "PO Line Number" TEXT,
  "Ordering Customer Email" TEXT,
  "Shipment Date" TEXT,
  "Shipping Address Name" TEXT,
  "Shipping Address Street 1" TEXT,
  "Shipping Address Street 2" TEXT,
  "Shipping Address City" TEXT,
  "Shipping Address State" TEXT,
  "Shipping Address Zip" TEXT,
  "Order Status" TEXT,
  "Carrier Name & Tracking Number" TEXT,
  "Item Subtotal" TEXT,
  "Item Subtotal Tax" TEXT,
  "Item Total" TEXT,
  "Tax Exemption Applied" TEXT,
  "Tax Exemption Type" TEXT,
  "Exemption Opt-Out" TEXT,
  "Buyer Name" TEXT,
  "Currency" TEXT,
  "Group Name" TEXT
); 
```

Enter fullscreen mode Exit fullscreen mode

对所有书籍的简单查询(ISBNs 不是以字母`B`开头):

```
sqlite> select "Order Date", "Title", "ASIN/ISBN", "Purchase Price Per Unit" from orders where "ASIN/ISBN" NOT LIKE "B%"; 
```

Enter fullscreen mode Exit fullscreen mode

买书的总花费:

```
sqlite> select SUM(CAST("QUANTITY" AS DECIMAL) * CAST(SUBSTR("Purchase Price Per Unit",2) AS DECIMAL)) from orders where "ASIN/ISBN" NOT LIKE "B%"; 
```

Enter fullscreen mode Exit fullscreen mode