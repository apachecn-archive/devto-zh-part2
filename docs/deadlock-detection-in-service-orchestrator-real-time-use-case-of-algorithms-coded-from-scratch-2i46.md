# 服务编排器中的死锁检测:从头开始编码的算法的实时用例

> 原文：<https://dev.to/venkteshv/deadlock-detection-in-service-orchestrator-real-time-use-case-of-algorithms-coded-from-scratch-2i46>

我在计算机科学学士学位的本科课程中有两门课程:数据结构和应用-1，在接下来的一个学期中有一门课程名为数据结构和应用-2。我们有一个非常好的教授，我们非常喜欢这门课程以及相关的实验室和迷你项目。规定的教科书也很好，内容也很清楚。出现上述序言的原因是，一旦我毕业并进入这个行业，我发现这些复杂的数据结构的使用，甚至像 tries(折扣数组)这样的基本数据结构的使用都不令人满意或为零。这让我很沮丧，因为我曾希望在产品公司工作意味着处理大量数据和构建高效的数据结构(对不起！)比如 linkedin 如何使用 Bloom filters 来避免不必要的数据库命中(如果存在的话),或者比如它们如何被用于垃圾邮件过滤等。但我也意识到我有点误解了。当然，许多所谓的产品公司，除了那些真正的技术娴熟的公司，并不太关心创新，所有的开发者都使用数据结构，只是它可能被抽象成一个库的形式或隐藏在普通的视线中。为了有效地理解数据库查询，你应该知道 B 树。如果你想知道学习二分图有什么用，一个具体的例子是给老师分配班级(试着猜猜这个应用程序中的用例)，我可以继续，但我希望你明白这一点。

那么为什么会有这么长的介绍呢？这样我就能得到一个冗长的帖子？认识到核心计算机科学课程及其应用的重要性。好了，现在让我们进入正题。

这篇文章的目的是解释我实现的死锁检测器，该检测器用于检测配置文件中的循环依赖关系，该配置文件指定要调用的提供给 orchestrator 的服务(当然，在真正的微服务架构中，您不会引入单点故障，而只是考虑这个用例)。

我发布的 npm 包的链接是:[https://www.npmjs.com/package/deadlock-detector](https://www.npmjs.com/package/deadlock-detector)

现在来看代码和解释。我用来检测对服务的循环调用的方法是一种递归深度优先图着色算法，其中节点被标记为“正在访问”或“已访问”。如果在访问一个节点时，您发现它已经处于“正在访问”状态，您就有了一个循环。可以跳过标记为“已访问”的节点。

`const states = {
Visited : 'Visited',
Visiting: 'Visiting',
NotVisited: 'NotVisited'
};`

一个节点可以有三种状态。如果已经访问过，则可以将其标记为已访问。或者访问节点是否正在被访问(有助于检测循环)或者默认状态(未被访问)。

```
const Equal = (parents,node) => {   
    var result = false;
    _.forEach(parents,(parent) => {

            result =   _.isEqual(parent,node) || result;
    })
    return result;}

` const depthFirstSearch = (services,node,edges,parents,visited,cycles) => {
    var state = visited[node] || '';
    if (state == states.Visited)
    return;
    else if(state == states.Visiting)
    {      
        if(Equal(parents,node)){      
        cycles.push(_.concat(parents,node));
    }
    }
    else
    {   
        visited[node] = states.Visiting;
        parents.push(node);
        _.forEach(services[node],(child) => {
            depthFirstSearch(services,child, edges, parents, visited, cycles);

        });

        parents.splice(parents.length - 1,1);

        visited[node] = states.Visited;
    }
}`

 `const findCycles = (services) => {
    const nodes = Object.keys(services);
    var visited = {};
    var parents = new Array();
    var cycles = new Array();
    _.forEach(nodes,(node) => {
        const edges = services[node];
        depthFirstSearch(services,node,edges,parents,visited,cycles);
    })
    return cycles;
};
module.exports=findCycles;`

Example Usage:  Input is specified in following format.Use the following snippet of code to test the above package I have published.

`const findCycles = require('deadlock-detector'); 
const services = { 
"A": ["A"], 
"L":["M","N"], 
"N":["L"], 
"B":["C","D"], 
"D":["E"], 
"E":["F","Q"],
 "F":["D"] };
 const cycles = findCycles(services);
 console.log(cycles);

In the above example  cycles are [ [ 'A', 'A' ], [ 'L', 'N', 'L' ], [ 'B', 'D', 'E', 'F', 'D' ] ].` 
```

以下是上述方法的流程。

Findcycles 初始化一个空的父数组和一个保存输出(检测到的循环)的数组，并对输入进行迭代，为每个节点调用深度优先搜索。

在深度优先搜索中，我们检查各种状态(颜色)。如果该节点已经被访问过，我们什么也不做，如果它处于访问状态，则返回，然后保持可疑状态，我们检查它是否与标记为父节点的任何节点相同，如果是，则通过连接父节点(在该节点之前标记为“正在访问”的节点)和节点来形成循环链，并且报告循环。当浏览完一个节点的所有子节点后，该节点将被标记为已访问。通过在本地克隆库来调试代码，以实现清晰的可视化。

结论:
就是这样，如果你需要一个 c#实现，请随意发表评论。欢迎提出改进国家预防机制一揽子方案的建议。