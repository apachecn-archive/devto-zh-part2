# 使用 Python、NodeJS、Angular 和 MongoDB 创建机器学习系统

> 原文：<https://dev.to/ladvien/using-python-nodejs-angular-and-mongodb-to-create-a-machine-learning-system-1804>

我已经开始设计一个系统来管理我构建的数据分析工具。

1.  非法的 REST 接口
2.  现有 Python 脚本的接口
3.  从 Python 脚本创建微服务的过程
4.  用于创建机器学习作业的界面，可从我的免费机器上获取。
5.  管理作业机器的作业队列以系统地处理机器学习作业
6.  数据存储和访问
7.  结果访问和职务元数据
8.  一种可视化结果的方法

我已经进入了一个相当复杂的处理上述问题的过程。我尝试过削减框架，因为我知道维护它将是一场噩梦，但我没有看到它。

*   用于在`HQ Machine`和`Worker Nodes`之间创建 RESTful 接口的节点
*   节点，以定期 ping the 机器，查看它们是否有要运行的作业
*   位于`HQ Machine`上的 MongoDB，用于存储作业结果数据、数据集路径以及可能的主数据
*   与`HQ Node`交互的角度，用于创建作业创建和结果查看 UI。
*   [ngx-数据表](https://github.com/swimlane/ngx-datatable)用于查看表格结果。
*   [ngx-charts](https://github.com/swimlane/ngx-charts) 用于查看工作结果(例如，可视化方差和线性度)
*   Python 来访问所有最新的令人敬畏的 ML 框架
*   [python-shell (npm)](https://www.npmjs.com/package/python-shell) 用于创建节点和 python 之间的接口。

### 利用家中所有的机器

机器学习对我来说是一个新世界。但是，这很酷。我喜欢在我做其他工作的时候让机器做困难的事情。这让我觉得特别有效率——就像，“我创造了那台机器，所以它做的任何工作都是我的功劳。*和*！我做的工作，而它在做它的工作。”这就是我拥有两台 3D 打印机的原因。

我注意到有可能利用我放在家里的旧电脑来达到同样的效果。计划是提取一个神经网络脚本，将其安装在所有的计算机上，并创建一个`HQ Computer`，在这里我可以创建一组超参数传递给整个房子的`Worker Nodes`。

为什么？很高兴我问起你。我对使用电脑感到内疚。有一个旧的 AMD 台式机，里面有一个 GFX1060，一个 2013 年的 MacBook Pro(我儿子的)，还有我 2015 年的 MacBook Pro。这些已经没什么用了，因为我的雇主已经提供了一台 iMac 供我使用。他们需要挣钱养活自己。

怎么会？再次，很高兴为你问。我将创建一个系统，从超参数集制作深度学习作业，并将它们发送到这些闲置的机器，从而在我努力支付账单的同时，试图让它们解决问题。这来自于神经网络的力量。它们几乎不需要手工调整。您只需为它们提供超参数，并让它们运行。

以下是餐巾涂鸦:

```
+-Local------------------------------------------------------+
|                                                            |
|        ____                   ____      Each machine runs  |
|        |""|                   |""|      Node and Express   |
|  HQ    |__|             #1    |__|      server, creating   |
|       [ ==.]`)               [ ==.]`)   routes to Python   |
|       ====== 0               ====== 0   scripts using      |
|  The HQ machine runs          ____      stdin and stdout   |
|  Node and Express, but        |""|                         |
|  the routes are for     #2    |__|                         |
|  storing results in a        [ ==.]`)                      |
|  database.                   ====== 0                      |
|                               ____                         |
|                               |""|                         |
|                         #3    |__|        Worker           |
|                              [ ==.]`)     Nodes            |
|                              ====== 0                      |
|                                                            |
+------------------------------------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

```
+-Local------------------------------------------------------+
|                 Each worker Node checks         Workers    |
|        ____    with HQ on a set interval         ____      |
|        |""|       for jobs to run                |""|      |
|  HQ    |__|   <--------------------------+ #1    |__|      |
|       [ ==.]`)                                  [ ==.]`)   |
|       ====== 0                                  ====== 0   |
|       ^ |                                        ____      |
|       | |                                  #2    |""|      |
|       | +--------------------------------------->|__|      |
|       |             If there is a job, the      [ ==.]`)   |
|       |             Worker will send a GET      ====== 0   |
|       |              request for the job         ____      |
|       |                  parameters              |""|      |
|       |                                    #3    |__|      |
|       +-----------------------------------------[ ==.]`)   |
|         Once completed, the Worker updates HQ   ====== 0   |
|              with the job results.                         |
+------------------------------------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

### 工作者节点

代码非常简单。它使用 Node、Express 和 python-shell 来创建一个标准化的 REST 接口，以便在控制作业队列的`HQ Node`之间创建简单的交互。

#### 节点端

