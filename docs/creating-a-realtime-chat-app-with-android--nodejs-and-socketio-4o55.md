# 使用 android、NodeJs 和 Socket.io 创建实时聊天应用

> 原文：<https://dev.to/medaymentn/creating-a-realtime-chat-app-with-android--nodejs-and-socketio-4o55>

# 简介

WebSockets 是非常漂亮的工具，它允许我们在现代 web 应用程序中建立实时通信。事实上，这个机制是如此强大，它被用来建立不同类型的应用程序，如实时聊天或通知系统等..

在本文中，我们将向您展示如何使用 android nodeJs 和 Socket.io 构建一个实时聊天应用程序

# 入门

我们的聊天应用分为两部分:

1-服务器端:一个节点 js 服务器，实现了 socket.io for server

2-客户端:创建 android 应用程序并为客户端实现 socket.io

# 本方 NodeJs 服务器

嗯，为了让事情更清楚，我们的项目架构将由两个文件组成:
package.json，它将处理我们的 node js 应用程序和 index.js 的所有依赖关系，index . js 将是我们的主服务器。

创建这两个文件后，我们打开项目
目录下的命令行并执行这个命令

```
npm install --save  express socket.io 
```

现在，在我们的 index.js 文件中，我们将构建我们的服务器并进行所有配置，使其看起来像这样

```
const express = require('express'),
http = require('http'),
app = express(),
server = http.createServer(app),
io = require('socket.io').listen(server);
app.get('/', (req, res) => {

res.send('Chat Server is running on port 3000')
});

server.listen(3000,()=>{

console.log('Node app is running on port 3000')

}); 
```

要确保我们的服务器正在运行，请转到我们的项目目录下的命令行并执行以下命令

```
node index.js 
```

注意:使用 node 命令，我们可以运行任何使用 node 环境创建的服务器，但问题是我们每次更新 index.js 文件时都必须运行相同的命令，所以为了使事情更简单，我们可以使用 nodemon 命令，它会在我们每次进行更改时自动重启我们的服务器

因此，要安装 nodemon，请转到命令行并运行

```
npm install -g nodemon 
```

为了确保我们的项目正在运行，我们应该在控制台中看到这个日志

