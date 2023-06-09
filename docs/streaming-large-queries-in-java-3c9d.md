# Java 中的流式大型查询

> 原文：<https://dev.to/chochos/streaming-large-queries-in-java-3c9d>

我从 1.0 版本开始就一直使用`JdbcTemplate`类，它已经发展得很好了，但是我希望在第 5 版中，它能包含一些针对大结果查询的流功能。唉，这并没有发生。

尽管如此，有时我需要执行返回数百万行的查询，并且我不能使用返回列表的 JdbcTemplate 方法。对它来说 A `RowCallbackHandler`是完美的，但是如果只是接收一个流就更好了，不是吗？尤其是如果您有自定义的行映射器...

因此，我决定编写自己的流生成器来使用 JdbcTemplate。在这个过程中，我最终创建了一个更通用的流生成器，我认为这很好，所以我想与任何需要类似东西的人分享。不过，我认为这对于一个图书馆来说还不够，所以我决定写一篇关于它的帖子。

## 挑战

首先，我们需要考虑流是懒惰的，当您获得一个流并定义要在其上完成的操作时，还没有发生任何事情，直到您实现一个最终操作，该操作需要实际遍历元素并在其上应用操作。有遍历整个流的操作(比如计数，或者将元素收集到另一个集合中)，也有短路操作(比如确定是否有任何元素通过了某些过滤器)。

所以我们想要得到一个流，并在其上定义操作，但是什么也没有发生，直到流需要被遍历，*然后*查询需要被运行(这意味着有一个到数据库的打开的连接)。如果发生了不好的事情，查询需要停止(JdbcTemplate 将负责清理连接和其他资源)。

我发现我可以做到这一点的唯一方法是使用两个线程:一个生产者线程在其中运行查询并将行以某种方式提供给流，另一个消费者线程是流的读取器。

我们需要一个缓冲区，生产者可以在其中存储元素，消费者可以从中获取元素。LinkedBlockingQueue 似乎非常适合这种情况。

所以，事不宜迟，这就是:

```
 public static <T> Stream<T> streamForQuery(int bufferSize, T endOfStreamMarker,
                                               Consumer<Consumer<T>> query) {
        final LinkedBlockingQueue<T> queue = new LinkedBlockingQueue<>(bufferSize);
        //This is the consumer that is usually passed to queries;
        //it will receive each item from the query and put it in the queue
        Consumer<T> filler = t -> {
            try {
                //Try to add to the queue, waiting up to 1 second
                //Honestly if after 1 second the queue is still full, either the stream consumer
                //needs some serious optimization or, more likely, a short-circuit terminal
                //operation was performed on the stream.
                if (!queue.offer(t, 1, TimeUnit.SECONDS)) {
                    //If the queue is full after 1 second, time out.
                    //Throw an exception to stop the producer queue.
                    log.error("Timeoud waiting to feed elements to stream");
                    throw new BufferOverflowException();
                }
            } catch (InterruptedException ex) {
                System.err.println("Interrupted trying to add item to stream");
                ex.printStackTrace();
            }
        };
        //For the stream that we return, we use a Spliterator.
        return StreamSupport.stream(() -> new Spliterators.AbstractSpliterator<T>(Long.MAX_VALUE, Spliterator.ORDERED) {
            //We need to know if the producer thread has been started
            private boolean started = false;
            //If there's an exception in the producer, keep it here
            private volatile Throwable boom;
            /** This method is called once, before advancing to the first element.
             * It will start the producer thread, which runs the query, passing it our
             * queue filler.
             */
            private void startProducer() {
                //Get the consumer thread
                Thread interruptMe = Thread.currentThread();
                //First time this is called it will run the query in a separate thread
                //This is the producer thread
                new Thread(() -> {
                    try {
                        //Run the query, with our special consumer
                        query.accept(filler);
                    } catch (BufferOverflowException ignore) {
                        //The filler threw this, means the queue is not being consumed fast enough
                        //(or, more likely, not at all)
                    } catch (Throwable thr) {
                        //Something bad happened, store the exception and interrupt the reader
                        boom = thr;
                        interruptMe.interrupt();
                    }
                }).start();
                started = true;
            }
            @Override
            public boolean tryAdvance(Consumer<? super T> action) {
                if (!started) {
                    startProducer();
                }
                try {
                    //Take an item from the queue and if it's not the end of stream maker, pass it
                    //to the action consumer.
                    T t = queue.take();
                    if (t != endOfStreamMarker) {
                        action.accept(t);
                        return true;
                    }
                } catch (InterruptedException ex) {
                    if (boom == null) {
                        System.err.println("Interrupted reading from stream");
                        ex.printStackTrace();
                    } else {
                        //Throw the exception from the producer on the consumer side
                        throw new RuntimeException(boom);
                    }
                }
                return false;
            }
        }, Spliterator.IMMUTABLE, false);
    } 
```

这是您使用 JdbcTemplate 的方法:

```
final MyRow marker = new MyRow();
Stream<MyRow> stream = streamForQuery(100, marker, callback -> {
    //Pass a RowCallbackHandler that passes a MyRow to the callback
    jdbcTemplate.query("SELECT * FROM really_big_table_with_millions_of_rows",
                       rs -> { callback.accept(myRowMapper.mapRow(rs, 0)); }
    );
    //Pass the marker to the callback, to signal end of stream
    callback.accept(marker);
}); 
```

此时，查询尚未执行。您可以做以下事情:

`stream = stream.filter(row -> row.isPretty());`

仍然什么都没发生。当你做这样的事情时:

`Optional<MyRow> row = stream.skip(100_000).limit(1000).findAny();`

然后执行查询，将读取前十万行(并跳过)，每一行都将通过过滤器，直到有一行或一千行被读取。

## 拥有巨大的力量...

请，请，请不要用它来代替一个好的 WHERE 子句和正确地索引你的表。我主要使用这个东西来生成报告，通过将元素映射到一个公共类型来连接不相交类型的流，以便进一步处理(基本上，弥补了 Java 中联合类型的不足)。

话虽如此，能够以流的方式从数据库中读取行还是很酷的。我猜这可以集成到 Spring 的 JdbcTemplate 和/或 jOOQ 中...