# 这段代码中哪里不对？发布您的解决方案。

> 原文：<https://dev.to/mrm8488/what-is-not-right-in-this-code-post-your-solution-58gb>

```
const fs = require("fs");
const { promisify } = require("util");

const cache = new Map();

cache.set("file1", "data of file 1...");
cache.set("file2", "data of file 2...");

const readFilePromise = promisify(fs.readFile);

// What is wrong in this function?

const getFileData = (fileName, callback) => {
    if (cache.has(fileName)) return callback(null, cache.get(fileName));
    return readFilePromise(fileName)
        .then(data => {
            cache.set(fileName, data);
            return callback(null, data);
        }).catch(err => callback(err));
}; 
```

Enter fullscreen mode Exit fullscreen mode