[![Alt text of image](../Images/62801e16bdffba27e1a6fdb93de2b488.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---Ge1OBrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQFdAsL44d-78A/article-inline_image-shrink_1000_1488/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DvO5wrxNPBaq_Qg6CkVaJNmyER_-bvO7icETjrdLPd_o)

最精彩的部分来了！！

我们现在将尝试在我们的服务器中实现一些 socket.io 方法来处理我们聊天应用程序的所有事件，包括用户连接状态和消息。

在 index.js 文件中，我们添加了第一个实现，它将检测是否有用户连接到我们的服务器

```
io.on('connection', (socket) => {

console.log('user connected')

socket.on('join', function(userNickname) {

        console.log(userNickname +" : has joined the chat "  )

        socket.broadcast.emit('userjoinedthechat',userNickname +" : has joined the chat ")
    });

}); 
```

实际上，socket.io 机制是基于监听和触发事件的，在第一个实现中，我们使用了带两个参数的(on)方法(' eventname '，callback)，定义了一个监听名为 connection 的事件的监听器，该事件将从客户端触发，以便节点 js 可以处理它，之后我们定义了一个方法，该方法将监听名为' join '的发出事件，并将记录已加入控制台聊天的用户的姓名。

现在，当节点 js 检测到一个用户时，它使用 emit 方法向客户端触发一个名为“userjoinedthechat”的事件，注意 socket.broadcast.emit 会将事件发送给连接到服务器的每个用户，除了发送者。

如果我们想将消息发送给包括发送者在内的所有用户，我们只需使用 io.emit()而不是 socket.emit()。

现在，为了处理消息，我们添加了这几行，我们可以看到，我们为回调函数添加了额外的参数，即用户昵称和消息内容，实际上，这些信息将在触发事件“messagedetection”时从客户端发送

```
 socket.on('messagedetection', (senderNickname,messageContent) => {

       //log the message in console 

       console.log(senderNickname+" :" +messageContent)
        //create a message object

      let  message = {"message":messageContent, "senderNickname":senderNickname}

// send the message to the client side 

       socket.emit('message', message )

      }); 
```

最后，当用户从客户端断开连接时，该事件将由这个实现来处理

```
 socket.on('disconnect', function() {
    console.log( 'user has left ')
    socket.broadcast.emit( "userdisconnect" ,' user has left')

}); 
```

现在我们的服务器已经准备好了，index.js 文件应该是这样的

```
const express = require('express'),
http = require('http'),
app = express(),
server = http.createServer(app),
io = require('socket.io').listen(server);
app.get('/', (req, res) => {

res.send('Chat Server is running on port 3000')
});
io.on('connection', (socket) => {

console.log('user connected')

socket.on('join', function(userNickname) {

        console.log(userNickname +" : has joined the chat "  );

        socket.broadcast.emit('userjoinedthechat',userNickname +" : has joined the chat ");
    })

socket.on('messagedetection', (senderNickname,messageContent) => {

       //log the message in console 

       console.log(senderNickname+" : " +messageContent)

      //create a message object 

      let  message = {"message":messageContent, "senderNickname":senderNickname}

       // send the message to all users including the sender  using io.emit() 

      io.emit('message', message )

      })

socket.on('disconnect', function() {

        console.log(userNickname +' has left ')

        socket.broadcast.emit( "userdisconnect" ,' user has left')

    })

})

server.listen(3000,()=>{

console.log('Node app is running on port 3000')

}) 
```

# 我们的安卓应用(Socket 客户端)

要开始打开 android studio 并创建一个包含空活动新项目，请打开 app build.gradle 文件并添加这些依赖项，然后同步您的项目。

```
compile 'com.android.support:recyclerview-v7:25.3.1'
compile('com.github.nkzawa:socket.io-client:0.5.0') {
    exclude group: 'org.json', module: 'json'
} 
```

现在说说这几行:

第一个是 recycler 视图，我们将使用它来显示我们的消息列表，第二个是库，它将为我们提供客户端的 socket.io 实现，以便我们可以触发或侦听事件。

不要忘记在 manifest.xml 中启用 INTERNET 权限

```
<uses-permission android:name="android.permission.INTERNET" ></uses-permission> 
```

在 activity_main.xml 中，我们将为用户添加一个用于输入昵称的编辑文本和一个允许用户进入聊天框的按钮

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"xmlns:app="http://schemas.android.com/apk/res-auto"xmlns:tools="http://schemas.android.com/tools"android:layout_width="match_parent"android:layout_height="match_parent"tools:context="com.example.aymen.androidchat.MainActivity">

   <EditText 
      android:id="@+id/nickname"android:layout_centerInParent="true"android:textSize="30dp"android:hint="Enter your nickname !"android:layout_width="match_parent"android:layout_height="wrap_content" /><Buttonandroid:layout_below="@+id/nickname"android:id="@+id/enterchat"android:text="Go to chat "android:layout_width="match_parent"android:layout_height="wrap_content" />

 </RelativeLayout> 
```

这样预览就会变成这样
[![Alt text of image](../Images/66a8e289b9e0305fcafc24bd47d429e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y5AxBC88--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQG0VWYYJnDL0w/article-inline_image-shrink_400_744/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DNEWFM18SAsIOPcSkMVlLJj_cK4oH3ILSdZSaFohrIDk)

现在你的 MainActivity.java 应该是这样的

```
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class MainActivity extends AppCompatActivity {

    private Button btn;
    private EditText nickname;
    public static final String NICKNAME = "usernickname";
    @Overrideprotected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //call UI components  by id
        btn = (Button)findViewById(R.id.enterchat) ;
        nickname = (EditText) findViewById(R.id.nickname);

        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //if the nickname is not empty go to chatbox activity and add the nickname to the intent extra

    if(!nickname.getText().toString().isEmpty()){

              Intent i  = new Intent(MainActivity.this,ChatBoxActivity.class);

                     //retreive nickname from EditText and add it to intent extra
                     i.putExtra(NICKNAME,nickname.getText().toString());

                     startActivity(i);
                 }
            }
        });

    }
} 
```

现在创建第二个名为 ChatBoxActivity 的空活动，并在 activity_chat_box.xml 中添加以下行

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"xmlns:app="http://schemas.android.com/apk/res-auto"xmlns:tools="http://schemas.android.com/tools"android:layout_width="match_parent"android:layout_height="match_parent"tools:context="com.example.aymen.androidchat.ChatBoxActivity">
<LinearLayout
android:weightSum="3"
android:orientation="vertical"
android:layout_width="match_parent"
android:layout_height="match_parent">
<android.support.v7.widget.RecyclerViewandroid:layout_weight="3"android:id="@+id/messagelist"android:layout_width="match_parent"android:layout_height="wrap_content"android:clipToPadding="false"android:scrollbars="vertical"/><Viewandroid:layout_marginTop="5mm"android:id="@+id/separator"android:layout_width="match_parent"android:layout_height="1dp"android:background="@android:color/darker_gray"/>

<LinearLayoutandroid:weightSum="3"android:orientation="horizontal"android:layout_width="match_parent"android:layout_height="wrap_content">

            <EditText

                android:id="@+id/message"android:layout_weight="3"android:layout_width="wrap_content"android:hint="your message"

                android:layout_height="match_parent" />

            <Button

                android:id="@+id/send"android:layout_width="wrap_content"android:layout_height="wrap_content"android:background="#00000000"android:text="send"
                /></LinearLayout>

    </LinearLayout>
</RelativeLayout> 
```

