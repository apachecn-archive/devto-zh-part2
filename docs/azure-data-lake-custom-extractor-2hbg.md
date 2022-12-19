# Azure 数据湖自定义提取器

> 原文：<https://dev.to/alanmbarr/azure-data-lake-custom-extractor-2hbg>

最近有人问我是否可以帮助他们使用 U-SQL 从 Azure Data Lake 中查询数据。ADL 作为云文件/数据库存储，可以查询大量数据。ADL 基于 YARN 企业 Hadoop 架构。ADL 提供了多种查询数据的方法，主要是他们自己的分析语言 U-SQL。它还具有使用 Apache Spark 进行查询的能力。大数据查询语言的目的是便于使用多台机器跨多个文件运行 map reduce 作业，以利用尽可能多的并行性。ADL 的一个弱点是它仅是云，并且似乎依赖于 Cosmos 而不是自己的本地 hadoop 集群。

## U-SQL

根据入门[文档](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started)，“U-SQL 是一种结合了声明式 SQL 和命令式 C#的语言”。实际上，它更像是带有一些 SQL 语义的 C#，但是如果你习惯于任何级别的数据库工具，你可能会失望。U-SQL 也支持 Python 和 R 扩展，尽管有一些限制。这似乎是使用 Spark(Scala/Java/Python)脚本的替代方法。

## 内置 Ins

Azure Data Lake 附带的大多数内置工具将实现我们试图为典型日志文件实现的目标。使用一个包含四列的普通 tweet 日志文件的 Getting started 示例，我们可以提取数据，选择我们想要输出的内容，并使用一些函数对其进行转换。

```
@t = EXTRACT 
date string,
time string,
author string,
tweet string
FROM "/input/MyTwitterHistory.csv" USING Extractors.Csv();

@res = 
SELECT author,
COUNT(\*) AS tweetcount
FROM @t GROUP BY author;

OUTPUT @res TO "/output/MyTwitterAnalysis.csv"ORDER BY tweetcount DESC
USING Outputters.Csv(); 
```

在很大程度上，内置工具将解决大多数常见的挑战。除非您的数据不符合典型的日志结构，比如在一组日志中填充一个 json 对象。如果 datetime 格式是以非标准方式完成的，那么在清理数据时使用命令式 C#工具也很方便。

## 挑战

在很大程度上，U-SQL 似乎并不太难学，它肯定有自己的概念，但大多数日志文件并不需要额外的复杂性。我们遇到的挑战来自一个日志文件，其中嵌入了一个 json 对象，该对象本身嵌入了一个巨大的 xml 字符串。我对许多 Azure 工具和云产品的痛点来自于围绕 C#生态系统的设计决策。字符串为 UTF-16，并由于 ADL 中的这种大小而导致内存问题。还有绑定重定向和 dll 如何在内部处理。我不确定这些事情有多容易避免，但处理起来相当乏味。

我们最初处理带有嵌入式 json 对象的日志文件的策略是使用 TextExtractor 来拆分管道，然后使用 Microsoft。Analytics.Sample.Format 库直接获取并解析 json。它完美地工作，直到我们碰到我们的巨大物体。提供的 json 解析库假设解析整个对象，幸运的是，在我们的例子中，我们只需要元数据的一部分。我们找到了一个 stack overflow 帖子，对我们的问题给出了答案。在实现了一个解决方案之后，现在更清楚了，唯一可用的选项是更新提取器以返回不同类型的数据。这似乎不是一个特定于 U-SQL 的命令。

## 提取器

任何提取器需要的主要元素是实现 IExtractor 接口。这需要一个被覆盖的提取方法。

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Interfaces.Streaming;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Utilities;

namespace Utilities
{
    //Use Atomic if we want to operate on the whole file vs line by line
    [SqlUserDefinedExtractor(AtomicFileProcessing = false)]
    public class CustomTextExtractor : IExtractor
    {
        //Contains the row
        private readonly Encoding _encoding;
        private readonly byte[] _row_delim;
        private readonly char _col_delim;

