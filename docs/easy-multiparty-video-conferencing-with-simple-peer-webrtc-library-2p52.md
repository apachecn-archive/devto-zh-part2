# 简单的多方视频会议，4 个步骤

> 原文：<https://dev.to/lucpattyn/easy-multiparty-video-conferencing-with-simple-peer-webrtc-library-2p52>

simple-peer 是一个优秀的库，它使得开发 WebRTC 解决方案变得轻而易举。它最棒的地方在于它巧妙地隐藏了所有的复杂性，并为基于 WebRTC 的视频会议和数据传输实现提供了一个易于使用的接口。因为屏幕共享几乎与传递视频数据相同，所以您也可以将它用于屏幕投射。

让我把它分成几个简单的步骤:

**第一步**

从[https://github.com/feross/simple-peer](https://github.com/feross/simple-peer)获得 simplepeer.min.js

并包含在 html 文件中。

**第二步**

找到一个 websocket 库，用于来回传递数据以进行初始通信。在我的例子中，我使用了 easy RTC([https://github.com/priologic/easyrtc](https://github.com/priologic/easyrtc))

**第三步**

围绕 websockets 层编写一个包装器，以便在主代码中不会暴露套接字库。这样，您可以随时更改内部 websocket 库，而无需更改用于 WebRTC 通信建立的代码。

这是我想到的一个叫做 hub (hub.js)的包装器。在 hub.js 中。on 方法用于侦听自定义/用户定义的事件。每当一个新的对等体加入时，我抛出带有对等体 id 的 app.peer 事件(步骤 4 中的例子)。类似地，内部 websocket 库调用了一些其他事件，这些事件会及时传递给 hub。

```

var hub = { 

    msgMap: {}, 

    connect:function connect(url){
        easyrtcConnect(url);
        },

    send: function(peerid, msgType, content){
        easyrtc.sendDataWS(peerid, msgType,  content);

    },

    sendToAll: function(msgType, content){
        easyrtc.sendDataWS({targetRoom:"default"}, msgType, content);

    },

    on: function(type, callback){
        this.msgMap[type] = callback;
    },

    event: function(peerid, type, msg){
        var callback = this.msgMap[type];
        if(callback){
            callback(peerid, msg);
        };      
    },

    peerMap: {},

    setPeer: function(peerid, peer){
        this.peerMap[peerid] = peer;
    },

    getPeer: function(peerid){
        return this.peerMap[peerid];
    },

    removePeer: function(peerid){
        delete this.peerMap[peerid];
    },

    iteratePeers: function(callback){
        var value;
        for (var key in this.peerMap) {
                value = this.peerMap[key];
                callback(key, value);
        }
    }

};

```

**第四步**

如前所述，当一个新的对等体/用户从内层加入(即上线/连接到套接字)时，我会通知集线器。
示例:hub.event(easyrtcid，“app.peer”，userid)；

为了进行多方会议，最好的方法是让最新的在线用户在登录时向所有其他用户发送问候消息。当我加入的时候，easyrtc 库给了我一个在线的联系人列表。从内层内部，我抛出带有 easyrtc id(即 socket.id)的 app.peer 事件。对于多个连接(读取用户)，会多次引发 hub.event(easyrtcid，“app.peer”，userid)。

```

hub.on('app.peer', function(peerid, userid){

    hub.send(peerid, 'hello'); // sending hello

});

```

打个比方，会产生这样的结果

```

-----------------------|- HELLO -> User A
User D-->Sends-->------|- HELLO -> User B
-----------------------|- HELLO -> User C

```

一旦有呼叫请求，用户默认接受。

接受时，我们创建用于 WebRTC 连接的 SimplePeer 对象。请浏览 simple-peer 的文档以了解发起和事件。

```
hub.on('hello', function (peerid, msg) {        

    var peer = new SimplePeer({ initiator: false, stream: localStream });       

    hub.setPeer(peerid, peer);

    peer.on('signal', function (data) {

        hub.send(peerid, 'signal', data);       

    });

    peerCreated(peerid, peer);

    hub.send(peerid, 'ack', '1'); // sending acknowledgement

});

```

请注意，上面的事件处理程序用于接收新连接用户的问候，作为回报，我们发送一个“ack”。

你好用户 A -发送确认->用户 D

你好用户 B -发送确认->用户 D

你好用户 C -发送确认->用户 D

剩下的就很简单了，当发起通信的用户收到 ack 时，他/她就形成一个 SimplePeer 对象作为发起者。当简单对等库用它的信号事件:peer.on('signal ')优雅地通知时，剩下的就是将 SDP Offer 信息传递给另一方..).

```
hub.on('ack', function (peerid, msg) {

    //debugger;

    if(msg == "1"){

        var peer = new SimplePeer({ initiator: true, stream: localStream});

        hub.setPeer(peerid, peer);  

        peer.on('signal', function (data) {
            hub.send(peerid, 'signal', data);
        });

        peerCreated(peerid, peer);
    }
});

```

在 peerCreated 方法内部，我们按照简单对等文档进行必要的度量。

```
function peerCreated(peerid, peer){

    peer.peerid = peerid; // you can choose to skip this

    //debugger;

    peer.on('connect', function () {
        console.log('CONNECT')
        peer.send('call established .. ' + selfID);
    });

    peer.on('error', function (err) { console.log('error', err) });

    peer.on('data', function (data) {
        console.log('data: ' + data)
    });

    peer.on('stream', function (stream) {
        console.log('new stream arrived .. ', this.peerid); 

        createRemoteVideoElement(peerid, stream);

    });

    peer.on('track', function (track, stream) {
        console.log('new track arrived .. ', this.peerid);  

        createRemoteVideoTrackElement(peerid, track, stream);

    });

    peer.on('removestream', function (stream) {
        //removeRemoteVideoElement(peerid); 
        console.log("stream removed .. ", peerid); // hardly called

    });

    peer.on('close', function () {
        console.log("connection closed .. ", peerid);
        removeRemoteVideoElement(peerid);

    });

}

```

然而，为了开始程序，我们需要获取摄像机和麦克风访问(用户媒体流)并将其存储在 localStream 变量中:

```
// get video/voice stream
navigator.getUserMedia({ video: true, audio: true }, gotMedia, function () {})

// This method starts of proceedings
function gotMedia (ownstream) {

    localStream = ownstream;

    connectToSocket(); // once socket connects we receive "app.peer" on hub

    var video = document.getElementById('me');
        video.srcObject = ownstream;
        video.play();

}

```

暂时就这些了！