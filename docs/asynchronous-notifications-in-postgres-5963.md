# Postgres 中的异步通知

> 原文：<https://dev.to/citizen428/asynchronous-notifications-in-postgres-5963>

我对 Postgres 很着迷:我对它了解得越多，就越意识到我还有多少不知道的。最近我发现了它的[异步通信能力](https://www.postgresql.org/docs/current/static/libpq-notify.html)，这显然已经存在很长时间了\ *(ツ)* /

让我们来看看与这个话题相关的两个最有趣的命令， [`NOTIFY`](https://www.postgresql.org/docs/10/static/sql-notify.html) 和 [`LISTEN`](https://www.postgresql.org/docs/10/static/sql-listen.html) 。以下是文档对它们的描述:

> NOTIFY 为访问同一个 PostgreSQL 数据库的一组进程提供了一个简单的进程间通信机制。有效负载字符串可以与通知一起发送，通过使用数据库中的表将附加数据从通知者传递到收听者，可以构建用于传递结构化数据的更高级机制。
> 
> 每当调用 NOTIFY channel 命令时，无论是由这个会话还是连接到同一数据库的另一个会话调用，当前在该通知信道上监听的所有会话都会得到通知，每个会话又会通知其连接的客户端应用程序。
> 
> LISTEN 将当前会话注册为名为 channel 的通知通道上的侦听器。如果当前会话已经注册为该通知通道的侦听器，则不执行任何操作。

听起来像是数据库级别的发布-订阅，有意思！我通过尝试和写代码学得最好，所以让我们开始吧。

### 为通知设置 Postgres

出于测试目的，让我们创建一个过于简化的`orders`表，除了主键之外，还包含一个电子邮件地址来标识下订单的人，以及一个`bigint`字段来存储以美分为单位的订单总额:

```
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,  
  email TEXT NOT NULL,
  total BIGINT NOT NULL
); 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要定义一个返回触发器的函数:

```
CREATE OR REPLACE FUNCTION notify_event() RETURNS TRIGGER AS $$
  DECLARE 
    record RECORD;  
    payload JSON;
  BEGIN
    IF (TG_OP = 'DELETE') THEN
      record = OLD;
    ELSE
      record = NEW;
    END IF;

    payload = json_build_object('table', TG_TABLE_NAME, 
                                'action', TG_OP, 
                                'data', row_to_json(record));

    PERFORM pg_notify('events', payload::text);

    RETURN NULL; 
  END;    
$$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

上面的内容非常简单:

1.  声明一些变量供以后使用。
2.  打开 [`TG_OP`特殊变量](https://www.postgresql.org/docs/current/static/plpgsql-trigger.html)来决定我们想要序列化哪一个版本的行。
3.  使用 [`json_build_object`和`row_to_json`](https://www.postgresql.org/docs/current/static/functions-json.html) 生成通知载荷。
4.  使用 [`pg_notify`](https://www.postgresql.org/docs/current/static/sql-notify.html) 在`events`频道广播消息。
5.  返回`NULL`，因为这是一个`AFTER`触发器。

现在我们可以创建一个`notify_order_event`触发器，它将在我们对`orders`表:
执行 CRUD 操作后调用这个函数

```
CREATE TRIGGER notify_order_event
AFTER INSERT OR UPDATE OR DELETE ON orders
  FOR EACH ROW EXECUTE PROCEDURE notify_event(); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，我们现在应该能够接收事件了。让我们通知 Postgres，我们对`events`通道上的通知感兴趣:

```
LISTEN events; 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们插入、更新或删除记录时，我们都会收到通知:

```
INSERT into orders (email, total) VALUES ('test@example.com', 10000);
INSERT 0 1
Asynchronous notification "events" with payload "{"table" : "orders", "action" : "INSERT", "data" : {"id":1,"email":"test@example.com","total":10000}}" received from server process with PID 5315. 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们刚刚收到了第一个异步通知，尽管不可否认这在同一个`psql`会话中不是特别有用，所以让我们添加另一个监听器。

### 监听另一个进程

在下面的例子中，我们将[再次](https://dev.to/citizen428/exploring-foreign-data-wrappers-in-postgres-36kp)使用杰里米·埃文的优秀的[续集宝石](https://github.com/jeremyevans/sequel) :

```
require 'sequel'

DB = Sequel.connect('postgres://user@localhost/notify-test')

puts 'Listening for DB events...'
DB.listen(:events, loop: true) do |_channel, _pid, payload| 
  puts payload
end 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码首先连接到数据库，然后使用 [`Sequel::Postgres::Database#listen`](https://www.rubydoc.info/gems/sequel/4.38.0/Sequel%2FPostgres%2FDatabase:listen) 在循环中监听事件。

如果我们启动这个脚本并在数据库中插入一条记录，JSON 有效负载将输出到控制台:

```
→ ruby test.rb
Listening for DB events...
{"table" : "orders", "action" : "INSERT", "data" : {"id":2,"email":"test@example.com","total":10000}} 
```

Enter fullscreen mode Exit fullscreen mode

不错，但仍然不是非常有用。如果我们可以将这个通知转发到我们的 web 浏览器来构建一个仪表板之类的东西，这不是很好吗？输入 WebSockets。

### 连接一个前端

由于这只是一个简单的演示，而不是一个成熟的应用程序，我觉得 Rails + ActionCable 有点过头了，所以我决定用 [Faye](https://github.com/faye/faye) 代替
写一个非常简单的 [Rack](https://rack.github.io) 应用程序

```
require 'faye/websocket'
require 'sequel'

DB = Sequel.connect('postgres://user@localhost/notify-test')

App = lambda do |env|
  ws = Faye::WebSocket.new(env)

  DB.listen(:events, loop: true) do |_channel, _pid, payload|
    ws.send(payload)
    ws.rack_response
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

像每个 Rack“app”一样，这只是一个支持`call`的对象，在我们的例子中是一个`lambda`。在这里，我们创建了一个 websocket，用于将从 Postgres 收到的消息转发到浏览器。要在那里接收它们，我们需要做的就是用一个将有效负载记录到控制台的`onmessage`处理程序来实例化一个 [`Websocket`](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)

```
socket = new WebSocket("ws://localhost:9292")
socket.onmessage = (event) => console.log(event.data) 
```

Enter fullscreen mode Exit fullscreen mode

每当我们现在在`orders`表中创建、修改或删除新记录时，浏览器都会通知我们:

[![Chrome console](img/c550afb1003abf989978974ec8e428ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2IE5QgVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y0xdf9il1hztda3gwy6y.png)

### 总结

Postgres' [`LISTEN`](https://www.postgresql.org/docs/10/static/sql-listen.html) 和 [`NOTIFY`](https://www.postgresql.org/docs/10/static/sql-notify.html) 通过发布-订阅模式提供了简单的进程间通信。这种机制有许多潜在的用例，从 ETL 日志到实时仪表板。