你的预览应该是这样的
[![Alt text of image](../Images/d1e4519ce8f0a3c9dad1b8287b60a5f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aiRMF5nc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQGJ_hXK26bhAg/article-inline_image-shrink_1500_2232/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DCwpgd_YPmFM-THSZWuSgE1uiCR6Aa3WtBWD0kzxFkaw)

现在，在实现 socket 客户端之前，我们应该创建一个适配器来处理和显示我们的消息，为此我们需要创建一个名为 item.xml 的文件和一个名为 message 的 java 类，它们有两个简单的字符串属性(昵称、消息)。

在我们的项目目录中，与 activites 一起创建一个名为 Message.java 的文件:

```
public class Message {

    private String nickname; 
    private String message ;

    public  Message(){

    }
    public Message(String nickname, String message) {
        this.nickname = nickname;
        this.message = message;
    }

    public String getNickname() {
        return nickname;
    }

    public void setNickname(String nickname) {
        this.nickname = nickname;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
} 
```

现在，在布局目录下创建一个名为 item.xml 的文件，并添加以下几行

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"android:orientation="horizontal" android:layout_width="match_parent"android:layout_height="match_parent"><TextViewandroid:id="@id/nickname"android:textSize="15dp"android:textStyle="bold"android:text="Nickname : "android:layout_width="wrap_content"android:layout_height="wrap_content" /><TextViewandroid:id="@id/message"android:textSize="15dp"android:text=" message "android:layout_width="wrap_content"android:layout_height="wrap_content" />
</LinearLayout> 
```

创建一个名为 ChatBoxAdapter.java 的文件，将这几行

```
package com.example.aymen.androidchat;

import android.support.annotation.NonNull;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.List;

public class ChatBoxAdapter  extends RecyclerView.Adapter<ChatBoxAdapter.MyViewHolder> {
    private List<Message> MessageList;

    public  class MyViewHolder extends RecyclerView.ViewHolder {
        public TextView nickname;
        public TextView message;

        public MyViewHolder(View view) {
            super(view);

            nickname = (TextView) view.findViewById(R.id.nickname);
            message = (TextView) view.findViewById(R.id.message);

        }
    }
// in this adaper constructor we add the list of messages as a parameter so that 
// we will passe  it when making an instance of the adapter object in our activity 

public ChatBoxAdapter(List<Message>MessagesList) {

        this.MessageList = MessagesList;

    }

    @Overridepublic int getItemCount() {
        return MessageList.size();
    }
    @Overridepublic ChatBoxAdapter.MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View itemView = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item, parent, false);

        return new ChatBoxAdapter.MyViewHolder(itemView);
    }

    @Overridepublic void onBindViewHolder(final ChatBoxAdapter.MyViewHolder holder, final int position) {

 //binding the data from our ArrayList of object to the item.xml using the viewholder 

        Message m = MessageList.get(position);
        holder.nickname.setText(m.getNickname());

        holder.message.setText(m.getMessage() );

    }

} 
```

现在，一切设置就绪，我们可以在我们的 ChatBoxActivity.java 中实现 socket 客户端了，下面是我们的下一步计划:

1.从 intent extra 中获取用户的昵称

2 .调用并实现与回收器视图相关的所有方法，包括适配器实例化

3 .声明并定义 socket 客户端与服务器连接的主机

4 .处理从服务器触发的所有事件

5 .当用户连接、断开或发送消息时发出事件

但在此之前，让我们检查一下是否一切正常，所以在我们的 ChatBoxActivity 中，我们将声明 socket 对象，并在 onCreate 方法中添加 socket 连接，这样当调用活动时，socket 客户端将直接触发事件连接

```
public class ChatBoxActivity extends AppCompatActivity {

