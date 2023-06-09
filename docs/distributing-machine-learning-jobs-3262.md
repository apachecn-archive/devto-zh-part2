# 分发机器学习作业

> 原文：<https://dev.to/ladvien/distributing-machine-learning-jobs-3262>

## 老板

一个人把机器学习的工作交给老板。作业是 JSON 对象，包含所需的机器学习脚本和成功执行所需的参数。老板存储作业并创建订单。订单是另一个 JSON 对象，表示请求的作业的状态。

```
 Job #4
 0                        Boss
/|\ +----------------->   ____
/ \                       +""+
                          +__+
                         [ ==.]`)
                   +----+====== 0 +--+
                   +                 |
                Order #3           Job #3
                   |                 |
                Order #2           Job #2
                   |                 |
                Order #1           Job #1 
```

Enter fullscreen mode Exit fullscreen mode

## 工人

工人使用[节点调度器](https://www.npmjs.com/package/node-schedule)向老板发送一个 HTTP 请求，让老板知道工人“无聊”然后，Boss 将在订单中搜索最早的未分配订单，如果找到了，它将把这个订单作为 JSON 对象返回给 Worker。此时，老板将订单的状态更新为“已分配”

工人发送另一个 HTTP 请求，这次请求与老板分配的订单相关的工作信息。

```
 Boss
          ____
          +""+
          +__+
         [ ==.]`)
   +----+====== 0 +--+
   +                 +            If the Boss finds an unassigned
Order #3           Job #3         Order it is returned. The worker requests the
   +                 +            related Job. The Boss updates the
Order #2           Job #2         the Order status to "assigned"
   +                 +                   Worker
Order #1           Job #1<-+              ____
  ^                        +----------->  +""+
  |                                       +__+
  +------------------------------------+ [ ==.]`)
          The worker checks with
          the boss periodically
          for the oldest submitted
          Order. 
```

Enter fullscreen mode Exit fullscreen mode

工人通过`stdout`将工作信息传递给适当的机器学习 Python 脚本。脚本被执行，无论成功与否，一个结果对象通过`stdout`被传递回工作者节点。

```
Worker
 ____
 +""+     Job #1
 +__+ +--------------->  Python Script
[ ==.]                         +
  ^                            |
  |                            |
  |                            v
  +------------------------ Outcome #1 
```

Enter fullscreen mode Exit fullscreen mode

然后，工人进行一个`callback` API 调用，并将结果对象传递给老板，以存储在数据库
中

```
 Boss                                Worker
          ____                                 ____
          +""+                                 +""+
          +__+                                 +__+
         [ ==.]`)                             [ ==.]`)
   +----+====== 0 +------+                       +
   |         |           |                       |
Order #3   Job #3     Outcome #1 <---------------+
   |         |
Order #2   Job #2
   |         |
Order #1   Job #1 
```

Enter fullscreen mode Exit fullscreen mode

### Mac 上的 MongoDB

```
brew install mongodb
nano /usr/local/etc/mongod.conf 
```

Enter fullscreen mode Exit fullscreen mode

您的文件应该看起来像这样

```
systemLog:
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true storage:
  dbPath: /usr/local/var/mongodb
net:
  bindIp: 127.0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

将`dbPath`更改为您希望 Mongo 存储数据库的位置。然后，使用 brew 的服务启动并启用 Mongo。

```
brew services start mongo 
```

Enter fullscreen mode Exit fullscreen mode

## 样本对象

### 订单

```
{  "_id"  :  "5bcc93d67f0b3f4844c87c7a",  "jobId"  :  "5bcc93d67f0b3f4844c87c79",  "createdDate"  :  ISODate("2018-10-21T14:57:26.980Z"),  "status"  :  "unassigned",  } 
```

Enter fullscreen mode Exit fullscreen mode

### 工作

