# 正在实现 json-rpc

> 原文：<https://dev.to/yorodm/implementando-json-rpc-en-go-51jn>

# 实施 JSON-RPC 进行中

**JSON-RPC** 是我最喜欢的与[odo](http://www.odoo.com)通信的方法，这几天我正在尝试从 [Go](http://golang.org) 中开发的服务向 **ERP** 发送信息

答案是 32 分钟(我不得不阅读许多文件)。

```
import (
    "errors"
    "fmt"
    "io/ioutil"
    "net/http"
    "net/rpc"
    "net/url"
    "reflect"
)

type JsonCodec struct {
    url *url.URL
    httpClient *http.Client
    responses map[uint64]*http.Response
    response *Response
    ready chan uint64
}

func (self *JsonCodec) WriteRequest(req *rpc.Request, args interface{}) error {
    request, err := NewRequest(self.url.String(), req.ServiceMethod, req.Seq, args)
    if err != nil {
        return err
    }

    response, err := self.httpClient.Do(request)
    if err != nil {
        return err
    }
    self.responses[req.Seq] = response
    self.ready <- req.Seq
    return nil
}

func (self *JsonCodec) ReadResponseHeader(resp *rpc.Response) error {
    seq := <-self.ready
    response := self.responses[seq]
    if response.StatusCode < 200 || response.StatusCode >= 300 {
        return fmt.Errorf("request error: %d", response.StatusCode)
    }
    data, err := ioutil.ReadAll(response.Body)
    if err != nil {
        return err
    }
    _ = response.Body.Close()
    r, err := NewResponse(data)
    if err != nil {
        return err
    }
    if r.Failed() {
        resp.Error = fmt.Sprintf("%v", r.Err())
    }
    self.response = r
    resp.Seq = seq
    delete(self.responses, seq)
    return nil
}

func (self *JsonCodec) ReadResponseBody(v interface{}) error {
    if v == nil {
        return nil
    }
    value := reflect.ValueOf(v)
    if value.Kind() != reflect.Ptr || value.IsNil() {
        return errors.New("Called with non ptr or nil")
    }
    value = value.Elem()
    value.Set(reflect.ValueOf(*self.response))
    return nil
}

func (self *JsonCodec) Close() error {
    transport := self.httpClient.Transport.(*http.Transport)
    transport.CloseIdleConnections()
    return nil
} 
```

如您所见，我使用标准库创建了一个新的编解码器，可以与 **net/rpc** 配合使用。

```
codec := &JsonCodec{
        url: u, /net.url.Url
        httpClient: httpClient, // net.http.Client
        ready: make(chan uint64),
        responses: make(map[uint64]*http.Response),
    }
client := rpc.NewClientWithCodec(codec) 
```

实施的简单性很大程度上反映了界面在 go 中的处理方式。如果你不熟悉这个主题，我建议你阅读。

1.  [举例:接口](https://gobyexample.com/interfaces)
2.  [接口的问题，以及如何解决它](https://dev.to/deanveloper/the-problem-with-interfaces-and-how-go-fixed-it)
3.  [哈金围棋界面](https://dev.to/loderunner/hacking-go-interfaces)