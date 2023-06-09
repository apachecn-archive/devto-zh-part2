# 💥每个带有对象💥

> 原文：<https://dev.to/bethanyhaubert/eachwithobject-42io>

最近我一直在纠结一个有点恶心的重构故事。下面是我对一个方法的第一次迭代，该方法循环遍历一个`@nameservers`哈希，并为每个哈希将`host`赋给类中的一个实例变量，然后循环遍历`info`哈希中的每个 IP，并将私有方法的输出添加到`@results`哈希中。
循环太多了。💩

```
def check
    @results = {}
    @nameservers.each do |host, info|
      @host = host
      info[:ips].each do |nameserver_ip|
        @results[nameserver_ip] = ip_check(nameserver_ip)
      end
    end

    @results
+end 
```

`@nameservers`是一个类似这样的散列:

```
{
            "Google" => {
        :country => "USA",
            :ips => [
            [0] "8.8.8.8",
            [1] "8.8.4.4"
        ]
    },
           "OpenDNS" => {
        :country => "USA",
            :ips => [
            [0] "208.67.222.222",
            [1] "208.67.220.220"
        ]
    },
         "Fortalnet" => {
        :country => "Brazil",
            :ips => [
            [0] "189.90.16.20",
            [1] "189.90.16.21"
        ]
    },
    "Probe Networks" => {
        :country => "Germany",
            :ips => [
            [0] "82.96.64.2",
            [1] "82.96.65.2"
        ]
    }
} 
```

简化该方法的最佳方式是什么？嗯，另一个开发者让我对`each_with_object`方法感兴趣。不用显式地定义和返回`@results`散列，它可以被合并到一个循环中。它是如何工作的？

```
 def check
                @nameservers.each_with_object({}) do |(host, info), results|
                    info[:ips].each do |nameserver_ip|
                        results[nameserver_ip] = ip_check(host, nameserver_ip)
                    end
                end
            end 
```

当你在`@nameservers`上调用`each_with_object`时，我会传递一个空散列作为参数。(不必是空散列。它可以是预先存在的散列或者甚至是数组)。在程序块内部，传递要循环的一个或多个对象，在本例中是`host`和`info`，然后是作为参数传递给对象的变量名。所以我的散列将作为`results`返回。
我的方法中的第二个循环几乎保持不变，除了我决定将`host`也传递给我的私有`ip_check`方法，这样状态就不会泄漏到对象的其余部分。

瞧啊。正是我需要的。