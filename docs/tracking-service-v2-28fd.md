# 与 Go 和 Redis V2 的跟踪服务

> 原文：<https://dev.to/douglasmakey/tracking-service-v2-28fd>

你还记得我的上一篇文章吗？在那篇文章中，我写了一个服务来寻找像优步这样的司机。如果没有，你可以在这里检查所以现在，我们要写我们服务的 V2。

我们服务的当前状态是，当用户使用资源“search”时，用户会收到一个响应，其中包含离他最近的驱动程序。但是如果用户身边没有司机会怎么样呢？我们不希望服务客户机向同一个端点发出大量请求来寻找驱动程序。我们想要做的是遵循优步使用的模式，那就是我们的客户端只发出一个请求，这个请求引发了一个任务，这个任务为我们寻找驱动程序 X 次，然后，用户收到结果。

为此，我们将使用 Go 提供的一些工具:Goroutines、频道和时间。跑马灯结构。

注意:这将是一个基本的实现。

## 高楼大厦例程

一个 goroutine 是轻量级的，花费比分配堆栈空间多一点。栈开始很小，所以它们很便宜，并且根据需要通过分配(和释放)堆存储来增长。

要将一个函数作为 goroutine 运行，只需在 func 调用前放上关键字`go`。当 func 完成时，goroutine 安静地退出。

```
go list.Sort()  // run list.Sort concurrently; don't wait for it. 
```

Enter fullscreen mode Exit fullscreen mode

注意:Goroutines 在同一个地址空间中运行，所以对共享内存的访问必须同步。sync 包提供了有用的原语，尽管在 Go 中你不太需要它们，因为还有其他原语。

