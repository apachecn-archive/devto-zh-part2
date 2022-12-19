# 使用 Kotlin 构建一个在线的 Android messenger 应用程序

> 原文：<https://dev.to/neo/build-an-android-messenger-app-with-online-presence-using-kotlin-34lk>

> 你需要在你的机器上安装 Android Studio 3+，Node，npm 和 MongoDB。需要对 Android 开发有所了解。

当构建一个聊天应用程序时，拥有在线状态功能是非常重要的。这很重要，因为你的用户想知道他们的朋友何时在线，并且更有可能回复他们的消息。

在本文中，我们将使用 Pusher Channels、Kotlin 和 Node.js 构建一个具有在线状态的 messenger 应用程序。

这是我们将构建的演示:

[![](../Images/31cd54d82454f960d3d5bd20271cd395.png)T2】](//images.ctfassets.net/1es3ne0caaid/mmdMFfq7WoYsk26cIKocs/486ce2b458e45a41cfeb01ab4441f3b4/android-messenger-presence-demo.gif)

# 先决条件

要跟进，您需要满足以下要求:

*   一个推送频道 app。你可以在这里创建一个。
*   Android Studio 安装在您的机器上。你可以在这里查看[最新的稳定版本。建议最低版本为 3.0。](https://developer.android.com/studio/index.html)
*   Android 开发和 Android Studio IDE 的基础知识。
*   科特林的基础知识。以下是[官方文件](https://kotlinlang.org/docs/reference/)。
*   Node.js 和 NPM(节点包管理器)。在这里下载。
*   Mongo DB 安装在您的机器上。你可以按照这里的说明[安装它。](https://docs.mongodb.com/manual/installation/)

## 构建后端服务器

我们的服务器将使用 Node.js 构建。首先，创建一个新的项目目录:

```
 $ mkdir backend-server 
```

接下来，在项目目录中创建一个新的`index.js`文件，并粘贴以下代码:

```
 <span class="hljs-comment">// File: ./index.js</span>
    <span class="hljs-keyword">var</span> express = <span class="hljs-built_in">require</span>(<span class="hljs-string">'express'</span>);
    <span class="hljs-keyword">var</span> bodyParser = <span class="hljs-built_in">require</span>(<span class="hljs-string">'body-parser'</span>);
    <span class="hljs-keyword">const</span> mongoose = <span class="hljs-built_in">require</span>(<span class="hljs-string">'mongoose'</span>);
    <span class="hljs-keyword">var</span> Pusher = <span class="hljs-built_in">require</span>(<span class="hljs-string">'pusher'</span>);

    <span class="hljs-keyword">var</span> app = express();

    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ <span class="hljs-attr">extended</span>: <span class="hljs-literal">false</span> }));

    <span class="hljs-keyword">var</span> pusher = <span class="hljs-keyword">new</span> Pusher({
      <span class="hljs-attr">appId</span>: <span class="hljs-string">'PUSHER_APP_ID'</span>,
      <span class="hljs-attr">key</span>: <span class="hljs-string">'PUSHER_APP_KEY'</span>,
      <span class="hljs-attr">secret</span>: <span class="hljs-string">'PUSHER_APP_SECRET'</span>,
      <span class="hljs-attr">cluster</span>: <span class="hljs-string">'PUSHER_APP_CLUSTER'</span>
    });

    mongoose.connect(<span class="hljs-string">'mongodb://127.0.0.1/db'</span>);

    <span class="hljs-keyword">const</span> Schema = mongoose.Schema;
    <span class="hljs-keyword">const</span> userSchema = <span class="hljs-keyword">new</span> Schema({
        <span class="hljs-attr">name</span>: { <span class="hljs-attr">type</span>: <span class="hljs-built_in">String</span>, <span class="hljs-attr">required</span>: <span class="hljs-literal">true</span>, },
        <span class="hljs-attr">count</span>: {<span class="hljs-attr">type</span>: <span class="hljs-built_in">Number</span>}
    });

    <span class="hljs-keyword">var</span> User = mongoose.model(<span class="hljs-string">'User'</span>, userSchema);
    userSchema.pre(<span class="hljs-string">'save'</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">next</span>) </span>{
        <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.isNew) {
            User.count().then(<span class="hljs-function"><span class="hljs-params">res</span> =></span> {
              <span class="hljs-keyword">this</span>.count = res; <span class="hljs-comment">// Increment count</span>
              next();
            });
          } <span class="hljs-keyword">else</span> {
            next();
          }
    });

    <span class="hljs-built_in">module</span>.exports = User;

    <span class="hljs-keyword">var</span> currentUser;

    <span class="hljs-comment">/* 
    =================================
    We will add our endpoints here!!!
    =================================
    */</span>

    <span class="hljs-keyword">var</span> port = process.env.PORT || <span class="hljs-number">5000</span>;

    app.listen(port); 
```

在上面的代码片段中，我们初始化了 Pusher、Express 和 MongoDB。我们使用 [Moongose](http://mongoosejs.com/) 连接到我们的 MongoDB 实例。

> 将`PUSHER_APP_*`键替换为推动器仪表盘上的键。

现在让我们添加端点。我们将添加的第一个端点是让用户登录。将下面的代码粘贴到您的`index.js`文件中的`currentUser`声明下面:

```
 <span class="hljs-comment">// File: ./index.js</span>

    <span class="hljs-comment">// [...]</span>

    app.post(<span class="hljs-string">'/login'</span>, (req,res) => {
        User.findOne({<span class="hljs-attr">name</span>: req.body.name}, (err, user) => {
            <span class="hljs-keyword">if</span> (err) {
                res.send(<span class="hljs-string">"Error connecting to database"</span>);
            }

            <span class="hljs-comment">// User exists</span>
            <span class="hljs-keyword">if</span> (user) {
                currentUser = user;
                <span class="hljs-keyword">return</span> res.status(<span class="hljs-number">200</span>).send(user)
            }

            <span class="hljs-keyword">let</span> newuser = <span class="hljs-keyword">new</span> User({<span class="hljs-attr">name</span>: req.body.name});

            newuser.save(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">err</span>) </span>{
                <span class="hljs-keyword">if</span> (err) <span class="hljs-keyword">throw</span> err;
            });

            currentUser = newuser;
            res.status(<span class="hljs-number">200</span>).send(newuser)
        });
    })

    <span class="hljs-comment">// [...]</span> 
```

这个端点接收一个带有请求的`username`,或者创建一个新用户，或者返回现有用户的数据。

让我们在上一个端点下面添加下一个端点:

```
 <span class="hljs-comment">// File: ./index.js</span>

    <span class="hljs-comment">// [...]</span>

    app.get(<span class="hljs-string">'/users'</span>, (req,res) => {
        User.find({}, (err, users) => {
            <span class="hljs-keyword">if</span> (err) <span class="hljs-keyword">throw</span> err;
            res.send(users);
        });
    })

    <span class="hljs-comment">// [...]</span> 
```

上面的这个端点从数据库中获取所有用户并返回他们。

因为我们将使用推送存在通道，所以我们需要一个端点来验证用户。在同一个文件中，将这段代码粘贴到上面的端点下面:

```
 <span class="hljs-comment">// File: ./index.js</span>

    <span class="hljs-comment">// [...]</span>

    app.post(<span class="hljs-string">'/pusher/auth/presence'</span>, (req, res) => {
        <span class="hljs-keyword">let</span> socketId = req.body.socket_id;
        <span class="hljs-keyword">let</span> channel = req.body.channel_name;

        <span class="hljs-keyword">let</span> presenceData = {
            <span class="hljs-attr">user_id</span>: currentUser._id,
            <span class="hljs-attr">user_info</span>: {<span class="hljs-attr">count</span>: currentUser.count, <span class="hljs-attr">name</span>: currentUser.name}
        };

        <span class="hljs-keyword">let</span> auth = pusher.authenticate(socketId, channel, presenceData);

        res.send(auth);
    });

    <span class="hljs-comment">// [...]</span> 
```

因为我们将使用私有通道，所以我们需要一个端点来进行身份验证。在上述端点下增加以下端点:

```
 <span class="hljs-comment">// File: ./index.js</span>

    <span class="hljs-comment">// [...]</span>

    app.post(<span class="hljs-string">'/pusher/auth/private'</span>, (req, res) => {
        res.send(pusher.authenticate(req.body.socket_id, req.body.channel_name));
    });

    <span class="hljs-comment">// [...]</span>

Finally, the last endpoint will be to trigger an event <span class="hljs-string">`new-message`</span> to a channel. Add the endpoint below the last one:

    <span class="hljs-comment">// File: ./index.js</span>

    <span class="hljs-comment">// [...]</span>

    app.post(<span class="hljs-string">'/send-message'</span>, (req, res) => {
        <span class="hljs-keyword">let</span> payload = {<span class="hljs-attr">message</span>: req.body.message, <span class="hljs-attr">sender_id</span>: req.body.sender_id}
        pusher.trigger(req.body.channel_name, <span class="hljs-string">'new-message'</span>, payload);
        res.send(<span class="hljs-number">200</span>);
    });

    <span class="hljs-comment">// [...]</span> 
```

添加完所有端点后，通过运行以下命令安装必要的 NPM 软件包:

```
 $ npm install express body-parser mongoose pusher 
```

在运行应用程序之前，使用以下命令确保 MongoDB 已经在运行:

```
 $ mongod --dbpath C:\MongoDB\data\db # Windows
    $ mongod --dbpath=/path/to/db/directory # Mac or Linux 
```

现在您可以使用下面的命令运行您的应用程序:

```
 $ node index.js 
```

您的应用将在此处可用: [http://localhost:5000](http://localhost:5000) 。

## 构建我们的 Android 应用

创建您的 Android 项目。在向导中，输入您的项目名称，比如说 **MessengerApp。**接下来，输入你的包名。您可以使用最少 19 个 SDK，然后选择一个**空活动**。在下一页，将**活动名称**更改为`LoginActivity`。在此之后，Android Studio 将为您构建您的项目。

现在我们有了项目，让我们为我们的应用程序添加所需的依赖项。打开你的应用模块`build.gradle`文件，添加这些:

```
 // File ../app/build.gradle
    dependencies {
      // [...]

      implementation 'com.android.support:design:28+'
      implementation 'com.pusher:pusher-java-client:1.6.0'
      implementation "com.squareup.retrofit2:retrofit:2.4.0"
      implementation "com.squareup.retrofit2:converter-scalars:2.4.0"
      implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
    } 
```

值得注意的是，我们添加了对[改型](http://square.github.io/retrofit/)和推进器的依赖。翻新是一个用于网络调用的 HTTP 客户端库。我们也添加了设计库依赖，因为我们想使用其中的一些类。同步你的 gradle 文件以获取依赖关系。

接下来，让我们准备我们的应用程序进行网络通话。改造需要一个接口来知道要访问的端点。

创建一个名为`ApiService`的新接口，并粘贴这个:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/ApiService.kt</span>
    <span class="hljs-keyword">import</span> okhttp3.RequestBody
    <span class="hljs-keyword">import</span> retrofit2.Call
    <span class="hljs-keyword">import</span> retrofit2.http.Body
    <span class="hljs-keyword">import</span> retrofit2.http.GET
    <span class="hljs-keyword">import</span> retrofit2.http.POST

    <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">ApiService</span> </span>{

      <span class="hljs-meta">@POST(<span class="hljs-meta-string">"/login"</span>)</span>
      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">login</span><span class="hljs-params">(<span class="hljs-meta">@Body</span> body:<span class="hljs-type">RequestBody</span>)</span></span>: Call<UserModel>

      <span class="hljs-meta">@POST(<span class="hljs-meta-string">"/send-message"</span>)</span>
      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">sendMessage</span><span class="hljs-params">(<span class="hljs-meta">@Body</span> body:<span class="hljs-type">RequestBody</span>)</span></span>: Call<String>

      <span class="hljs-meta">@GET(<span class="hljs-meta-string">"/users"</span>)</span>
      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">getUsers</span><span class="hljs-params">()</span></span>: Call<List<UserModel>>
    } 
```

这里，我们声明了三个端点。它们用于登录、发送消息和获取用户。请注意，在我们的一些响应中，我们返回了`Call<UserModel>`。让我们创建`UserModel`。创建一个名为`UserModel`的新类，并粘贴以下内容:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/UserModel.kt</span>
    <span class="hljs-keyword">import</span> com.google.gson.annotations.Expose
    <span class="hljs-keyword">import</span> com.google.gson.annotations.SerializedName

    <span class="hljs-keyword">data</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">UserModel</span></span>(<span class="hljs-meta">@SerializedName(<span class="hljs-meta-string">"_id"</span>)</span> <span class="hljs-meta">@Expose</span> <span class="hljs-keyword">var</span> id: String,
                         <span class="hljs-meta">@SerializedName(<span class="hljs-meta-string">"name"</span>)</span> <span class="hljs-meta">@Expose</span> <span class="hljs-keyword">var</span> name: String,
                         <span class="hljs-meta">@SerializedName(<span class="hljs-meta-string">"count"</span>)</span> <span class="hljs-meta">@Expose</span> <span class="hljs-keyword">var</span> count: <span class="hljs-built_in">Int</span>,
                         <span class="hljs-keyword">var</span> online:<span class="hljs-built_in">Boolean</span> = <span class="hljs-literal">false</span>) 
```

上面，我们使用了一个数据类，这样模型类所需的一些其他函数，如`toString`、`hashCode`被默认添加到该类中。

我们只期待来自服务器的`id`和`name`的值。我们添加了`online`属性，这样我们以后就可以更新了。

接下来，创建一个名为`RetrofitInstance`的新类，并粘贴以下代码:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/RetrofitInstance.kt</span>
    <span class="hljs-keyword">import</span> okhttp3.OkHttpClient
    <span class="hljs-keyword">import</span> retrofit2.Retrofit
    <span class="hljs-keyword">import</span> retrofit2.converter.gson.GsonConverterFactory
    <span class="hljs-keyword">import</span> retrofit2.converter.scalars.ScalarsConverterFactory

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">RetrofitInstance</span> </span>{

      <span class="hljs-keyword">companion</span> <span class="hljs-keyword">object</span> {
        <span class="hljs-keyword">val</span> retrofit: ApiService <span class="hljs-keyword">by</span> lazy {
          <span class="hljs-keyword">val</span> httpClient = OkHttpClient.Builder()
          <span class="hljs-keyword">val</span> builder = Retrofit.Builder()
              .baseUrl(<span class="hljs-string">"http://10.0.2.2:5000/"</span>)
              .addConverterFactory(ScalarsConverterFactory.create())
              .addConverterFactory(GsonConverterFactory.create())

          <span class="hljs-keyword">val</span> retrofit = builder
              .client(httpClient.build())
              .build()
          retrofit.create(ApiService::<span class="hljs-class"><span class="hljs-keyword">class</span>.<span class="hljs-title">java</span>)</span>
        }
      }
    } 
```

这个类包含一个名为`retrofit`的类变量。它为我们提供了一个改造的实例，我们将在多个类中引用它。

最后，要请求互联网访问权限，请更新`AndroidManifest.xml`文件，如下所示:

```
 <span class="hljs-comment">// File: ./app/src/main/ApiService.kt</span>
    <manifest xmlns:android=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-keyword">package</span>=<span class="hljs-string">"com.example.messengerapp"</span>>

      <uses-permission android:name=<span class="hljs-string">"android.permission.INTERNET"</span> />
      [...]

    </manifest> 
```

现在我们可以使用 reform 发出请求。

我们将实现的下一个特性是登录。打开已经创建好的`LoginActivity`布局文件`activity_login.xml`并粘贴这个:

```
 // File: ./app/src/main/res/layout/activity_login.xml
    <span class="hljs-meta"><?xml version="1.0" encoding="utf-8"?></span>
    <span class="hljs-tag"><<span class="hljs-name">android.support.constraint.ConstraintLayout</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-attr">xmlns:app</span>=<span class="hljs-string">"http://schemas.android.com/apk/res-auto"</span>
      <span class="hljs-attr">xmlns:tools</span>=<span class="hljs-string">"http://schemas.android.com/tools"</span>
      <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">android:layout_margin</span>=<span class="hljs-string">"20dp"</span>
      <span class="hljs-attr">tools:context</span>=<span class="hljs-string">".LoginActivity"</span>></span>

      <span class="hljs-tag"><<span class="hljs-name">EditText</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/editTextUsername"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">app:layout_constraintBottom_toBottomOf</span>=<span class="hljs-string">"parent"</span>
        <span class="hljs-attr">app:layout_constraintLeft_toLeftOf</span>=<span class="hljs-string">"parent"</span>
        <span class="hljs-attr">app:layout_constraintRight_toRightOf</span>=<span class="hljs-string">"parent"</span>
        <span class="hljs-attr">app:layout_constraintTop_toTopOf</span>=<span class="hljs-string">"parent"</span> /></span>

      <span class="hljs-tag"><<span class="hljs-name">Button</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/loginButton"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:text</span>=<span class="hljs-string">"Login"</span>
        <span class="hljs-attr">app:layout_constraintTop_toBottomOf</span>=<span class="hljs-string">"@+id/editTextUsername"</span> /></span>

    <span class="hljs-tag"></<span class="hljs-name">android.support.constraint.ConstraintLayout</span>></span> 
```

该布局包含一个输入用户名的输入字段和一个发出登录请求的按钮。

接下来，打开`LoginActivity.Kt`文件并粘贴这个:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/LoginActivity.kt</span>
    <span class="hljs-keyword">import</span> android.content.Intent
    <span class="hljs-keyword">import</span> android.os.Bundle
    <span class="hljs-keyword">import</span> android.support.v7.app.AppCompatActivity
    <span class="hljs-keyword">import</span> android.util.Log
    <span class="hljs-keyword">import</span> kotlinx.android.synthetic.main.activity_login.*
    <span class="hljs-keyword">import</span> okhttp3.MediaType
    <span class="hljs-keyword">import</span> okhttp3.RequestBody
    <span class="hljs-keyword">import</span> org.json.JSONObject
    <span class="hljs-keyword">import</span> retrofit2.Call
    <span class="hljs-keyword">import</span> retrofit2.Callback
    <span class="hljs-keyword">import</span> retrofit2.Response

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LoginActivity</span> : <span class="hljs-type">AppCompatActivity</span></span>() {

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onCreate</span><span class="hljs-params">(savedInstanceState: <span class="hljs-type">Bundle</span>?)</span></span> {
        <span class="hljs-keyword">super</span>.onCreate(savedInstanceState)
        setContentView(R.layout.activity_login)
        loginButton.setOnClickListener {
          <span class="hljs-keyword">if</span> (editTextUsername.text.isNotEmpty()) {
            loginFunction(editTextUsername.text.toString())
          }
        }
      }

      <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">loginFunction</span><span class="hljs-params">(name:<span class="hljs-type">String</span>)</span></span> {
        <span class="hljs-keyword">val</span> jsonObject = JSONObject()
        jsonObject.put(<span class="hljs-string">"name"</span>, name)

        <span class="hljs-keyword">val</span> jsonBody = RequestBody.create(
            MediaType.parse(<span class="hljs-string">"application/json; charset=utf-8"</span>), 
            jsonObject.toString()
        )

        RetrofitInstance.retrofit.login(jsonBody).enqueue(<span class="hljs-keyword">object</span>:Callback<UserModel> {
          <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onFailure</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">UserModel</span>>?, t: <span class="hljs-type">Throwable</span>?)</span></span> {
            Log.i(<span class="hljs-string">"LoginActivity"</span>,t!!.localizedMessage)
          }

          <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onResponse</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">UserModel</span>>?, response: <span class="hljs-type">Response</span><<span class="hljs-type">UserModel</span>>?)</span></span> {
            <span class="hljs-keyword">if</span> (response!!.code() == <span class="hljs-number">200</span>) {
              Singleton.getInstance().currentUser = response.body()!!
              startActivity(Intent(<span class="hljs-keyword">this</span><span class="hljs-symbol">@LoginActivity</span>,ContactListActivity::<span class="hljs-class"><span class="hljs-keyword">class</span>.<span class="hljs-title">java</span>))</span>
              finish()
            }
          }
        })
      }
    } 
```

在该文件中，我们为登录按钮设置了一个侦听器，这样当它被单击时，我们可以将文本发送到服务器进行身份验证。我们还将登录的用户存储在一个 singleton 类中，以便我们可以在以后访问用户的详细信息。

创建一个名为`Singleton`的新类，并粘贴这个类:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/RetrofitInstance.kt</span>
    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Singleton</span> </span>{
      <span class="hljs-keyword">companion</span> <span class="hljs-keyword">object</span> {
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> ourInstance = Singleton()
        <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">getInstance</span><span class="hljs-params">()</span></span>: Singleton {
          <span class="hljs-keyword">return</span> ourInstance
        }
      }
      <span class="hljs-keyword">lateinit</span> <span class="hljs-keyword">var</span> currentUser: UserModel
    } 
```

有了这个类，我们就可以访问`currentUser`，也就是登录的用户。

接下来，让我们创建一个名为`ContactListActivity`的新活动。现在，让这个类为空，打开相应的名为`activity_contact_list`的布局文件，粘贴以下内容:

```
 // File: ./app/src/main/res/layout/activity_contact_list.xml
    <span class="hljs-meta"><?xml version="1.0" encoding="utf-8"?></span>
    <span class="hljs-tag"><<span class="hljs-name">android.support.constraint.ConstraintLayout</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-attr">xmlns:app</span>=<span class="hljs-string">"http://schemas.android.com/apk/res-auto"</span>
      <span class="hljs-attr">xmlns:tools</span>=<span class="hljs-string">"http://schemas.android.com/tools"</span>
      <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">tools:context</span>=<span class="hljs-string">".ContactListActivity"</span>></span>

      <span class="hljs-tag"><<span class="hljs-name">android.support.v7.widget.RecyclerView</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/recyclerViewUserList"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span>/></span>

    <span class="hljs-tag"></<span class="hljs-name">android.support.constraint.ConstraintLayout</span>></span> 
```

该布局包含一个 recycler 视图，它将为我们提供从数据库中获取的所有联系人列表。因为我们在列表中显示项目，所以我们需要一个适配器类来管理项目如何膨胀到布局中。

创建一个名为`ContactRecyclerAdapter`的新类，并粘贴这个类:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/ContactRecyclerAdapter.kt</span>
    <span class="hljs-keyword">import</span> android.support.v7.widget.RecyclerView
    <span class="hljs-keyword">import</span> android.view.LayoutInflater
    <span class="hljs-keyword">import</span> android.view.View
    <span class="hljs-keyword">import</span> android.view.ViewGroup
    <span class="hljs-keyword">import</span> android.widget.ImageView
    <span class="hljs-keyword">import</span> android.widget.TextView
    <span class="hljs-keyword">import</span> java.util.*

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ContactRecyclerAdapter</span></span>(<span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> list: ArrayList<UserModel>, <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> listener: UserClickListener)
      : RecyclerView.Adapter<ContactRecyclerAdapter.ViewHolder>() {

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onCreateViewHolder</span><span class="hljs-params">(parent: <span class="hljs-type">ViewGroup</span>, viewType: <span class="hljs-type">Int</span>)</span></span>: ViewHolder {
        <span class="hljs-keyword">return</span> ViewHolder(LayoutInflater.from(parent.context)
            .inflate(R.layout.user_list_row, parent, <span class="hljs-literal">false</span>))
      }

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onBindViewHolder</span><span class="hljs-params">(holder: <span class="hljs-type">ViewHolder</span>, position: <span class="hljs-type">Int</span>)</span></span> = holder.bind(list[position])

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">getItemCount</span><span class="hljs-params">()</span></span>: <span class="hljs-built_in">Int</span> = list.size

      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">showUserOnline</span><span class="hljs-params">(updatedUser: <span class="hljs-type">UserModel</span>)</span></span> {
        list.forEachIndexed { index, element ->
          <span class="hljs-keyword">if</span> (updatedUser.id == element.id) {
            updatedUser.online = <span class="hljs-literal">true</span>
            list[index] = updatedUser
            notifyItemChanged(index)
          }

        }
      }

      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">showUserOffline</span><span class="hljs-params">(updatedUser: <span class="hljs-type">UserModel</span>)</span></span> {
        list.forEachIndexed { index, element ->
          <span class="hljs-keyword">if</span> (updatedUser.id == element.id) {
            updatedUser.online = <span class="hljs-literal">false</span>
            list[index] = updatedUser
            notifyItemChanged(index)
          }
        }
      }

      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">add</span><span class="hljs-params">(user: <span class="hljs-type">UserModel</span>)</span></span> {
        list.add(user)
        notifyDataSetChanged()
      }

      <span class="hljs-keyword">inner</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ViewHolder</span></span>(itemView: View) : RecyclerView.ViewHolder(itemView) {
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> nameTextView: TextView = itemView.findViewById(R.id.usernameTextView)
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> presenceImageView: ImageView = itemView.findViewById(R.id.presenceImageView)

        <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">bind</span><span class="hljs-params">(currentValue: <span class="hljs-type">UserModel</span>)</span></span> = with(itemView) {
          <span class="hljs-keyword">this</span>.setOnClickListener {
            listener.onUserClicked(currentValue)
          }
          nameTextView.text = currentValue.name
          <span class="hljs-keyword">if</span> (currentValue.online){
            presenceImageView.setImageDrawable(<span class="hljs-keyword">this</span>.context.resources.getDrawable(R.drawable.presence_icon_online))
          } <span class="hljs-keyword">else</span> {
            presenceImageView.setImageDrawable(<span class="hljs-keyword">this</span>.context.resources.getDrawable(R.drawable.presence_icon))

          }

        }
      }

      <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">UserClickListener</span> </span>{
        <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onUserClicked</span><span class="hljs-params">(user: <span class="hljs-type">UserModel</span>)</span></span>
      }
    } 
```

这个适配器有一些被覆盖的方法和一些自定义方法。

`onCreateViewHolder`显示每一行的外观。`onBindViewHolder`通过调用内部`ViewHolder`类中的`bind`方法将数据绑定到每一项。`getItemCount`给出了列表的大小。

对于我们的定制方法，`showUserOffline`更新用户并显示他们何时离线。而`showUserOnline`则相反。最后，我们有`add`方法，它向列表中添加一个新的联系人并刷新它。

在上面的适配器类中，我们使用了一个名为`user_list_row`的新布局。创建一个新的布局`user_list_row`并粘贴这个:

```
 // File: ./app/src/main/res/layout/user_list_row.xml
    <span class="hljs-meta"><?xml version="1.0" encoding="utf-8"?></span>
    <span class="hljs-tag"><<span class="hljs-name">LinearLayout</span>
      <span class="hljs-attr">android:orientation</span>=<span class="hljs-string">"horizontal"</span>
      <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-attr">xmlns:app</span>=<span class="hljs-string">"http://schemas.android.com/apk/res-auto"</span>
      <span class="hljs-attr">xmlns:tools</span>=<span class="hljs-string">"http://schemas.android.com/tools"</span>
      <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
      <span class="hljs-attr">android:layout_margin</span>=<span class="hljs-string">"20dp"</span>
      <span class="hljs-attr">android:gravity</span>=<span class="hljs-string">"center"</span>
      <span class="hljs-attr">tools:context</span>=<span class="hljs-string">".LoginActivity"</span>></span>

      <span class="hljs-tag"><<span class="hljs-name">ImageView</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/presenceImageView"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"15dp"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"15dp"</span>
        <span class="hljs-attr">app:srcCompat</span>=<span class="hljs-string">"@drawable/presence_icon"</span> /></span>

      <span class="hljs-tag"><<span class="hljs-name">TextView</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">tools:text</span>=<span class="hljs-string">"Neo"</span>
        <span class="hljs-attr">android:textSize</span>=<span class="hljs-string">"20sp"</span>
        <span class="hljs-attr">android:layout_marginStart</span>=<span class="hljs-string">"10dp"</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/usernameTextView"</span>
        <span class="hljs-attr">app:layout_constraintTop_toBottomOf</span>=<span class="hljs-string">"@+id/editTextUsername"</span>
        /></span>

    <span class="hljs-tag"></<span class="hljs-name">LinearLayout</span>></span> 
```

此布局是布局上每个项目外观的可视化表示。该布局有一个显示用户在线状态的图像视图。该布局还有一个文本视图，在图标旁边显示联系人的姓名。图标是矢量绘图。让我们创建文件。

创建一个名为`presence_icon_online`的新 drawable 并粘贴这个:

```
 // File: ./app/src/main/res/drawable/presence_icon_online.xml
    <span class="hljs-tag"><<span class="hljs-name">vector</span> <span class="hljs-attr">android:height</span>=<span class="hljs-string">"24dp"</span> <span class="hljs-attr">android:tint</span>=<span class="hljs-string">"#3FFC3C"</span>
        <span class="hljs-attr">android:viewportHeight</span>=<span class="hljs-string">"24.0"</span> <span class="hljs-attr">android:viewportWidth</span>=<span class="hljs-string">"24.0"</span>
        <span class="hljs-attr">android:width</span>=<span class="hljs-string">"24dp"</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">path</span> <span class="hljs-attr">android:fillColor</span>=<span class="hljs-string">"#FF000000"</span> <span class="hljs-attr">android:pathData</span>=<span class="hljs-string">"M12,2C6.48,2 2,6.48 2,12s4.48,10 10,10 10,-4.48 10,-10S17.52,2 12,2z"</span>/></span>
    <span class="hljs-tag"></<span class="hljs-name">vector</span>></span> 
```

创建另一个名为`presence_icon`的 drawable 并粘贴这个:

```
 // File: ./app/src/main/res/drawable/presence_icon.xml
    <span class="hljs-tag"><<span class="hljs-name">vector</span> <span class="hljs-attr">android:height</span>=<span class="hljs-string">"24dp"</span> <span class="hljs-attr">android:tint</span>=<span class="hljs-string">"#C0C0C6"</span>
        <span class="hljs-attr">android:viewportHeight</span>=<span class="hljs-string">"24.0"</span> <span class="hljs-attr">android:viewportWidth</span>=<span class="hljs-string">"24.0"</span>
        <span class="hljs-attr">android:width</span>=<span class="hljs-string">"24dp"</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>></span>
        <span class="hljs-tag"><<span class="hljs-name">path</span> <span class="hljs-attr">android:fillColor</span>=<span class="hljs-string">"#FF000000"</span> <span class="hljs-attr">android:pathData</span>=<span class="hljs-string">"M12,2C6.48,2 2,6.48 2,12s4.48,10 10,10 10,-4.48 10,-10S17.52,2 12,2z"</span>/></span>
    <span class="hljs-tag"></<span class="hljs-name">vector</span>></span> 
```

接下来，打开`ContactListActivity`类并粘贴这个:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/ContactListActivity.kt</span>
    <span class="hljs-keyword">import</span> android.content.Intent
    <span class="hljs-keyword">import</span> android.os.Bundle
    <span class="hljs-keyword">import</span> android.support.v7.app.AppCompatActivity
    <span class="hljs-keyword">import</span> android.support.v7.widget.LinearLayoutManager
    <span class="hljs-keyword">import</span> android.util.Log
    <span class="hljs-keyword">import</span> com.pusher.client.Pusher
    <span class="hljs-keyword">import</span> com.pusher.client.PusherOptions
    <span class="hljs-keyword">import</span> com.pusher.client.channel.PresenceChannelEventListener
    <span class="hljs-keyword">import</span> com.pusher.client.channel.User
    <span class="hljs-keyword">import</span> com.pusher.client.util.HttpAuthorizer
    <span class="hljs-keyword">import</span> kotlinx.android.synthetic.main.activity_contact_list.*
    <span class="hljs-keyword">import</span> retrofit2.Call
    <span class="hljs-keyword">import</span> retrofit2.Callback
    <span class="hljs-keyword">import</span> retrofit2.Response

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ContactListActivity</span> : <span class="hljs-type">AppCompatActivity</span></span>(),
        ContactRecyclerAdapter.UserClickListener {

      <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> mAdapter = ContactRecyclerAdapter(ArrayList(), <span class="hljs-keyword">this</span>)

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onCreate</span><span class="hljs-params">(savedInstanceState: <span class="hljs-type">Bundle</span>?)</span></span> {
        <span class="hljs-keyword">super</span>.onCreate(savedInstanceState)
        setContentView(R.layout.activity_contact_list)
        setupRecyclerView()
        fetchUsers()
        subscribeToChannel()
      }

    } 
```

在这个类中，我们初始化了`ContactRecyclerAdapter`，然后在`onCreate`方法中调用了三个函数。让我们创建这些新函数。

在同一个类中，添加以下方法:

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">setupRecyclerView</span><span class="hljs-params">()</span></span> {
      with(recyclerViewUserList) {
        layoutManager = LinearLayoutManager(<span class="hljs-keyword">this</span><span class="hljs-symbol">@ContactListActivity</span>)
        adapter = mAdapter
      }
    }

    <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">fetchUsers</span><span class="hljs-params">()</span></span> {
      RetrofitInstance.retrofit.getUsers().enqueue(<span class="hljs-keyword">object</span> : Callback<List<UserModel>> {
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onFailure</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">List</span><<span class="hljs-type">UserModel</span>>>?, t: <span class="hljs-type">Throwable</span>?)</span></span> {}
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onResponse</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">List</span><<span class="hljs-type">UserModel</span>>>?, response: <span class="hljs-type">Response</span><<span class="hljs-type">List</span><<span class="hljs-type">UserModel</span>>>?)</span></span> {
          <span class="hljs-keyword">for</span> (user <span class="hljs-keyword">in</span> response!!.body()!!) {
            <span class="hljs-keyword">if</span> (user.id != Singleton.getInstance().currentUser.id) {
              mAdapter.add(user)
            }
          }
        }
      })
    }

    <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">subscribeToChannel</span><span class="hljs-params">()</span></span> {

      <span class="hljs-keyword">val</span> authorizer = HttpAuthorizer(<span class="hljs-string">"http://10.0.2.2:5000/pusher/auth/presence"</span>)
      <span class="hljs-keyword">val</span> options = PusherOptions().setAuthorizer(authorizer)
      options.setCluster(<span class="hljs-string">"PUSHER_APP_CLUSTER"</span>)

      <span class="hljs-keyword">val</span> pusher = Pusher(<span class="hljs-string">"PUSHER_APP_KEY"</span>, options)
      pusher.connect()

      pusher.subscribePresence(<span class="hljs-string">"presence-channel"</span>, <span class="hljs-keyword">object</span> : PresenceChannelEventListener {
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onUsersInformationReceived</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?, users: <span class="hljs-type">MutableSet</span><<span class="hljs-type">User</span>>?)</span></span> {
          <span class="hljs-keyword">for</span> (user <span class="hljs-keyword">in</span> users!!) {
            <span class="hljs-keyword">if</span> (user.id!=Singleton.getInstance().currentUser.id){
              runOnUiThread {
                mAdapter.showUserOnline(user.toUserModel())
              }
            }
          }
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onEvent</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?, p1: <span class="hljs-type">String</span>?, p2: <span class="hljs-type">String</span>?)</span></span> {}
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onAuthenticationFailure</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?, p1: <span class="hljs-type">Exception</span>?)</span></span> {}
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onSubscriptionSucceeded</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?)</span></span> {}

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">userSubscribed</span><span class="hljs-params">(channelName: <span class="hljs-type">String</span>, user: <span class="hljs-type">User</span>)</span></span> {
          runOnUiThread {
            mAdapter.showUserOnline(user.toUserModel())
          }
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">userUnsubscribed</span><span class="hljs-params">(channelName: <span class="hljs-type">String</span>, user: <span class="hljs-type">User</span>)</span></span> {
          runOnUiThread {
            mAdapter.showUserOffline(user.toUserModel())
          }
        }
      })
    }

    <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onUserClicked</span><span class="hljs-params">(user: <span class="hljs-type">UserModel</span>)</span></span> {
      <span class="hljs-keyword">val</span> intent = Intent(<span class="hljs-keyword">this</span>, ChatRoom::<span class="hljs-class"><span class="hljs-keyword">class</span>.<span class="hljs-title">java</span>)</span>
      intent.putExtra(ChatRoom.EXTRA_ID,user.id)
      intent.putExtra(ChatRoom.EXTRA_NAME,user.name)
      intent.putExtra(ChatRoom.EXTRA_COUNT,user.count)
      startActivity(intent)
    } 
```

> 用仪表盘上的值替换`PUSHER_APP_*`键。

*   `setupRecyclerView`将布局管理器和适配器分配给回收器视图。为了让回收器视图工作，您需要这两样东西。
*   从服务器获取所有用户并显示在列表上。它免除了当前登录的用户。
*   `subcribeToChannel`订阅在线频道。当您订阅一个频道时，`onUsersInformationReceived`会显示订阅该频道的所有用户，包括当前用户。因此，在那个回调中，我们调用了适配器类中的`showUserOnline`方法，这样用户旁边的图标就可以被更改，以表示用户在线。
*   选择联系人时会调用`onUserClicked`。我们将用户的详细信息传递给下一个名为`ChatRoom`的活动。

在前面的代码片段中，我们使用了一个扩展函数将从 Pusher 接收的`User`对象转换为我们自己的`UserModel`对象。让我们来定义这个扩展。

创建一个名为`Utils`的新类，并粘贴这个类:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/Utils.kt</span>
    <span class="hljs-keyword">import</span> com.pusher.client.channel.User
    <span class="hljs-keyword">import</span> org.json.JSONObject

    <span class="hljs-function"><span class="hljs-keyword">fun</span> User.<span class="hljs-title">toUserModel</span><span class="hljs-params">()</span></span>:UserModel{
      <span class="hljs-keyword">val</span> jsonObject = JSONObject(<span class="hljs-keyword">this</span>.info)
      <span class="hljs-keyword">val</span> name = jsonObject.getString(<span class="hljs-string">"name"</span>)
      <span class="hljs-keyword">val</span> numb = jsonObject.getInt(<span class="hljs-string">"count"</span>)
      <span class="hljs-keyword">return</span> UserModel(<span class="hljs-keyword">this</span>.id, name, numb)
    } 
```

现在，因为我们在前面的`onUserClicked`方法中引用了一个`ChatRoom`活动，所以让我们创建它。

创建一个名为`ChatRoom`的新活动。该活动带有一个布局文件`activity_chat_room`，将其粘贴到布局文件:

```
 // File: ./app/src/main/res/layout/activity_chat_room.xml
    <span class="hljs-meta"><?xml version="1.0" encoding="utf-8"?></span>
    <span class="hljs-tag"><<span class="hljs-name">android.support.constraint.ConstraintLayout</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-attr">xmlns:app</span>=<span class="hljs-string">"http://schemas.android.com/apk/res-auto"</span>
      <span class="hljs-attr">xmlns:tools</span>=<span class="hljs-string">"http://schemas.android.com/tools"</span>
      <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span>
      <span class="hljs-attr">tools:context</span>=<span class="hljs-string">".ChatRoom"</span>></span>

      <span class="hljs-tag"><<span class="hljs-name">android.support.v7.widget.RecyclerView</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/recyclerViewChat"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span> /></span>

      <span class="hljs-tag"><<span class="hljs-name">EditText</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/editText"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"0dp"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:layout_margin</span>=<span class="hljs-string">"16dp"</span>
        <span class="hljs-attr">android:hint</span>=<span class="hljs-string">"Enter a message"</span>
        <span class="hljs-attr">app:layout_constraintBottom_toBottomOf</span>=<span class="hljs-string">"parent"</span>
        <span class="hljs-attr">app:layout_constraintEnd_toStartOf</span>=<span class="hljs-string">"@+id/sendButton"</span>
        <span class="hljs-attr">app:layout_constraintStart_toStartOf</span>=<span class="hljs-string">"parent"</span> /></span>

      <span class="hljs-tag"><<span class="hljs-name">android.support.design.widget.FloatingActionButton</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/sendButton"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:layout_gravity</span>=<span class="hljs-string">"end|bottom"</span>
        <span class="hljs-attr">android:layout_margin</span>=<span class="hljs-string">"16dp"</span>
        <span class="hljs-attr">android:src</span>=<span class="hljs-string">"@android:drawable/ic_menu_send"</span>
        <span class="hljs-attr">app:layout_constraintEnd_toEndOf</span>=<span class="hljs-string">"parent"</span>
        <span class="hljs-attr">app:layout_constraintBottom_toBottomOf</span>=<span class="hljs-string">"parent"</span> /></span>

    <span class="hljs-tag"></<span class="hljs-name">android.support.constraint.ConstraintLayout</span>></span> 
```

上面的布局包含聊天消息的回收器视图、收集新消息的编辑文本和发送消息的浮动操作按钮。

接下来，创建一个名为`ChatRoomAdapter`的新类，并粘贴以下内容:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/ChatRoomAdapter.kt</span>
    <span class="hljs-keyword">import</span> android.support.v7.widget.CardView
    <span class="hljs-keyword">import</span> android.support.v7.widget.RecyclerView
    <span class="hljs-keyword">import</span> android.view.LayoutInflater
    <span class="hljs-keyword">import</span> android.view.View
    <span class="hljs-keyword">import</span> android.view.ViewGroup
    <span class="hljs-keyword">import</span> android.widget.RelativeLayout
    <span class="hljs-keyword">import</span> android.widget.TextView
    <span class="hljs-keyword">import</span> java.util.*

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ChatRoomAdapter</span> </span>(<span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> list: ArrayList<MessageModel>)
      : RecyclerView.Adapter<ChatRoomAdapter.ViewHolder>() {

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onCreateViewHolder</span><span class="hljs-params">(parent: <span class="hljs-type">ViewGroup</span>, viewType: <span class="hljs-type">Int</span>)</span></span>: ViewHolder {
        <span class="hljs-keyword">return</span> ViewHolder(LayoutInflater.from(parent.context)
            .inflate(R.layout.chat_item, parent, <span class="hljs-literal">false</span>))
      }

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onBindViewHolder</span><span class="hljs-params">(holder: <span class="hljs-type">ViewHolder</span>, position: <span class="hljs-type">Int</span>)</span></span> = holder.bind(list[position])

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">getItemCount</span><span class="hljs-params">()</span></span>: <span class="hljs-built_in">Int</span> = list.size

      <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">add</span><span class="hljs-params">(message: <span class="hljs-type">MessageModel</span>)</span></span> {
        list.add(message)
        notifyDataSetChanged()
      }

      <span class="hljs-keyword">inner</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ViewHolder</span></span>(itemView: View) : RecyclerView.ViewHolder(itemView) {
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> messageTextView: TextView = itemView.findViewById(R.id.text)
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> cardView: CardView = itemView.findViewById(R.id.cardView)

        <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">bind</span><span class="hljs-params">(message: <span class="hljs-type">MessageModel</span>)</span></span> = with(itemView) {
          messageTextView.text = message.message
          <span class="hljs-keyword">val</span> params = cardView.layoutParams <span class="hljs-keyword">as</span> RelativeLayout.LayoutParams
          <span class="hljs-keyword">if</span> (message.senderId==Singleton.getInstance().currentUser.id) {
            params.addRule(RelativeLayout.ALIGN_PARENT_RIGHT)
          }
        }
      }
    } 
```

这个适配器的工作方式与我们之前创建的类似。但是有一点不同，这里不需要显示 online 和 offline 方法。

接下来，创建另一个名为`MessageModel`的类，并粘贴这个类:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/MessageModel.kt</span>
    <span class="hljs-keyword">data</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MessageModel</span></span>(<span class="hljs-keyword">val</span> message: String, <span class="hljs-keyword">val</span> senderId: String) 
```

适配器类的`onCreateViewHolder`方法中使用的`chat_item`布局表示每个布局的外观。创建一个名为`chat_item`的新布局，粘贴如下:

```
 // File: ./app/src/main/res/layout/chat_item.xml
    <span class="hljs-meta"><?xml version="1.0" encoding="utf-8"?></span>
    <span class="hljs-tag"><<span class="hljs-name">RelativeLayout</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
      <span class="hljs-attr">xmlns:app</span>=<span class="hljs-string">"http://schemas.android.com/apk/res-auto"</span>
      <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
      <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
      <span class="hljs-attr">android:layout_margin</span>=<span class="hljs-string">"16dp"</span>
      <span class="hljs-attr">android:orientation</span>=<span class="hljs-string">"vertical"</span>></span>

      <span class="hljs-tag"><<span class="hljs-name">android.support.v7.widget.CardView</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/cardView"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
        <span class="hljs-attr">android:layout_gravity</span>=<span class="hljs-string">"start"</span>
        <span class="hljs-attr">app:cardCornerRadius</span>=<span class="hljs-string">"8dp"</span>
        <span class="hljs-attr">app:cardUseCompatPadding</span>=<span class="hljs-string">"true"</span>></span>

        <span class="hljs-tag"><<span class="hljs-name">LinearLayout</span>
          <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
          <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
          <span class="hljs-attr">android:gravity</span>=<span class="hljs-string">"start"</span>
          <span class="hljs-attr">android:orientation</span>=<span class="hljs-string">"vertical"</span>
          <span class="hljs-attr">android:padding</span>=<span class="hljs-string">"8dp"</span>></span>

          <span class="hljs-tag"><<span class="hljs-name">TextView</span>
            <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/text"</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_gravity</span>=<span class="hljs-string">"center_vertical|start"</span>
            <span class="hljs-attr">android:layout_marginBottom</span>=<span class="hljs-string">"4dp"</span>
            <span class="hljs-attr">android:textStyle</span>=<span class="hljs-string">"bold"</span> /></span>

        <span class="hljs-tag"></<span class="hljs-name">LinearLayout</span>></span>

      <span class="hljs-tag"></<span class="hljs-name">android.support.v7.widget.CardView</span>></span>

    <span class="hljs-tag"></<span class="hljs-name">RelativeLayout</span>></span> 
```

最后，打开`ChatRoom`活动类并粘贴这个:

```
 <span class="hljs-comment">// File: ./app/src/main/java/com/example/messengerapp/ChatRoom.kt</span>
    <span class="hljs-keyword">import</span> android.app.Activity
    <span class="hljs-keyword">import</span> android.os.Bundle
    <span class="hljs-keyword">import</span> android.support.v7.app.AppCompatActivity
    <span class="hljs-keyword">import</span> android.support.v7.widget.LinearLayoutManager
    <span class="hljs-keyword">import</span> android.util.Log
    <span class="hljs-keyword">import</span> android.view.View
    <span class="hljs-keyword">import</span> android.view.inputmethod.InputMethodManager
    <span class="hljs-keyword">import</span> com.pusher.client.Pusher
    <span class="hljs-keyword">import</span> com.pusher.client.PusherOptions
    <span class="hljs-keyword">import</span> com.pusher.client.channel.PrivateChannelEventListener
    <span class="hljs-keyword">import</span> com.pusher.client.util.HttpAuthorizer
    <span class="hljs-keyword">import</span> kotlinx.android.synthetic.main.activity_chat_room.*
    <span class="hljs-keyword">import</span> okhttp3.MediaType
    <span class="hljs-keyword">import</span> okhttp3.RequestBody
    <span class="hljs-keyword">import</span> org.json.JSONObject
    <span class="hljs-keyword">import</span> retrofit2.Call
    <span class="hljs-keyword">import</span> retrofit2.Callback
    <span class="hljs-keyword">import</span> retrofit2.Response
    <span class="hljs-keyword">import</span> java.lang.Exception
    <span class="hljs-keyword">import</span> java.util.*

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ChatRoom</span> : <span class="hljs-type">AppCompatActivity</span></span>() {

      <span class="hljs-keyword">companion</span> <span class="hljs-keyword">object</span> {
        <span class="hljs-keyword">const</span> <span class="hljs-keyword">val</span> EXTRA_ID = <span class="hljs-string">"id"</span>
        <span class="hljs-keyword">const</span> <span class="hljs-keyword">val</span> EXTRA_NAME = <span class="hljs-string">"name"</span>
        <span class="hljs-keyword">const</span> <span class="hljs-keyword">val</span> EXTRA_COUNT = <span class="hljs-string">"numb"</span>
      }

      <span class="hljs-keyword">private</span> <span class="hljs-keyword">lateinit</span> <span class="hljs-keyword">var</span> contactName: String
      <span class="hljs-keyword">private</span> <span class="hljs-keyword">lateinit</span> <span class="hljs-keyword">var</span> contactId: String
      <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> contactNumb: <span class="hljs-built_in">Int</span> = -<span class="hljs-number">1</span>
      <span class="hljs-keyword">lateinit</span> <span class="hljs-keyword">var</span> nameOfChannel: String
      <span class="hljs-keyword">val</span> mAdapter = ChatRoomAdapter(ArrayList())

      <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onCreate</span><span class="hljs-params">(savedInstanceState: <span class="hljs-type">Bundle</span>?)</span></span> {
        <span class="hljs-keyword">super</span>.onCreate(savedInstanceState)
        setContentView(R.layout.activity_chat_room)
        fetchExtras()
        setupRecyclerView()
        subscribeToChannel()
        setupClickListener()
      }
    } 
```

在这个文件中，我们声明了用于通过意图向活动发送数据的常量。我们还初始化了稍后将使用的变量，如适配器和联系信息。然后我们在`onCreate`方法中调用了一些额外的方法。让我们将它们添加到课程中。

将下面定义的`fetchExtras`方法添加到该类中。该方法获取聊天室活动发送的额外内容。

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">fetchExtras</span><span class="hljs-params">()</span></span> {
      contactName = intent.extras.getString(ChatRoom.EXTRA_NAME)
      contactId = intent.extras.getString(ChatRoom.EXTRA_ID)
      contactNumb = intent.extras.getInt(ChatRoom.EXTRA_COUNT)
    } 
```

下一个方法是`setupRecyclerView`方法。这将使用适配器和布局管理器初始化回收器视图。将函数粘贴到与之前相同的类中:

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">setupRecyclerView</span><span class="hljs-params">()</span></span> {
      with(recyclerViewChat) {
        layoutManager = LinearLayoutManager(<span class="hljs-keyword">this</span><span class="hljs-symbol">@ChatRoom</span>)
        adapter = mAdapter
      }
    } 
```

下一个方法是`subscribeToChannel`方法。此方法为用户订阅与所选联系人的私人频道。将下面的代码粘贴到与前面相同的类中:

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">subscribeToChannel</span><span class="hljs-params">()</span></span> {
      <span class="hljs-keyword">val</span> authorizer = HttpAuthorizer(<span class="hljs-string">"http://10.0.2.2:5000/pusher/auth/private"</span>)
      <span class="hljs-keyword">val</span> options = PusherOptions().setAuthorizer(authorizer)
      options.setCluster(<span class="hljs-string">"PUSHER_APP_CLUSTER"</span>)

      <span class="hljs-keyword">val</span> pusher = Pusher(<span class="hljs-string">"PUSHER_APP_KEY"</span>, options)
      pusher.connect()

      nameOfChannel = <span class="hljs-keyword">if</span> (Singleton.getInstance().currentUser.count > contactNumb) {
        <span class="hljs-string">"private-"</span> + Singleton.getInstance().currentUser.id + <span class="hljs-string">"-"</span> + contactId
      } <span class="hljs-keyword">else</span> {
        <span class="hljs-string">"private-"</span> + contactId + <span class="hljs-string">"-"</span> + Singleton.getInstance().currentUser.id
      }

      Log.i(<span class="hljs-string">"ChatRoom"</span>, nameOfChannel)

      pusher.subscribePrivate(nameOfChannel, <span class="hljs-keyword">object</span> : PrivateChannelEventListener {
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onEvent</span><span class="hljs-params">(channelName: <span class="hljs-type">String</span>?, eventName: <span class="hljs-type">String</span>?, <span class="hljs-keyword">data</span>: <span class="hljs-type">String</span>?)</span></span> {
          <span class="hljs-keyword">val</span> obj = JSONObject(<span class="hljs-keyword">data</span>)
          <span class="hljs-keyword">val</span> messageModel = MessageModel(obj.getString(<span class="hljs-string">"message"</span>), obj.getString(<span class="hljs-string">"sender_id"</span>))

          runOnUiThread {
            mAdapter.add(messageModel)
          }
        }

        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onAuthenticationFailure</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?, p1: <span class="hljs-type">Exception</span>?)</span></span> {}
        <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onSubscriptionSucceeded</span><span class="hljs-params">(p0: <span class="hljs-type">String</span>?)</span></span> {}
      }, <span class="hljs-string">"new-message"</span>)
    } 
```

> 用仪表盘上的值替换`PUSHER_APP_*`键。

上面的代码允许用户订阅私人频道。私有信道像存在信道一样需要授权。但是，它不公开在其他用户订阅时触发的回调。

下一个要添加的方法是`setupClickListener`。将该方法粘贴到与之前相同的类:

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">setupClickListener</span><span class="hljs-params">()</span></span> {
      sendButton.setOnClickListener{
        <span class="hljs-keyword">if</span> (editText.text.isNotEmpty()) {
          <span class="hljs-keyword">val</span> jsonObject = JSONObject()
          jsonObject.put(<span class="hljs-string">"message"</span>,editText.text.toString())
          jsonObject.put(<span class="hljs-string">"channel_name"</span>,nameOfChannel)
          jsonObject.put(<span class="hljs-string">"sender_id"</span>,Singleton.getInstance().currentUser.id)

          <span class="hljs-keyword">val</span> jsonBody = RequestBody.create(
              MediaType.parse(<span class="hljs-string">"application/json; charset=utf-8"</span>), 
              jsonObject.toString()
          )

          RetrofitInstance.retrofit.sendMessage(jsonBody).enqueue(<span class="hljs-keyword">object</span>: Callback<String>{
            <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onFailure</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">String</span>>?, t: <span class="hljs-type">Throwable</span>?)</span></span> {}
            <span class="hljs-keyword">override</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">onResponse</span><span class="hljs-params">(call: <span class="hljs-type">Call</span><<span class="hljs-type">String</span>>?, response: <span class="hljs-type">Response</span><<span class="hljs-type">String</span>>?)</span></span> {}
          })

          editText.text.clear()
          hideKeyBoard()
        }

      }
    } 
```

上面的方法为浮动动作按钮分配了一个 click 侦听器，以将消息发送到服务器。消息发出后，我们清除文本视图并隐藏键盘。

在同一个类中添加一个隐藏键盘的方法，如下:

```
 <span class="hljs-keyword">private</span> <span class="hljs-function"><span class="hljs-keyword">fun</span> <span class="hljs-title">hideKeyBoard</span><span class="hljs-params">()</span></span> {
      <span class="hljs-keyword">val</span> imm = getSystemService(Activity.INPUT_METHOD_SERVICE) <span class="hljs-keyword">as</span> InputMethodManager
      <span class="hljs-keyword">var</span> view = currentFocus

      <span class="hljs-keyword">if</span> (view == <span class="hljs-literal">null</span>) {
        view = View(<span class="hljs-keyword">this</span>)
      }

      imm.hideSoftInputFromWindow(view.windowToken, <span class="hljs-number">0</span>)
    } 
```

申请到此为止。现在，您可以在 Android Studio 中运行您的应用程序，您应该会看到应用程序在运行。

> 在运行 Android 应用程序之前，确保我们之前构建的 Node.js API 正在运行。

[![](../Images/31cd54d82454f960d3d5bd20271cd395.png)T2】](//images.ctfassets.net/1es3ne0caaid/mmdMFfq7WoYsk26cIKocs/486ce2b458e45a41cfeb01ab4441f3b4/android-messenger-presence-demo.gif)

## 结论

在本文中，您已经再次了解了 Pusher 的一些功能，例如私有和在线通道。我们学习了如何对各种渠道的用户进行身份验证。我们使用这些通道实现了两个人之间的私人聊天和一个联系人的在线通知。

本文中构建的应用程序的源代码可以在 [GitHub](https://github.com/neoighodaro/kotlin-messenger-app-with-online-presence-status) 上获得。

这篇文章最初出现在 [Pusher 博客](https://pusher.com/tutorials/android-messenger-presence-kotlin)上。