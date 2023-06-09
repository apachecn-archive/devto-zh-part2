# 带 ttl 的简单 golang 批发商

> 原文：<https://dev.to/irlndts/simple-golang-jobber-with-ttl-455h>

有些时候你需要 TTL 来做一些事情，比如 API 请求或者其他一些限制。在 golang 中，你可以使用 goroutines 和 standart time package 以非常简单的方式完成。

解决这个任务的基本方法是这样的。

```
// First of all you need a channel to interract with the request
c := make(chan int, 1)

// start anonymous routine with the some function call
go func() {
    result := someFunc()
    c <- result
    close(c)
}()

// start timer
timer := time.NewTimer(<timeout>)
defer timer.Stop()

var result int
var err error
select {
    case result = <-c:
    // do nothing, break select
    case <-timer.C:
    err = fmt.Errorf("time exceeded")
}
// handle error and use the result in the code 
```

Enter fullscreen mode Exit fullscreen mode

举个例子，有一张网。带 TTL
的标准程序包中的 LookupIP 函数调用

```
 func main() {
    ips, err := lookupIPs("https://dev.to", time.Second)
    if err != nil {
        // check the error
    }
    // do something with the list of ips
}

type lookupIP struct {
    ips []net.IP
    err error
}

func lookupIPs(url string, timeout time.Duration) ([]net.IP, error) {
    c := make(chan lookupIP, 1)

    go func() {
        ips, err := net.LookupIP(url)
        c <- lookupIP{ips, err}
        close(c)
    }()

    timer := time.NewTimer(timeout)
    defer timer.Stop()

    var lIP lookupIP
    select {
    case lIP = <-c:
        // do nothing, break select
    case <-timer.C:
        lIP.err = fmt.Errorf("lookup ip: time exceeded")
    }
    if lIP.err != nil {
        return nil, lIP.err
    }
    return lIP.ips, nil
} 
```

Enter fullscreen mode Exit fullscreen mode