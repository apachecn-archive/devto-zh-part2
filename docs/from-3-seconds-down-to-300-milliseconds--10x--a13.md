# 从 3 秒到 300 毫秒:10^x

> 原文：<https://dev.to/lucpattyn/from-3-seconds-down-to-300-milliseconds--10x--a13>

这是一个数据集过滤算法，对 10000 个条目耗时 3 秒

```
    json& w = where; // condition for filter in JSON format
    // iterate an array of 10000 entries
    for (json::iterator it = col.begin(); it != col.end(); ++it) {
        bool matched = true;
        // iterate the condition array (area of interest)
        for (json::iterator wit = w.begin(); wit != w.end(); ++wit) {

            json compareParams = wit.value();
            std::string key = compareParams.begin().key();

            for (auto item : compareParams) {
                //item.dump(4);
                std::string op = item.begin().key();
                int val = item.begin().value();
                matched &= match(*it, key, op, val);

            }           

        }

        if (matched) {
            out.push_back(*it);
        }
    }

```

该算法的性能没有达到预期。所以稍微调整一下代码就快了 10 倍。JSON 格式中有一个条件需要匹配。刚刚把 json 解析代码移到了大循环和 wallah 之外...该算法返回结果的速度快了 10 倍(或更多)。

```
    // iterate the condition array
    struct Compare{
        Compare(std::string k, std::string o, int v) {
            key = k;
            op = o;
            val = v;
        }

        std::string key;
        std::string op;
        int val;
    };
    std::vector c;

    json& w = where;
    for (json::iterator wit = w.begin(); wit != w.end(); ++wit) {
        json compareParams = wit.value();
        std::string key = compareParams.begin().key();

        for (auto item : compareParams) {

            std::string op = item.begin().key();
            int val = item.begin().value();

            c.push_back(Compare(key, op, val));
        }

    }

    // iterate the collection array
    for (json::iterator it = col.begin(); it != col.end(); ++it) {
        bool matched = true;
        // area of interest: This is where the condition parsing codes were
        for (int i = 0; i < c.size(); i++) {
            matched &= match(*it, c[i].key, c[i].op, c[i].val);
        }

        if (matched) {
            out.push_back(*it);
        }
    }       

```