    //declare socket object

private Socket socket;
private String Nickname ;

@Overrideprotected 
void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chat_box);

// get the nickame of the user

  Nickname= (String)getIntent().getExtras().getString(MainActivity.NICKNAME);

//connect you socket client to the server

try {

//if you are using a phone device you should connect to same local network as your laptop and disable your pubic firewall as well 

 socket = IO.socket("http://yourlocalIPaddress:3000");

 //create connection 

socket.connect()

// emit the event join along side with the nickname

socket.emit('join',Nickname); 

        } catch (URISyntaxException e) {
            e.printStackTrace();

        }

    }
} 
```

现在运行您的模拟器，在第一个活动中输入昵称，然后单击“go to chat”。您将在服务器控制台中看到一个日志，指示用户已成功与服务器建立连接，我们可以看到服务器中触发事件 join 的侦听器工作正常，它记录了连接的用户的名称

[![Alt text of image](../Images/d0f27b8838e0938f933b4b8d1effe6d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p3128Yfn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQHYt9jcKTKY6Q/article-inline_image-shrink_400_744/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DG2yf2Q1kOXuGVQff0628QPD_zbTA8bakOVYB2r1TkXc)

现在一切正常，我们不应该忘记，当我们的服务器处理一个事件时，它还会广播其他自定义事件，因此这些被触发的事件应该在客户端处理，为此我们将为事件“userjoinedthechat”创建第一个侦听器，这是一个在服务器处理事件“join”时触发的自定义事件。

在我们的 ChatBoxActivity 中，我们将添加这些行

```
socket.on("userjoinedthechat", new Emitter.Listener() {
    @Overridepublic void call(final Object... args) {
        runOnUiThread(new Runnable() {
            @Overridepublic void run() {
                String data = (String) args[0];
                // get the extra data from the fired event and display a toast 
                Toast.makeText(ChatBoxActivity.this,data,Toast.LENGTH_SHORT).show();

            }
        });
    } 
```

现在，我们同时运行 2 个模拟器，我们从两边输入两个不同的昵称，我们可以看到，两个模拟器中的一个表示用户已经成功加入聊天

[![Alt text of image](../Images/101c7916296a26ca18cfdbcf01075e42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXYRowKy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQEq9HweRGzHHQ/article-inline_image-shrink_1500_2232/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DL5gNhWdtq7TBjvlZa0y7YqMTwbqyUgBrJF1qI4JT0vc)

现在是我们应用程序最好的部分，也就是聊天信息:

要显示消息，我们必须这样进行

1 .将 onclickListener 添加到按钮 send 中，并从 EditText 中获取消息内容，然后使用 emit()方法发出事件“messagedetection ”,并附带发送者的昵称和消息内容

2 .该事件将由服务器处理并广播给所有用户

3 .在 android 中添加一个套接字监听器来监听服务器触发的事件“消息”

4 .从额外的数据中提取昵称和消息，并创建对象消息的新实例

5 .将实例添加到消息的数组列表中，并通知适配器更新回收器视图

但在此之前，让我们设置我们的 recyler 视图，适配器，消息文本字段和按钮发送。

在 ChatBoxActivity 中添加以下声明

```
public RecyclerView myRecylerView ;
public List<Message> MessageList ;
public ChatBoxAdapter chatBoxAdapter;
public  EditText messagetxt ;
public  Button send ; 
```

在 onCreate 方法中添加以下几行

```
messagetxt = (EditText) findViewById(R.id.message) ;
send = (Button)findViewById(R.id.send);
MessageList = new ArrayList<>();
myRecylerView = (RecyclerView) findViewById(R.id.messagelist);
RecyclerView.LayoutManager mLayoutManager = new LinearLayoutManager(getApplicationContext());
myRecylerView.setLayoutManager(mLayoutManager);
myRecylerView.setItemAnimator(new DefaultItemAnimator()); 
```

现在，在您的 ChatBoxActivity 中，按钮操作应该是这样的

```
send.setOnClickListener(new View.OnClickListener() {
    @Overridepublic void onClick(View v) {
        //retrieve the nickname and the message content and fire the event messagedetection

  if(!messagetxt.getText().toString().isEmpty()){

            socket.emit("messagedetection",Nickname,messagetxt.getText().toString());

            messagetxt.setText(" ");         
    }

    }
}); 
```

监听器应该是这样的

```
socket.on("message", new Emitter.Listener() {
    @Overridepublic void call(final Object... args) {
        runOnUiThread(new Runnable() {
            @Overridepublic void run() {
                JSONObject data = (JSONObject) args[0];
                try {
                    //extract data from fired event

              String nickname = data.getString("senderNickname");
              String message = data.getString("message");

           // make instance of message

          Message m = new Message(nickname,message);

          //add the message to the messageList

          MessageList.add(m);

          // add the new updated list to the adapter 
          chatBoxAdapter = new ChatBoxAdapter(MessageList);

           // notify the adapter to update the recycler view

          chatBoxAdapter.notifyDataSetChanged();

           //set the adapter for the recycler view 

          myRecylerView.setAdapter(chatBoxAdapter);

                } catch (JSONException e) {
                    e.printStackTrace();
                }

            }
        });
    }
}); 
```

正如我们在下面的截图中看到的，一切都正常:))并且消息从两边显示，请注意，我们可以与许多其他用户连接，但我们只需运行其他模拟器并输入昵称即可加入聊天框

[![Alt text of image](../Images/cf35a79181dfffc7dc5a3f96316e1ff4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7HuHp1Uy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQHTPTtd9ViV7w/article-inline_image-shrink_1000_1488/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DKf-D2adfIIqxh6JcUUEkdGZ9_maQnkL0pM4ZvOBFeKE)

在结束本教程之前，我们必须执行最后一个函数，该函数检测用户是否断开了与聊天框的连接。

在我们的 ChatBoxActivity 中，重写 onDestroy()方法并添加以下代码行

```
@Override
protected void onDestroy() {
    super.onDestroy();
    socket.disconnect(); 
} 
```

对于听众来说

```
socket.on("userdisconnect", new Emitter.Listener() {
    @Overridepublic void call(final Object... args) {
        runOnUiThread(new Runnable() {
            @Overridepublic void run() {
                String data = (String) args[0];

                Toast.makeText(ChatBoxActivity.this,data,Toast.LENGTH_SHORT).show();

            }
        });
    }
}); 
```

最后，我们的 ChatBoxActivity 将如下所示

```
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.DefaultItemAnimator;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.github.nkzawa.emitter.Emitter;
import com.github.nkzawa.socketio.client.IO;
import com.github.nkzawa.socketio.client.Socket;

import org.json.JSONException;
import org.json.JSONObject;

import java.net.URISyntaxException;
import java.util.ArrayList;
import java.util.List;

public class ChatBoxActivity extends AppCompatActivity {
    public RecyclerView myRecylerView ;
    public List<Message> MessageList ;
    public ChatBoxAdapter chatBoxAdapter;
    public  EditText messagetxt ;
    public  Button send ;
    //declare socket objectprivate Socket socket;

    public String Nickname ;
    @Overrideprotected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chat_box);

        messagetxt = (EditText) findViewById(R.id.message) ;
        send = (Button)findViewById(R.id.send);
        // get the nickame of the user
        Nickname= (String)getIntent().getExtras().getString(MainActivity.NICKNAME);
        //connect you socket client to the servertry {
            socket = IO.socket("http://yourlocalIPaddress:3000");
            socket.connect();
            socket.emit("join", Nickname);
        } catch (URISyntaxException e) {
            e.printStackTrace();

        }
       //setting up recyler
        MessageList = new ArrayList<>();
        myRecylerView = (RecyclerView) findViewById(R.id.messagelist);
        RecyclerView.LayoutManager mLayoutManager = new LinearLayoutManager(getApplicationContext());
        myRecylerView.setLayoutManager(mLayoutManager);
        myRecylerView.setItemAnimator(new DefaultItemAnimator());

        // message send action
        send.setOnClickListener(new View.OnClickListener() {
            @Overridepublic void onClick(View v) {
                //retrieve the nickname and the message content and fire the event messagedetectionif(!messagetxt.getText().toString().isEmpty()){
                    socket.emit("messagedetection",Nickname,messagetxt.getText().toString());

                    messagetxt.setText(" ");
                }

            }
        });

        //implementing socket listeners
        socket.on("userjoinedthechat", new Emitter.Listener() {
            @Overridepublic void call(final Object... args) {
                runOnUiThread(new Runnable() {
                    @Overridepublic void run() {
                        String data = (String) args[0];

                        Toast.makeText(ChatBoxActivity.this,data,Toast.LENGTH_SHORT).show();

                    }
                });
            }
        });
        socket.on("userdisconnect", new Emitter.Listener() {
            @Overridepublic void call(final Object... args) {
                runOnUiThread(new Runnable() {
                    @Overridepublic void run() {
                        String data = (String) args[0];

                        Toast.makeText(ChatBoxActivity.this,data,Toast.LENGTH_SHORT).show();

                    }
                });
            }
        });
        socket.on("message", new Emitter.Listener() {
            @Overridepublic void call(final Object... args) {
                runOnUiThread(new Runnable() {
                    @Overridepublic void run() {
                        JSONObject data = (JSONObject) args[0];
                        try {
                            //extract data from fired event

                            String nickname = data.getString("senderNickname");
                            String message = data.getString("message");

                            // make instance of message

                            Message m = new Message(nickname,message);

                            //add the message to the messageList

                            MessageList.add(m);

                            // add the new updated list to the dapter
                            chatBoxAdapter = new ChatBoxAdapter(MessageList);

                            // notify the adapter to update the recycler view

                            chatBoxAdapter.notifyDataSetChanged();

                            //set the adapter for the recycler view

                            myRecylerView.setAdapter(chatBoxAdapter);

                        } catch (JSONException e) {
                            e.printStackTrace();
                        }

                    }
                });
            }
        });
    }

    @Override

protected void onDestroy() {
        super.onDestroy();

        socket.disconnect(); 
    }
} 
```

# 结论

在这个例子中，我们很好地了解了 socket.io 以及 node js 和 android 的用法，我们还试图解释一些基础知识，了解 socket.io 的机制以及如何在客户端和服务器之间建立双向通信，请注意，socket.io 中还有其他工具，如 rooms 和 namespaces，它们对创建漂亮的 web 和移动应用程序非常有帮助。

在这些相关链接中找到两个项目:

客户端:[https://github . com/medymenn/andidchat](https://github.com/medaymenTN/AndroidChat)

服务器端:[https://github.com/medaymenTN/NodeJSChatServer](https://github.com/medaymenTN/NodeJSChatServer)