        public CustomTextExtractor(Encoding encoding = null, string row_delim = "\r\n", char col_delim = '|')
        {
            _encoding = Encoding.UTF8;
            _row_delim = _encoding.GetBytes(row_delim);
            _col_delim = col_delim;
        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {

            //Read the input line by line
            foreach (Stream current in input.Split(_row_delim))
            {
                using (StreamReader streamReader = new StreamReader(current, _encoding))
                {
                    string[] parts = streamReader.ReadToEnd()
                        .Trim().Split(new char[] { _col_delim }, StringSplitOptions.None);

                    int count = 0; // start with first column
                    foreach (string part in parts)
                    {
                        if (count == 3)
                        {
                            string json = part;
                            var log = JsonConvert.DeserializeObject<RootObject>(json);
                            var items = new SqlArray<string>(new string[] 
                            {
                                log.header.appName,
                                log.header.currentContext,
                                log.header.documentID,
                                log.header.endTime,
                                log.header.errorCode,
                                log.header.message,
                                log.header.rootContext,
                                log.header.server,
                                log.header.startTime,
                            });

                            output.Set<SqlArray<string>>(count, items);
                            count += 1;
                        }
                        else
                        {
                            //    // keep the rest of the columns as-is
                            output.Set(count, part);
                            count += 1;
                        }

                    }
                }
                yield return output.AsReadOnly();
            }
            yield break;
        }

    }

} 
```

在我们使用的提取方法中。Set on output 设置列值的内容。在我们的日志中，我们知道第四列总是我们的 json 对象。USQL 能够发送回一些特别支持的数据类型，还有一些特殊的数据类型是 SqlArray 和 SqlMap(键值对)。一旦这段代码完成，我们就可以编译它并获取。dll 并上传到 azure 数据湖。另一种选择是使用 codebehind，本质上是一个与 usqlscript 类似命名的文件，但以。然而这将在每次运行时上传代码。

## U-SQL 脚本

我们的 U-SQL 脚本。我建议下载 Azure 数据湖工具，并首先在本地运行作业，因为每次运行都有成本。

```
// A. CREATE ASSEMBLY: Register assemblies (if they do not already exist).
CREATE ASSEMBLY IF NOT EXISTS [Newtonsoft.Json] FROM @"adl://alandatalakeadls.azuredatalakestore.net/Newtonsoft.Json.dll";
CREATE ASSEMBLY IF NOT EXISTS [Microsoft.Analytics.Samples.Formats] FROM  @"adl://alandatalakeadls.azuredatalakestore.net/Microsoft.Analytics.Samples.Formats.dll";
DROP ASSEMBLY IF EXISTS [Utilities];
CREATE ASSEMBLY IF NOT EXISTS [Utilities] FROM @"adl://alandatalakeadls.azuredatalakestore.net/Utilities.dll";
// B. REFERENCE ASSEMBLY: Load assemblies for compile time and execution.
REFERENCE ASSEMBLY [Newtonsoft.Json];
REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];
REFERENCE ASSEMBLY [Utilities];

// C. USING: Specify namespace to shorten function names (e.g. Microsoft.Analytics.Samples.Formats.Json.JsonExtractor)
USING Microsoft.Analytics.Samples.Formats.Json;

// 1\. Initialise variables for Input (e.g. JSON) and Output (e.g. CSV).
DECLARE @InputFile string = @"adl://alandatalakeadls.azuredatalakestore.net/BreakDownOfCode.txt";
DECLARE @OutputFile string = @"adl://alandatalakeadls.azuredatalakestore.net/output.csv";

@RawExtract  = EXTRACT 
        Date string,              
        Status  string,           
        PriceNotification  string,
        header SqlArray <string>
FROM @InputFile 
USING new Utilities.CustomTextExtractor(col_delim:'|',row_delim:"\n");

//Change the data in some way if we need to such as if an item is null coalesce to an empty string. Call C# methods.
@IntermediaryData =
SELECT
    Date,
    Status,
    PriceNotification,
    header
FROM @RawExtract;

@OutputData =
SELECT
    Date,
    Status,
    PriceNotification,
    header[0] AS appName,
    header[1] AS currentContext,
    header[2] AS documentID,
    header[3] AS endTime,
    header[4] AS errorCode,
    header[5] AS message,
    header[6] AS rootContext,
    header[7] AS server,
    header[8] AS startTime 
FROM @IntermediaryData;

OUTPUT @OutputData
TO @OutputFile
USING Outputters.Csv(outputHeader:true, quoting : false); 
```

[示例中的内置工具和提供的 JSON 用户定义函数。Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats/Microsoft.Analytics.Samples.Formats) 尤其是 JsonTuple 可以完成大部分繁重的工作。编写一个定制的提取器并不需要太多额外的工作，而且根据你的日志有多时髦，它可能是必要的。