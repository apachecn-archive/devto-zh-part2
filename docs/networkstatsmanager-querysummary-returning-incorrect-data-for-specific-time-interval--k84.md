# NetworkStatsManager query summary 返回特定时间间隔的不正确数据

> 原文：<https://dev.to/simply005/networkstatsmanager-querysummary-returning-incorrect-data-for-specific-time-interval--k84>

我使用下面的代码来获取给定时间间隔内每个应用程序使用的数据

long t1 = system . current time millis()-(60 * 1000)；
long T2 = system . current time millis()；
network stats network stats = network stats manager . query summary(connectivity manager。TYPE_WIFI，null，t1，T2)；

long data _ tx = 0；
long data _ rx = 0；

网络状态。桶桶=新网络状态。bucket()；

while(network stats . hasnextbucket()){
network stats . getnextbucket(bucket)；
String calling app = context . getpackagemanager()。getNameForUid(bucket . getuid())；
data _ tx = bucket . gettxbytes()；
data _ rx = bucket . getrx bytes()；
}

但是，对于给定的时间间隔，任何给定应用程序的 bucket.getTxBytes()和 bucket.getRxBytes()的值都太大，这不可能是真的，因为我已经用 glasswire 数据监控应用程序检查过了。那么，这是获取每个应用程序在任何给定时间间隔内的数据使用情况的正确方法吗？我们如何实时监控应用程序以监控其互联网活动？