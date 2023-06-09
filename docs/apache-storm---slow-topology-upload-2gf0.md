# Apache Storm -慢速拓扑上传

> 原文：<https://dev.to/risdenk/apache-storm---slow-topology-upload-2gf0>

**注:**这是一个来自 notes 的老帖子。这可能不再适用，但如果分享对某人有帮助的话。

### 概述

[HDP 2.2 之后的阿帕奇风暴](https://storm.apache.org/)似乎很难处理大型拓扑 jar，需要一段时间才能上传。已经有[少数](https://mail-archives.apache.org/mod_mbox/storm-user/201603.mbox/%3CCAPC1M2i3OpKhC3n_+oTJke45Efuxq2PxMVurx71oEU-=Nqd9gQ@mail.gmail.com%3E)的[报告](https://community.hortonworks.com/questions/24517/topology%C2%ADcode%C2%ADdistribution%C2%ADtakes%C2%ADtoo%C2%ADmuch%C2%ADtime.html)的暴风拓扑坛子上传缓慢。几年前我遇到了这个。修复方法是增加`nimbus.thrift.max_buffer_size`设置。

### 搞定

将`nimbus.thrift.max_buffer_size`从默认的 1048576 增加到 20485760。

### 参考文献

*   [https://mail-archives . Apache . org/mod _ mbox/storm-user/2014 03 . mbox/% 3c fc 98 ee 12-4 aed-4d 06-9917-c 449 b 96 EB 08 a @ Gmail . com % 3E](https://mail-archives.apache.org/mod_mbox/storm-user/201403.mbox/%3CFC98EE12-4AED-4D06-9917-C449B96EB08A@gmail.com%3E)
*   [http://stack overflow . com/questions/27092653/storm-supervisor-connectivity-error-downloading-the-jar-from-nimbus](http://stackoverflow.com/questions/27092653/storm-supervisor-connectivity-error-downloading-the-jar-from-nimbus)
*   [https://qna list . com/questions/4768442/nimbus-fails-after-uploading-topology-reading-too-large-of-frame-size](https://qnalist.com/questions/4768442/nimbus-fails-after-uploading-topology-reading-too-large-of-frame-size)