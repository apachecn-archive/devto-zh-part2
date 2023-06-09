# 关于少数民族代表数据的注释(以及美国以外的相关性)

> 原文：<https://dev.to/icyapril/notes-on-ethnic-minority-representation-data-and-relevance-outside-the-us-1p4a>

我最近与一位朋友交谈，他正考虑使用他们公司美国总部的少数族裔数据和分类，为他们在曼彻斯特和伦敦的英国分支机构设定目标。我想分享一些数据，说明为什么我认为使用另一个地区的数据是一个坏主意，以防其他人面临类似的困境，或者已经这样做了，但没有意识到这样做的负面影响。

我很想听听你对此的想法，如果你认为我得出了错误的结论。

对于从事数据处理或机器学习的人来说，你可能听过“垃圾进，垃圾出”这句话。不管给定算法的强度如何；如果你提供有问题的数据，你会得到有问题的预测。我认为同样的情况也适用于使用美国多元化数据来推动多元化。

科技公司多样性报告通常包括类似分类的群体和数据:“亚洲人”、“黑人”、“拉丁人”、“美国土著人”、“混血人”和“白人”。例如，参见下图【2018 年优步的多样性数据:

[![Uber - Global Gender and US Race/Ethnicity](img/7132775df6b13cc4307794e263b13eae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6cQLRgGX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ubernewsroomapi.10upcdn.com/wp-content/uploads/2018/04/Screen-Shot-2018-04-23-at-8.38.27-PM.png)

总部位于旧金山的科技公司通常只提供美国的种族多样性数据，而不提供其海外办事处的数据。在某些方面，英国政府的某些部门也采用了类似的分类形式。比如说；下面的"[平均每小时工资(按种族划分)- 2017 年](https://www.ethnicity-facts-figures.service.gov.uk/work-pay-and-benefits/pay-and-income/average-hourly-pay/latest)"图表分为 5 个少数民族群体，但更偏向少数民族群体。这里值得注意的是，这两个少数民族群体占据了最高(印度人和中国人)和最低(巴基斯坦人/孟加拉人)——在硅谷的衡量标准中通常被归为“亚洲人”。

[![Average Pay per Hour (£) by Ethnicity - 2017](img/b815b60f3fb3316584e631c505d9ba2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6v7zjuW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tsxwdb9cb8loaxt1xpeq.png)

一个更详细的指标是按种族划分的 GCSE 成绩(衡量 16 岁学生成绩的学校资格)。在这个群体中，“吉普赛/罗姆白人”和“爱尔兰白人旅行者”都是 T2 人，排在最后。紧随其后的是各种“黑人”群体，但巴基斯坦儿童的表现不如来自“黑人”和“非洲黑人”类别的儿童。中国和印度的孩子有最高的成就。

[![% British Pupils Achieving A* to C in English and Maths GCSE - 2017](img/d36badda8f6566af983ac77cf2341735.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mwiiGSIZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbjvyru4fl5bqnlornlf.png)

请注意，这些指标与美国不可比；根据美国人口普查局的数据，巴基斯坦裔美国人的平均家庭收入是 62，848 美元，而黑人或非裔美国人[的平均家庭收入是 38，555 美元。尽管美国和英国的语言有些相似，但这两个地区的多样性数据是不可比的。](https://www.webcitation.org/6gpGlyhlr?url=http://factfinder.census.gov/faces/tableservices/jsf/pages/productview.xhtml?src=bkmk)

总结；我倾向于相信，庞大而不准确的民族群体有进一步削弱特定群体代表性的风险。

感谢[ethnicity-facts-figures.service.gov.uk](https://www.ethnicity-facts-figures.service.gov.uk)在此提供大部分数据。