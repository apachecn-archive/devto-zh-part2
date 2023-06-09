# Apache Solr - Apache 方解石 Avatica 集成

> 原文：<https://dev.to/risdenk/apache-solr---apache-calcite-avatica-integration-3hjb>

### 概述

Apache Solr 是一个基于 [Apache Lucene](https://lucene.apache.org/solr/) 的全文搜索引擎。Apache Solr 的功能之一是处理类似 SQL 的语句。这是在 Solr 6.0 中引入的，并在后续版本中进行了改进。最初，SQL 支持使用了 [Presto SQL 解析器](https://github.com/prestodb/presto/blob/master/presto-parser/src/main/java/com/facebook/presto/sql/parser/SqlParser.java)。由于 Presto 没有优化器，它被 Apache 方解石所取代。方解石提供了将 SQL 的执行下推到 Apache Solr 的能力。

Apache 方解石 Avatica 是 Apache 方解石的子项目，提供 JDBC 驱动程序和 JDBC 服务器。Avatica 架构图展示了这两者是如何结合在一起的。

[![](img/b5e9eb19632b45851353c377be415bdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--trGQvjnq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/julianhyde/share/master/slides/avatica-architecture.png)

### 阿帕奇 Solr 和阿帕奇方解石 Avatica

Apache Solr 在历史上构建了自己的 JDBC 驱动程序实现。这需要相当多的努力，因为 JDBC 规范有很多方法需要实现。SOLR-9963 是为了尝试将 Apache 方解石 Avatica 集成到 SOLR 中而创建的。这将为 Avatica JDBC 驱动程序提供一个端点，并且不再需要单独的 Apache Solr JDBC 驱动程序实现。

### 将 Apache 方解石 Avatica 集成为 Apache Solr 处理程序

因为 Apache 方解石 Avatica 是在 Jetty 中实现的，就像 Apache Solr 一样，所以我想在 Solr 中添加 Avatica 作为另一个处理程序。这将暴露 Avatica 的所有特性，而不改变 Solr 的任何内部特性。然后，Avatica 处理程序可以使用 Apache Solr 中现有的方解石引擎来处理查询。

我创建了 [SOLR-9963](https://issues.apache.org/jira/browse/SOLR-9963) ，到 2017 年 2 月初，我已经有了 Avatica 和 SOLR 整合的工作实例。我可以在 Apache Solr 上直接使用现有的 Avatica JDBC 驱动程序，没有任何问题。可悲的是，我还没有时间来完成合并这个变化。

### 使用 Apache 方解石 Avatica 处理程序测试 Apache Solr

Apache 方解石 Avatica 的一个很酷的特性是，您可以使用 JSON 有效负载通过 pure REST 与它进行交互。我创建了一个简单的测试脚本来展示即使使用 Apache Solr 也是如此。

`./test_avatica_solr.sh "http://localhost:8983/solr/test/avatica" "select * from test limit 10"`

**test _ avatica _ Solr . sh**T2】

```
#!/usr/bin/env bash

set -u
#set -x

AVATICA=$1
SQL=$2

CONNECTION_ID="conn-$(whoami)-$(date +%s)"
MAX_ROW_COUNT=100
NUM_ROWS=2
OFFSET=0

echo "Open connection"
curl -i -w "\n" "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"openConnection\",\"connectionId\": \"${CONNECTION_ID}\"}"

# Example of how to set connection properties with info key
#curl -i "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"openConnection\",\"connectionId\": \"${CONNECTION_ID}\",\"info\": {\"zk\": \"$ZK\",\"lex\": \"MYSQL\"}}"
echo

echo "Create statement"
STATEMENTRSP=$(curl -s "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"createStatement\",\"connectionId\": \"${CONNECTION_ID}\"}")
STATEMENTID=$(echo "$STATEMENTRSP" | jq .statementId)
echo

echo "PrepareAndExecuteRequest"
curl -i -w "\n" "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"prepareAndExecute\",\"connectionId\": \"${CONNECTION_ID}\",\"statementId\": $STATEMENTID,\"sql\": \"$SQL\",\"maxRowCount\": ${MAX_ROW_COUNT}, \"maxRowsInFirstFrame\": ${NUM_ROWS}}"
echo

# Loop through all the results
ISDONE=false
while ! $ISDONE; do
  OFFSET=$((OFFSET + NUM_ROWS))
  echo "FetchRequest - Offset=$OFFSET"
  FETCHRSP=$(curl -s "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"fetch\",\"connectionId\": \"${CONNECTION_ID}\",\"statementId\": $STATEMENTID,\"offset\": ${OFFSET},\"fetchMaxRowCount\": ${NUM_ROWS}}")
  echo "$FETCHRSP"
  ISDONE=$(echo "$FETCHRSP" | jq .frame.done)
  echo
done

echo "Close statement"
curl -i -w "\n" "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"closeStatement\",\"connectionId\": \"${CONNECTION_ID}\",\"statementId\": $STATEMENTID}"
echo

echo "Close connection"
curl -i -w "\n" "$AVATICA" -H "Content-Type: application/json" --data "{\"request\": \"closeConnection\",\"connectionId\": \"${CONNECTION_ID}\"}"
echo 
```

### 接下来是什么？

如果这个功能看起来很有趣，最好将你的想法添加到 [SOLR-9963](https://issues.apache.org/jira/browse/SOLR-9963) 中。如果有兴趣，那么我们可以努力让 SOLR-9963 合并。Apache Solr JDBC 驱动程序将需要切换到包装 Avatica JDBC 驱动程序。总的来说，这应该会改善 Apache Solr 带来的 SQL 体验。