这是概念验证的 NodeJS 代码。

```
var express = require('express');
var bodyParser = require('body-parser');
var pythonRunner = require('./preprocessing-services/python-runner');

var app = express();
const port = 3000;

app.use(bodyParser.json())

// Python script runner interface
app.post('/scripts/run', (req, res) => {
    try {
        let pythonJob = req.body;
        pythonRunner.scriptRun(pythonJob)
        .then((response, rejection) => {
            res.send(response);
        });
    } catch (err) {
        res.send(err);
    }
});

app.listen(port, () => {
    console.log(`Started on port ${port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码是一个使用 Express 的死简单 NodeJS 服务器。它使用`body-parser`中间件来塑造 JSON 对象。`pythonJob`对象看起来像这样(真实路径名已经被改变，以帮助保护它们的匿名性)。

```
{  "scriptsPath":  "/Users/hinky-dink/dl-principal/python-scripts/",  "scriptName":  "union.py",  "jobParameters":  {  "dataFileName":  "",  "dataPath":  "/Users/hinky-dink/bit-dl/data/lot-data/wine_encoded/",  "writePath":  "/Users/hinky-dink/bit-dl/data/lot-data/wine_encoded/",  "execution":  {  "dataFileOne":  "wine_2017_encoded.csv",  "dataFileTwo":  "wine_2018_encoded.csv",  "outputFilename":  "wine_17-18.csv"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这些属性中的每一个都将被传递给 Python shell，以便执行`data_prep.py`。它们作为系统参数传递给 shell。

这里是`python-runner.js`

```
let {PythonShell} = require('python-shell')

var scriptRun = function(pythonJob){    
    return new Promise((resolve, reject) => {
        console.log(pythonJob)
        try {
            let options = {
                mode: 'text',
                pythonOptions: ['-u'], // get print results in real-time
                scriptPath: pythonJob.scriptsPath,
                args: [pythonJob.jobParameters.dataFileName, 
                       pythonJob.jobParameters.dataPath, 
                       pythonJob.jobParameters.writePath,
                       JSON.stringify(pythonJob.jobParameters.execution)]
            };
            PythonShell.run(pythonJob.scriptName, options, function (err, results) {
                if (err) throw err;
                try {
                    result = JSON.parse(results.pop());
                    if(result) {
                        resolve(result);
                    } else {
                        reject({'err': ''})
                    }
                } catch (err) {
                    reject({'error': 'Failed to parse Python script return object.'})
                }
            });
        } catch (err) {
            reject(err)
        }
    });
}
module.exports = {scriptRun} 
```

Enter fullscreen mode Exit fullscreen mode

#### 蟒侧

这是上面例子中的 Python 脚本。这意味着检测什么类型的数据在一个表中。如果它是连续的，它就不去管它(我可能会添加规范化选项作为某个点)，如果它是分类的，它就把它转换成一个[虚拟变量](https://en.wikipedia.org/wiki/Dummy_variable_(statistics))。然后，它将这些编码数据保存在`Worker Node`端(现在)。最后，它将一个`JSON`字符串返回给`node`方。

```
"""
Created on Mon Jun 11 21:12:10 2018
@author: cthomasbrittain
"""

import sys
import json
# filename = sys.argv[1]
filepath = sys.argv[2]
pathToWriteProcessedFile = sys.argv[3]

request = sys.argv[4]
request = json.loads(request)

try:
    cols_to_remove = request['columnsToRemove']
    unreasonable_increase = request['unreasonableIncreaseThreshold']
except:
    # If columns aren't contained or no columns, exit nicely
    result = {'status': 400, 'message': 'Expected script parameters not found.'}
    print(str(json.dumps(result)))
    quit()

pathToData = filepath + filename

# Clean Data --------------------------------------------------------------------
# ------------------------------------------------------------------------------- 
# Importing data transformation libraries import pandas as pd

# The following method will do the following:a
#   1\. Add a prefix to columns based upon datatypes (cat and con)
#   2\. Convert all continuous variables to numeric (float64)
#   3\. Convert all categorical variables to objects
#   4\. Rename all columns with prefixes, convert to lower-case, and replace
#      spaces with underscores.
#   5\. Continuous blanks are replaced with 0 and categorical 'not collected'
# This method will also detect manually assigned prefixes and adjust the 
# columns and data appropriately.  
# Prefix key:
# a) con = continuous
# b) cat = categorical
# c) rem = removal (discards entire column) 
def add_datatype_prefix(df, date_to_cont = True):    
    import pandas as pd
    # Get a list of current column names.
    column_names = list(df.columns.values)
    # Encode each column based with a three letter prefix based upon assigned datatype.
    # 1\. con = continuous
    # 2\. cat = categorical 
    for name in column_names:
        if df[name].dtype == 'object':
            try:
                df[name] = pd.to_datetime(df[name])
                if(date_to_cont):
                    new_col_names = "con_" + name.lower().replace(" ", "_").replace("/", "_")
                    df = df.rename(columns={name: new_col_names})
                else:
                    new_col_names = "date_" + name.lower().replace(" ", "_").replace("/", "_")
                    df = df.rename(columns={name: new_col_names})                    
            except ValueError:
                pass

    column_names = list(df.columns.values)

    for name in column_names:
        if name[0:3] == "rem" or "con" or "cat" or "date":
            pass
        if df[name].dtype == 'object':
            new_col_names = "cat_" + name.lower().replace(" ", "_").replace("/", "_")
            df = df.rename(columns={name: new_col_names})
        elif df[name].dtype == 'float64' or df[name].dtype == 'int64' or df[name].dtype == 'datetime64[ns]':
            new_col_names = "con_" + name.lower().replace(" ", "_").replace("/", "_")
            df = df.rename(columns={name: new_col_names})
    column_names = list(df.columns.values)

    # Get lists of coolumns for conversion
    con_column_names = []
    cat_column_names = []
    rem_column_names = []
    date_column_names = []

    for name in column_names:
        if name[0:3] == "cat":
            cat_column_names.append(name)
        elif name[0:3] == "con":
            con_column_names.append(name)
        elif name[0:3] == "rem":
            rem_column_names.append(name)
        elif name[0:4] == "date":
            date_column_names.append(name)

    # Make sure continuous variables are correct datatype. (Otherwise, they'll be dummied).
    for name in con_column_names:
        df[name] = pd.to_numeric(df[name], errors='coerce')
        df[name] = df[name].fillna(value=0)

    for name in cat_column_names:
        df[name] = df[name].apply(str)
        df[name] = df[name].fillna(value='not_collected')

    # Remove unwanted columns    
    df = df.drop(columns=rem_column_names, axis=1)
    return df

# ------------------------------------------------------
# Encoding Categorical variables
# ------------------------------------------------------ 
# The method below creates dummy variables from columns with
# the prefix "cat".  There is the argument to drop the first column
# to avoid the Dummy Variable Trap. def dummy_categorical(df, drop_first = True):
    # Get categorical data columns.
    columns = list(df.columns.values)
    columnsToEncode = columns.copy() 

    for name in columns:
        if name[0:3] != 'cat':          
            columnsToEncode.remove(name)

    # if there are no columns to encode, return unmutated.
    if not columnsToEncode:
        return df

    # Encode categories
    for name in columnsToEncode:

        if name[0:3] != 'cat':
            continue

        tmp = pd.get_dummies(df[name], drop_first = drop_first)
        names = {}

        # Get a clean column name.
        clean_name = name.replace(" ", "_").replace("/", "_").lower()
        # Get a dictionary for renaming the dummay variables in the scheme of old_col_name + response_string
        if clean_name[0:3] == "cat":
            for tmp_name in tmp:
                tmp_name = str(tmp_name)
                new_tmp_name = tmp_name.replace(" ", "_").replace("/", "_").lower()
                new_tmp_name = clean_name + "_" + new_tmp_name
                names[tmp_name] = new_tmp_name

        # Rename the dummy variable dataframe
        tmp = tmp.rename(columns=names)

        # join the dummy variable back to original dataframe.
        df = df.join(tmp)

    # Drop all old categorical columns
    df = df.drop(columns=columnsToEncode, axis=1)
    return df

# Read the file df = pd.read_csv(pathToData)

# Drop columns such as unique IDs try:
    df = df.drop(cols_to_remove, axis=1)
except:
    # If columns aren't contained or no columns, exit nicely
    result = {'status': 404, 'message': 'Problem with columns to remove.'}
    print(str(json.dumps(result)))
    quit()

# Get the number of columns before hot encoding num_cols_before = df.shape[1]

# Encode the data. df = add_datatype_prefix(df)
df = dummy_categorical(df)

# Get the new dataframe shape. num_cols_after = df.shape[1]

percentage_increase = num_cols_after / num_cols_before

result = ""

if percentage_increase > unreasonable_increase:
    message = "\"error\": \"Feature increase is greater than unreasonableIncreaseThreshold, most likely a unique id was included."
    result = {'status': 400, 'message': message}
else:
    filename = filename.replace(".csv", "")
    import os
    if not os.path.exists(pathToWriteProcessedFile):
        os.makedirs(pathToWriteProcessedFile)

    writeFile = pathToWriteProcessedFile + filename + "_encoded.csv"
    df.to_csv(path_or_buf=writeFile, sep=',')

    # Process the results and return JSON results object
    result = {'status': 200, 'message': 'encoded data', 'path': writeFile}

print(str(json.dumps(result))) 
```

Enter fullscreen mode Exit fullscreen mode

这是前提。我将在一系列文章中添加更多的服务。