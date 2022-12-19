# 使用 AWK 实现简单快速的 CSV 数据标准化

> 原文：<https://dev.to/djviolin/super-simple-and-fast-delimited-csv-data-normalization-with-awk-1ej6>

这里有一场关于开发的讨论，哪种编程语言或工具比人们意识到的更强大？

我分享了我的 AWK 片段，其中我规范化了一个 8GB 大小的带分隔符的 CSV 数据源，用于数据库导入，只使用了 AWK，和 Go 对应程序一样快。这项任务在我的家用机器上 7 分钟内完成，和我的 Go 代码一样:

```
#!/usr/bin/awk -f

# This block only runs once at the start, before the first line
# Use this to print CSV header on the top
BEGIN {
    FS="|"; # input field separator
    OFS="|"; # output field separator
}

# This block runs at every line
{
    # We will order a new named variable to every column
    line = $0; # variable `$0` stores the entire line
    url = $1;
    title = $2;
    body = $3
    tags = $4;

    if (line ~ /^$/) next; # if line is blank, then skip it
    if (NF != 4) next; # if column count is not equal to 4, then skip the line

    # Skip any line where tags column contains the word "cars"
    if (index(tags, "cars") != 0) { next; }

    # Normalize the url column with regex by only keeping the article id
    # Example input: <a href="https://example.com/article/foo123456">Hello</a>
    gsub(/.*example\.com\/article\/|[\042].*/, "", url); # outputs: foo123456

    # Skip lines that has non-alphanumeric characters in url column (like <>#&@)
    # Skip lines that has empty url column (after gsub normalization)
    # Skip lines where url starts with foo or bar
    if (url !~ /[[:alnum:]]/ ||
        length(url) == 0 ||
        url ~ /^foo|^bar/) {
        next;
    }

    # Replace multiple ; with one (needed for errorless CSV import in Postgres)
    gsub(/[\073]+/, ";", tags);

    # Print the line with OFS, aka: profit! :)
    print url, title, body, tags;
} 
```

Enter fullscreen mode Exit fullscreen mode

那么您应该使用以下命令来运行:

`$ zcat my-csv-file.zip | ./normalize.awk | sort --field-separator="|" --key=1,1 > normalized-sorted.csv`

专业提示:稍后更新数据库时，将此命令与`tail`命令结合使用。；)

也许您会发现这段代码很有用，可以节省您用 Python 或 Go 开发类似解决方案的大量时间。不客气！

如果你有什么要补充或修改的，那就拍我吧！