[有效的 Go-Go 路线](https://golang.org/doc/effective_go.html?#goroutines)

## 通道

~~通道是一种类型化的管道，通过它你可以用通道操作符<发送和接收值。~~

```
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and
           // assign value to v. 
```

Enter fullscreen mode Exit fullscreen mode

(数据按箭头方向流动。)

像地图一样，通道是用 make 分配的，结果值充当对底层数据结构的引用。如果提供了可选的整数参数，它将设置通道的缓冲区大小。对于无缓冲或同步通道，默认值为零。

```
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files 
```

Enter fullscreen mode Exit fullscreen mode

无缓冲通道将通信(值的交换)与同步结合在一起，保证两个计算(goroutines)处于已知状态。

有很多使用频道的好习惯用法。这里有一个让我们开始。在上一节中，我们在后台启动了排序。通道可以允许正在启动的 goroutine 等待排序完成。

```
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
<-c   // Wait for sort to finish; discard sent value. 
```

Enter fullscreen mode Exit fullscreen mode

接收器总是阻塞，直到有数据要接收。如果通道无缓冲，发送方会阻塞，直到接收方收到该值。如果通道有缓冲区，则发送方仅在值被复制到缓冲区之前阻塞；如果缓冲区已满，这意味着要等到某个接收器检索到一个值。

[有效渠道](https://golang.org/doc/effective_go.html?#channels)

## 时间。心脏

计时器是当你想在将来做某件事的时候用的——ticker 是当你想定期重复做某件事的时候用的。这是一个定期滴答直到我们停止它的例子。

```
package main

import "time"
import "fmt"

func main() {

    // Tickers use a similar mechanism to timers: a
    // channel that is sent values. Here we'll use the
    // `range` builtin on the channel to iterate over
    // the values as they arrive every 500ms.
    ticker := time.NewTicker(500 * time.Millisecond)
    go func() {
        for t := range ticker.C {
            fmt.Println("Tick at", t)
        }
    }()

    // Tickers can be stopped like timers. Once a ticker
    // is stopped it won't receive any more values on its
    // channel. We'll stop ours after 1600ms.
    time.Sleep(1600 * time.Millisecond)
    ticker.Stop()
    fmt.Println("Ticker stopped")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 我们开始编码吧

首先，我们将创建一个名为 tasks 的新文件夹，在其中创建一个“request.go ”,其中包含执行搜索的代码。

```
// FILE: tasks/search.go

// These are the reasons which a request is invalid.
var (
    ErrExpired  = errors.New("request expired")
    ErrCanceled = errors.New("request canceled")
)

// RequestDriverTask is a simple struct that contains info about the user, request and driver, you can add more information if you want.
type RequestDriverTask struct {
    ID       string
    UserID   string
    Lat, Lng float64
    DriverID string
}

// NewRequestDriverTask create and return a pointer to RequestDriverTask
func NewRequestDriverTask(id, userID string, lat, lng float64) *RequestDriverTask {
    return &RequestDriverTask{
        ID:     id,
        UserID: userID,
        Lat:lat,
        Lng:lng,
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们实现了 **Run** 方法，这个 func 将从处理程序中启动。

```
// FILE: tasks/search.go

// Run is the function for executing the task, this task validating the request and launches another goroutine called 'doSearch' which does the search.
func (r *RequestDriverTask) Run() {
    // We create a new ticker with 30s time duration, this it means that each 30s the task executes the search for a driver.
    ticker := time.NewTicker(time.Second * 30)

    // With the done channel, we receive if the driver was found
    done := make(chan struct{})

    for {
        // The select statement lets a goroutine wait on multiple communication operations.
        select {
        case <-ticker.C:
            err := r.validateRequest()
            switch err {
            case nil:
                log.Println(fmt.Sprintf("Search Driver - Request %s for Lat: %f and Lng: %f", r.ID, r.Lat, r.Lng))
                go r.doSearch(done)
            case ErrExpired:
                // Notify to user that the request expired.
                sendInfo(r, "Sorry, we did not find any driver.")
                return
            case ErrCanceled:
                log.Printf("Request %s has been canceled. ", r.ID)
                return
            default: // defensive programming: expected the unexpected
                log.Printf("unexpected error: %v", err)
                return
            }

        case _, ok := <-done:
            if !ok {
                sendInfo(r, fmt.Sprintf("Driver %s found", r.DriverID))
                ticker.Stop()
                return
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们要为 RequestDriverTask 创建两个方法。

第一个方法是 **validateRequest** ，这个函数验证密钥，如果密钥是活动的，或者如果密钥过期，如果请求无效，它将返回一个原因之类的错误。

第二种方法是 **doSearch** ，这个函数使用我们的 RedisClient 及其函数 SearchDrivers 进行搜索。

```
// FILE: tasks/search.go

// validateRequest validates if the request is valid and return an error like a reason in case not.
func (r *RequestDriverTask) validateRequest() error {
    rClient := storages.GetRedisClient()
    keyValue, err := rClient.Get(r.ID).Result()
    if err != nil {
        // Request has been expired.
        return ErrExpired
    }

    isActive, _ := strconv.ParseBool(keyValue)
    if !isActive {
        // Request has been canceled.
        return ErrCanceled
    }

    return nil
}

// doSearch do search of driver and close to the channel.
func (r *RequestDriverTask) doSearch(done chan struct{}) {
    rClient := storages.GetRedisClient()
    drivers := rClient.SearchDrivers(1, r.Lat, r.Lng, 5)
    if len(drivers) == 1 {
        // Driver found
        // Remove driver location, we can send a message to the driver for that it does not send again its location to this service.
        rClient.RemoveDriverLocation(drivers[0].Name)
        r.DriverID = drivers[0].Name
        close(done)
    }

    return
} 
```

Enter fullscreen mode Exit fullscreen mode

函数 **sendInfo** 只是一个例子，如果你愿意，你可以实现另一个服务或推送通知或 WebSocket，我想写另一篇文章，其中我使用 FCM 实现了一个例子，我写了一个小库 [go-fcm](https://github.com/douglasmakey/go-fcm) 来通知用户。

```
// sendInfo this func is only example, you can use another services, websocket or push notification for send data to user.
func sendInfo(r *RequestDriverTask, message string) {
    log.Println("Message to user:", r.UserID)
    log.Println(message)
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经有了搜索任务的函数，现在我们需要为我们的服务创建新的端点，我们将在处理程序中创建一个名为“v2”的新文件夹，并在其中创建“search . go”

```
// FILE: handler/v2/search.go

func SearchV2(w http.ResponseWriter, r *http.Request) {
    rClient := storages.GetRedisClient()

    // We use Redis to keep a key unique for each request.
    // With this key also we will know if the request is active or if the user canceled the request.
    requestID, err := rClient.Incr("request_id").Result()
    if err != nil {
        return
    }
    key := strconv.Itoa(int(requestID))

    // Set true value for the key and also the expiration time, this expiration time is the duration that has the request to find a driver.
    rClient.Set(key, true, time.Minute*4)
    body := struct {
        Lat, Lng float64
    }{}

    if err := json.NewDecoder(r.Body).Decode(&body); err != nil {
        log.Printf("could not decode request: %v", err)
        http.Error(w, "could not decode request", http.StatusInternalServerError)
        return
    }

    // We create a new task and launch with a goroutine.
    rTask := tasks.NewRequestDriverTask(key, fmt.Sprintf("requestor_%s", key), body.Lat, body.Lng)
    go rTask.Run()

    // Return 200 and request_id
    w.WriteHeader(http.StatusOK)
    w.Write([]byte(fmt.Sprintf(`{"request_id": %s}`, key)))

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们为端点‘v2/cancel’创建处理程序来取消请求，因为如果用户不想等待搜索，它可以取消请求。

```
// FILE: handler/v2/search.go

func CancelRequest(w http.ResponseWriter, r *http.Request) {
    rClient := storages.GetRedisClient()

    body := struct {
        RequestID string `json:"request_id"`
    }{}

    if err := json.NewDecoder(r.Body).Decode(&body); err != nil {
        log.Printf("could not decode request: %v", err)
        http.Error(w, "could not decode request", http.StatusInternalServerError)
        return
    }

    rClient.Set(body.RequestID, false, time.Minute*1)
    w.WriteHeader(http.StatusOK)
    return

} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要在路由中添加新的端点。

```
// FILE: handler/base.go

import (
    "net/http"
    "github.com/douglasmakey/tracking/handler/v2"
)

func NewHandler() *http.ServeMux {
    mux := http.NewServeMux()
    mux.HandleFunc("/tracking", tracking)
    mux.HandleFunc("/search", search)

    //V2
    mux.HandleFunc("/v2/search", v2.SearchV2)
    mux.HandleFunc("/v2/cancel", v2.CancelRequest)
    return mux
} 
```

Enter fullscreen mode Exit fullscreen mode

# 举例

查找最近的司机

```
curl -i --header "Content-Type: application/json" --data '{"lat": -33.44262, "lng": -70.63054}' http://localhost:8000/v2/search

HTTP/1.1 200 OK
Date: Sat, 29 Sep 2018 15:33:48 GMT
Content-Length: 17
Content-Type: application/json

{"request_id": 1} 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果驱动程序没有接近客户端位置，并且超过了 4 分钟或我们设置的持续时间，请求将会过期，而不会找到任何驱动程序。

服务器日志

```
2018/09/30 01:57:53 Starting HTTP Server. Listening at ":8000"
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Search Driver - Request 1 for Lat: -33.442620 and Lng: -70.630540
Message to user:  requestor_1
Sorry, we did not find any driver. 
```

Enter fullscreen mode Exit fullscreen mode

我们将向“v2/search”发出另一个请求，但是在另一个终端中 1 分钟后，我们将驱动程序位置发送给服务。

```
// Another Terminal
curl -i --header "Content-Type: application/json" --data '{"id": "1", "lat": -33.44091, "lng": -70.6301}' http://localhost:8000/tracking 
```

Enter fullscreen mode Exit fullscreen mode

```
// Terminal with main
2018/09/30 02:12:03 Starting HTTP Server. Listening at ":8000"
2018/09/30 02:12:38 Search Driver - Request 2 for Lat: -33.442620 and Lng: -70.630540
2018/09/30 02:13:08 Search Driver - Request 2 for Lat: -33.442620 and Lng: -70.630540
2018/09/30 02:13:38 Search Driver - Request 2 for Lat: -33.442620 and Lng: -70.630540
2018/09/30 02:13:38 Message to user: requestor_2
2018/09/30 02:13:38 Driver 1 found 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们将向“v2/search”发出另一个请求，但这次我们将向“v2/cancel”发出请求，以取消订单，因为我们不能等待。

```
// Another Terminal
curl -i --header "Content-Type: application/json" --data '{"request_id": "3"}' http://localhost:8000/v2/cancel 
```

Enter fullscreen mode Exit fullscreen mode

```
// Terminal with main
2018/09/30 02:19:24 Starting HTTP Server. Listening at ":8000"
2018/09/30 02:19:56 Search Driver - Request 3 for Lat: -33.442620 and Lng: -70.630540
2018/09/30 02:19:56 Request 3 has been canceled. 
```

Enter fullscreen mode Exit fullscreen mode