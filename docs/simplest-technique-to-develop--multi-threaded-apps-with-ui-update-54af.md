# 利用 UI 更新开发多线程应用的最简单技术

> 原文：<https://dev.to/lucpattyn/simplest-technique-to-develop--multi-threaded-apps-with-ui-update-54af>

## 介绍

虽然没有线程操作的简单应用程序可以很容易地在 iOS/Android 系统(或桌面)上开发，但当线程参与进来时，事情就变得模糊了。那么我们该如何应对呢？

## 背景

我需要开发的大多数应用程序都必须处理多线程，这些年来，我学到了一些简单的技术(当你从错误中学习时，这很简单),我想分享一些用于应用程序开发的技术。

假设我通过套接字从服务器接收到一些即时通知消息，我需要在 UI 页面中显示这些消息。

因为通知是通过套接字发出的，所以很可能发生在后台线程中。当我们通过套接字接收数据时，经常会创建不止一个线程。

大多数初学者会遇到更新 GUI 和保存他们收到的通知中的数据的问题，这使得应用程序处于非常不稳定的状态。

因此，在着手解决这个问题时，有三件关键的事情要记住:

1.  在后台读取/获取/接收数据，以确保应用程序不会挂起/等待/冻结传入的数据。无论如何，套接字通知通常是通过后台线程发出的。

2.  以短脉冲在前台线程中写入/存储数据。通常向应用程序的 UI/主线程发送请求来运行存储操作会自动处理线程同步。在昂贵的操作的情况下，使用排队服务。

3.  在主/ui 线程中反映 gui 的变化。

通常，如果我们不需要存储太多信息或更新太多模型，我们可以简单地向系统发送一个请求，一旦我们在后台线程中接收到数据，就在主/UI 线程中运行下一组操作。

在我们的例子中，如果我们在 onMessage 事件中收到服务器通知消息，在事件处理程序中，我们可以使用如下代码(取决于 android/ios):

```
    runOnUIThread/dispatch_get_main() 

```

更新模型/数据库以及用户界面

伪代码示例:

```
void onNewMessage(message){

    runOnUIThread->{ // this usually takes care of the thread syncronization
        db.insertNewMessage(message);
        updateAllViews();
    }
}

```

现在，如果您觉得 db/model 操作会变得很昂贵，那么您可以在后台实现一个队列服务，以便在 DB/model 中插入/更新频繁传入的消息。需要在前台实现从队列中获取消息的服务，以便相应地更新 GUI。为了简单起见，您可以使用来自队列服务的事件通知(以及相关信息),请求主线程更新 GUI。

在这种情况下，伪代码示例如下所示:

```
void onNewMessage(message){

    QService.insert(message);

}

QService.onFinishProcessingMessage(message){
    // the UI page/element might register as an observer into the event service
    EventService.notifyObservers("event_message_processed", message); 

}

uiPage.on("event_message_processed", function(message){

     // most likely the event notification would come in a background thread,
     // so invoke running in main thread for GUI operations/update
    runOnUIThread->{
        updateUI();
    }

});

```

编码快乐！

## 历史

文章上传时间:2018 年 11 月 15 日