```
{  "_id"  :  ObjectId("5bcc93d67f0b3f4844c87c79"),  "hiddenLayers"  :  [  {  "activation"  :  "relu",  "widthModifier"  :  4,  "dropout"  :  0.2  },  {  "activation"  :  "relu",  "widthModifier"  :  2.3,  "dropout"  :  0.2  },  {  "activation"  :  "relu",  "widthModifier"  :  1.3,  "dropout"  :  0.2  }  ],  "dataFileName"  :  "wine_data.csv",  "scriptName"  :  "nn.py",  "projectName"  :  "wine_data",  "depedentVariable"  :  "con_lot",  "crossValidateOnly"  :  true,  "crossValidationCrossingType"  :  "neg_mean_squared_error",  "batchSize"  :  100000,  "epochs"  :  3000,  "patienceRate"  :  0.05,  "slowLearningRate"  :  0.01,  "loss"  :  "mse",  "pcaComponents"  :  -1,  "extraTreesKeepThreshd"  :  0,  "saveWeightsOnlyAtEnd"  :  false,  "optimizer"  :  "rmsprop",  "lastLayerActivator"  :  "",  "learningRate"  :  0.05,  "l1"  :  0.1,  "l2"  :  0.1,  "minDependentVarValue"  :  0,  "maxDependentVarValue"  :  1500,  "scalerType"  :  "standard",  } 
```

Enter fullscreen mode Exit fullscreen mode

### 结果

```
{  "_id"  :  ObjectId("5bcc88fa7f0b3f4844c87c78"),  "status"  :  200,  "jobId"  :  "5bcc724d7449f746b5aa6fe8",  "loss"  :  15109.168650257,  "metric"  :  14281.4453526111,  } 
```

Enter fullscreen mode Exit fullscreen mode

## 代码

### 工人

server.js

```
var express = require('express');
var bodyParser = require('body-parser');
var pythonRunner = require('./preprocessing-services/python-runner');
var schedule = require('node-schedule');
var axios = require('axios');
var fs = require('fs');
var {Worker} = require('./worker/worker');

// Get Worker Node configuration
var fs = require('fs');
var config = JSON.parse(fs.readFileSync('./python-scripts/worker-node-configure.json', 'utf8'));

if(!config) { 
    console.log('No configuration file found.')
    process.exit();
}

// Boss' address
bossAddress = config.bossAddress;
nodeName = config.nodeName;
console.log(`Boss's address is ${bossAddress}`);
console.log(`This worker's name is ${nodeName}`);

var worker = new Worker('bored');

// Start server and add Middleware
var app = express();
const port = 3000;
app.use(bodyParser.json())

