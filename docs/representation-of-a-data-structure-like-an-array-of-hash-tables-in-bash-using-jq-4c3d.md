# 使用 jq 在 Bash 中表示类似哈希表数组的数据结构

> 原文：<https://dev.to/yuyatakeyama/representation-of-a-data-structure-like-an-array-of-hash-tables-in-bash-using-jq-4c3d>

Bash 中需要过哈希表吗？我在编写部署脚本时需要它。

我发现 Bash 4 有一个数据结构叫做 Dictionary。
[https://stack overflow . com/questions/1494178/how-to-define-hash-tables-in-bash](https://stackoverflow.com/questions/1494178/how-to-define-hash-tables-in-bash)

但是我的笔记本电脑中的 Bash 版本是 3.2.57，我无法使用它。
当然，更新 Bash 很容易。
但是我们可能需要在旧版本的环境中工作。
所以我想出了另一个办法。

## 如何

```
#!/bin/bash -eu

# A data structure like an array of hash tables
# Strictly, it's a stream of JSONs
PARAMETERS=$(cat <<_EOT_ {
  "foo":"FOO 1",
  "bar":"BAR 1"
}
{"foo":"FOO 2","bar":"BAR 2"}
{
  "foo": "フー",
  "bar": "\u30d0\u30fc"
} _EOT_ )

# Transform into a line-delimited JSONs
PARAMETERS=$(echo "${PARAMETERS}" | jq . --monochrome-output --compact-output)

# Enumerate JSONs and output foo and bar of each item
while IFS='' read -r param || [[ -n "${param}" ]]; do foo=$(echo "${param}" | jq .foo -r)
  bar=$(echo "${param}" | jq .bar -r)

  echo "foo=${foo}"
  echo "bar=${bar}"

  echo "---"
done < <(echo "${PARAMETERS}") 
```

Enter fullscreen mode Exit fullscreen mode

```
$ bash json_stream.sh
foo=FOO 1
bar=BAR 1
--------
foo=FOO 2
bar=BAR 2
--------
foo=フー
bar=バー
-------- 
```

Enter fullscreen mode Exit fullscreen mode

*   使用 heredoc 构建一个字符串形式的 JSON
*   将 JSON 转换成行分隔的 JSON
*   从每行读取一个 JSON
    *   [https://stackoverflow.com/a/10929511](https://stackoverflow.com/a/10929511)
*   在循环中检索每个属性，如`jq .foo`

希望你喜欢它！