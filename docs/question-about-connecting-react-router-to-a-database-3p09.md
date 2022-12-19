# 关于将 React 路由器连接到数据库的问题

> 原文：<https://dev.to/christycakes/question-about-connecting-react-router-to-a-database-3p09>

我正试图从我的 React 应用程序(有路线)连接到一个数据库。使用 Express 和 MySQL 的 get 请求成功:

db/index.js

```
import mysql from 'mysql'

let pool = mysql.createPool({
    connectionLimit: 10,
    password: 'app',
    user: 'app',
    database: 'chirprapp',
    host: 'localhost',
    port: '3306'
});

let chirprdb = {};

chirprdb.all = () => {
    return new Promise((resolve, reject) => {
        pool.query('SELECT * FROM chirps', (err, results) => {
            if (err) {
                connection.end()
                return reject(err);
            }
            return resolve(results);
        })
    })
}

chirprdb.one = (id) => {
    return new Promise((resolve, reject) => {
        pool.query('SELECT * FROM chirps WHERE id = ?', [id], (err, results) => {
            if (err) {
                return reject(err);
            }
            return resolve(results[0]);
        })
    })
} 
```

路线

```
import { Router } from 'express';
import db from '../db'

router.get('/:id?', async (req, res) => {
    let id = req.params.id;
    if (id) {
        try {
            let results = await db.one(id);
            res.json(results);
        } catch (e) {
            console.log(e);
            res.sendStatus(500);
        }
    } else {
        try {
            let results = await db.all();
            res.json(results);
        } catch (e) {
            console.log(e);
            res.sendStatus(500);
        }
    }
}); 
```

但是当我试图提出一个更简单的 get 请求时，比如

db/index.js

```
let mysql = require('mysql')

let connection = mysql.createConnection(
    {
        host: 'localhost',
        database: 'chirpapp',
        user: 'app',
        pasword: 'app'
    }
);
module.exports = connection; 
```

路线

```
import { Router } from 'express';
import connection from '../db'

let router = Router();

router.get('/', (req, res) => {
    connection.connect()
    connection.query('SELECT * FROM chirps', (err, results) => {
        if(err) {
            connection.end();
            res.sendStatus(500);
            return console.log(err);
        }
        res.json(results);
        return connection.end();
    })
}) 
```

我得到状态 500 内部服务错误和语法错误:JSON 中位置 0 处的意外标记 I。从我在网上读到的，语法错误意味着服务器发送回 HTML 而不是 JSON。

为什么基本上相同的请求会得到两种不同的响应？

这是我第一次这样做，所以你的任何见解肯定会有所启发。