// Start checking for Boredom
var j = schedule.scheduleJob('*/1 * * * *', function(){
    if (worker.status === 'bored') {
        console.log('Worker is bored.');
        axios({
            method: 'post',
            url: bossAddress + `/bored/${nodeName}`
        }).then((response) => {
            let orderId = response.data._id
            let jobId = response.data.jobId;
            console.log(`Boss provided jobID #${jobId}`);
            axios({
                method: 'get',
                url: bossAddress + `/retrieve/job/${jobId}`
            }).then((response) => {
                let job = response.data;
                console.log(`Worker found the details for jobID #${jobId}`);
                job.callbackAddress = bossAddress;
                job.assignmentId = orderId;
                pythonRunner.scriptRun(job, worker)
                .then((response) => {
                    console.log('Worker started job, will let Boss know when finished.');
                });
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => {
            console.log('Failed to find new job.')
        });
    }
});

// Python script runner interface
app.post('/scripts/run', (req, res) => {
    try {
        let pythonJob = req.body;
        pythonRunner.scriptRun(pythonJob)
        .then((response) => {
            console.log(response);
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

python-runner.js

```
let {PythonShell} = require('python-shell')
var fs = require('fs');
var path = require('path');
var axios = require('axios');

var scriptRun = function(pythonJob, worker){
    console.log(worker);
    worker.status = 'busy';
    return new Promise((resolve, reject) => {
        try {
            let callbackAddress = pythonJob.callbackAddress;
            let options = {
                mode: 'text',
                pythonOptions: ['-u'], // get print results in real-time
                scriptPath: path.relative(process.cwd(), 'python-scripts/'),
                args: [JSON.stringify(pythonJob)]
            };
            PythonShell.run(pythonJob.scriptName, options, function (err, results) {
                if (err) throw err;
                try {
                    result = JSON.parse(results.pop());
                    if(result) {
                        console.log(callbackAddress + '/callback')
                        axios({
                            method: 'post',
                            url: callbackAddress + '/callback',
                            data: result
                        }).then((response) => {
                            console.log(`Worker let let the Boss know job is complete.`);
                            worker.status = 'bored';
                        }).catch((error) => {
                            worker.status = 'bored'
                        });
                    } else {
                        worker.status = 'bored'
                    }
                } catch (err) {
                   worker.status = 'bored'
                }
            });
            resolve({'message': 'job started'});
        } catch (err) {
            reject(err)
            worker.status = 'bored'
        }
    });
}
module.exports = {scriptRun} 
```

Enter fullscreen mode Exit fullscreen mode

### 老板

server.js

```
const express = require('express');
const bodyParser = require('body-parser');
const axios = require('axios');
var timeout = require('connect-timeout')

const {mongoose} = require('./backend/database-services/dl-mongo');
const workerNode = require('./backend/services/worker-node');
const work = require('./backend/services/work');

// Database collection
var {Job} = require('./backend/database-services/models/job');
var {Order} = require('./backend/database-services/models/order');

const bossAddress = 'http://maddatum.com'

// Server setup.
var app = express();
const port = 3000;

// Add request parameters.
app.use((req, res, next) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.setHeader('Access-Control-Allow-Headers', 
                  'Origin, X-Requested-With, Content-Type, Accept'); 
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, PATCH, DELETE, OPTIONS');
    next();
});

// Add the middleware.
app.use(bodyParser.json())

/*
This route is for creating new Jobs on the queue
*/
app.post('/job/:method', (req, res) => {
    if (!req.body) { return { 'message': 'No request provided.' }};
    try {
        switch (req.params.method) {
            case 'create':
                work.create(req.body)
                .then((response) =>{
                    res.send(response);
                }).catch((error) => {
                    res.send({'error': error })
                });
                break;
            default:
                res.send({'error': 'No method selected.'})
        }
    } catch (err) {
        res.send({'error': 'Error with request shape.', err})
    }
});

/*
This route is for adding new WorkerNodes to the database.
*/
app.post('/worker-node/:method', (req, res) => {
    if (!req.body) { return { 'message': 'No request provided.' }};
    try {
        switch (req.params.method) {
            case 'create':
                workerNode.create(req.body)
                .then((response) =>{
                    res.send(response);
                }).catch((error) =>{
                    res.send({'error': error.message});
                })
            break;
            default:
                throw err;
        }
    } catch (err) {
        res.send({'error': 'Error with request shape.', err })
    }
});

app.post('/callback', (req, res) => {
    if (!req.body) { return { 'message': 'No request provided.' }};
    let outcome = req.body;
    console.log(outcome);
    try {
        work.file(outcome)
        .then((response) =>{
            console.log(response);
            res.send(response);
        })
    } catch (err) {
        res.send({'error': 'Error with request shape.', err })
    }
});

/*
Route for Worker Node to let the Boss know it needs a Job.
The oldest Job which is unassigned is provided.
*/
app.post('/bored/:id', (req, res) => {
    if (!req.body) { return { 'message': 'No request provided.' }};
    try {
        let workerNodeId = req.params.id;
        console.log(`${workerNodeId} said it's bored.`);
        if (!workerNodeId) { throw {'error': 'No id provided.'}}
        Order.findOne({ status: 'unassigned' }, {}, { sort: { 'created_at' : -1 } }, (err, order) => {
            console.log(`Found a work order, #${order._id}`)
            order.status = 'assigned';
            console.log(`Provided ${workerNodeId} with ${order.jobId}`);
            order.save()
            .then((doc) => {
                console.log(`Updated the Order #${doc.id}'s status to ${order.status}`);
                res.send(doc);
            });
        })
        .catch((err) => {
            res.send({'message': `No work to do.  Don't get used to it.`})
        });
    } catch (err) {
        res.send({'error': 'Error with request shape.', err })
    }
});

/*
Retrieve Orders or Job
*/
app.get('/retrieve/:type/:id?/:param1?', (req, res) => {
    if (!req.body) { return { 'message': 'No request provided.' }};
    try {
        let type = req.params.type;
        let id = req.params.id;
        let param1 = req.params.param1;
        switch(type) {
            case 'order':
                Order.find().then((response) => {
                    res.send(response);
                });
                break;
            case 'job':
                if (!id)  { throw {'error': 'Missing Id'} }
                Job.findOne({'_id': id })
                .then((response) => {
                    res.send(response);
                });
                break;
            default:
                throw error
        }
    } catch (err) {
        res.send({'error': 'Error with request shape.', err })
    }
})

app.listen(port, () => {
    console.log(`Started on port ${port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode