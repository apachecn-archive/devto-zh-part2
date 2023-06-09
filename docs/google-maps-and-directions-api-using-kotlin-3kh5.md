# 使用 Kotlin 的谷歌地图和方向 API

> 原文：<https://dev.to/lawgimenez/google-maps-and-directions-api-using-kotlin-3kh5>

[![Google Maps and Directions API using Kotlin](img/f1738e827dfd676a915f439f4c23913b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---_LfXuNC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/09/Google-Map-screenshot-1.png)

我将在下面实现如何使用 Kotlin 实现谷歌地图和方向 API，我们将绘制一条从**起点**到**目的地**的路线。我不打算在这里介绍如何从谷歌地图平台控制台获取 API 密钥，因为[谷歌对此有明确的指示](https://developers.google.com/maps/documentation/android-sdk/signup)。

我们将假设您已经获得了 API 密钥，并且已经在 **AndroidManifest.xml** 中添加了`<meta-data>`。

在你的 gradle 文件中，添加下面几行，然后同步。

```
implementation 'com.google.android.gms:play-services-maps:15.0.1'
implementation 'com.google.maps.android:android-maps-utils:0.5' 
```

Enter fullscreen mode Exit fullscreen mode

在 XML 布局中，将以下代码添加到任意位置。在我这边，我把它添加到了工具栏下面。

```
<fragment
    android:id="@+id/fragment_map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_below="@+id/toolbar"
    /> 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的活动中，我们需要实现`OnMapReadyCallback`。您的活动应该如下图所示。

```
class HomeActivity: AppCompatActivity(), OnMapReadyCallback {

private var googleMap: GoogleMap? = null

override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_home)
        val mapFragment = supportFragmentManager.findFragmentById(R.id.fragment_map) as SupportMapFragment
        mapFragment.getMapAsync(this)
    }

override fun onMapReady(googleMap: GoogleMap?) {
        this.googleMap = googleMap
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行您的项目，它应该会显示谷歌地图。

接下来我们要绘制一条从起点到终点的路线。在您的覆盖方法`onMapReady`中添加下面几行。

```
val latLngOrigin = LatLng(10.3181466, 123.9029382) // Ayala
val latLngDestination = LatLng(10.311795,123.915864) // SM City
      this.googleMap!!.addMarker(MarkerOptions().position(latLngOrigin).title("Ayala"))
        this.googleMap!!.addMarker(MarkerOptions().position(latLngDestination).title("SM City"))
       this.googleMap!!.moveCamera(CameraUpdateFactory.newLatLngZoom(latLngOrigin, 14.5f)) 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码块中，我们创建了一个变量`latLngOrigin`,因为这将是我们路线的起点。变量`latLngDestination`将是目的地或路线的终点。Next lines 将以下变量作为标记添加到谷歌地图中。

### 方向 API

我们将使用[谷歌的方向 API](https://developers.google.com/maps/documentation/directions/start) ，这样我们就可以计算两个端点之间的路线。尝试阅读并熟悉文档。

因为我们只有坐标，我们可以将它们传递给方向 API，如下所示。替换您自己的 API 密钥。

```
https://maps.googleapis.com/maps/api/directions/json?origin=10.3181466,123.9029382&destination=10.311795,123.915864&key=<YOUR_API_KEY> 
```

Enter fullscreen mode Exit fullscreen mode

示例 JSON 响应可以在文档中找到。所以看一看吧。

请注意，我还使用了凌空作为我的 HTTP 请求库。

```
val path: MutableList<List<LatLng>> = ArrayList()
val urlDirections = "https://maps.googleapis.com/maps/api/directions/json?origin=10.3181466,123.9029382&destination=10.311795,123.915864&key=<YOUR_API_KEY>"
val directionsRequest = object : StringRequest(Request.Method.GET, urlDirections, Response.Listener<String> {
            response ->
            val jsonResponse = JSONObject(response)
            // Get routes
            val routes = jsonResponse.getJSONArray("routes")
            val legs = routes.getJSONObject(0).getJSONArray("legs")
            val steps = legs.getJSONObject(0).getJSONArray("steps")
            for (i in 0 until steps.length()) {
                val points = steps.getJSONObject(i).getJSONObject("polyline").getString("points")
                path.add(PolyUtil.decode(points))
            }
            for (i in 0 until path.size) {
                this.googleMap!!.addPolyline(PolylineOptions().addAll(path[i]).color(Color.RED))
            }
        }, Response.ErrorListener {
            _ ->
        }){}
 val requestQueue = Volley.newRequestQueue(this)
 requestQueue.add(directionsRequest) 
```

Enter fullscreen mode Exit fullscreen mode

下面的代码块向方向 API 发送一个 HTTP GET 请求，我们将解析该响应。我们需要的是来自`polyline`对象的`points`数据。运行这个项目，你应该会在谷歌地图上绘制出一条路线。

[![Google Maps and Directions API using Kotlin](img/9b6596a116a157a883174e450444cbd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UuIYYroj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/09/Google-Map-screenshot.png)