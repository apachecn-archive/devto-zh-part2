# 马来西亚🇲🇾吉隆坡

> 原文：<https://dev.to/commonshost/kuala-lumpur-malaysia-15dn>

这个周末，一个公共主机小羊 Mk I 服务器被部署在吉隆坡，以更好地服务马来西亚的互联网用户。总体来说，这是第三个 Commons Host point of presence (PoP ),也是第一个位于我的家乡新加坡以外的地方。

[![Telekom Tower](img/f6ac10854cbd373187e2f9887bd12a42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---lE-dB5L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ya1mdsp8jmsshxlc68o.jpg) 
图片:马来西亚吉隆坡的电信大楼(来源于维基百科上的[reaper spectre](http://wikimapia.org/28233/Menara-TM#/photo/1742434)

像大多数亚洲城市一样，吉隆坡也有几家光纤 ISP，以非常合理的价格为住宅和中小企业客户提供服务。在商用光纤连接上部署低成本的 CDN 边缘服务器是世界上许多地方昂贵或不存在的企业级数据中心的新替代方案。

KL 的部署从早上 7 点开始，从新加坡骑了 4 个小时的摩托车来亲手交付微型服务器。这是小羊羔无意的振动测试。幸运的是，她完全是固态硬件，并成功通过。

[![Seb passed out on couch with supportive friends](img/fe998127b25c7e18c844b657f682f6b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JX8EDDdK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lq6wzynjy93qj0wbz7xf.jpg) 
图:CDN 边缘服务器部署成功后，Seb 立即昏倒在沙发上。感谢好朋友们的支持。

使用 Ansible 实现部署过程[的自动化是一项正在进行的工作。手动设置服务器需要几个小时。使用](https://gitlab.com/commonshost/ansible/issues) [Constellix](https://constellix.com) 的 GeoDNS 配置现在将来自马来西亚访问者的所有流量路由到吉隆坡 PoP，两个预先存在的新加坡 PoP 作为故障转移。星期天傍晚，工作完成了。感谢在精神上和身体上支持我的好朋友们的陪伴。

[![Photo: Seb presenting a brown bag lunch session at NEXT Academy Kuala Lumpur](img/7cccd69398a9a7803f635cd8d5647685.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IpJs6BVI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1hz4u3ltds983735prz.jpg)

周一，在 NEXT Academy Kuala Lumpur 上，我做了一个两分钟的*午餐*演讲，之后是观众 Q&a .许多来自敏锐的学生、导师和讲师的好问题。都是关于自由和开源软件项目的技术方面和可持续商业模式。

希望在此次部署中获得的经验教训有助于在全球范围内推广更多部署。