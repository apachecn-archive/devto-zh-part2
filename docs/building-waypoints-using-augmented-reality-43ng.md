# 使用增强现实构建路点

> 原文：<https://dev.to/issartcompany/building-waypoints-using-augmented-reality-43ng>

## 简介

自从增强现实(AR)和虚拟现实(VR)被介绍给人们以来，已经过去了很长时间，这是一个非常有前途的公告。新技术应该会改变和改善我们生活的方方面面。现在我们有一堆使用 AR 技术的应用。这些应用从游戏(如[战锤 40，000 免费刀片](https://itunes.apple.com/us/app/warhammer-40-000-freeblade/id1043640363?mt=4)、[机器](https://itunes.apple.com/us/app/the-machines/id1280682965?mt=4))到一些在日常生活中真正帮助我们的例子( [AR MeasureKit](https://itunes.apple.com/us/app/ar-measurekit/id1258270451?mt=8) 、 [Sky Guide AR](https://itunes.apple.com/us/app/sky-guide-ar/id576588894?mt=8) 、 [IKEA Place](https://itunes.apple.com/us/app/ikea-place/id1279244498?mt=8) )。所有这些应用程序只使用手机本身，但这项技术最有用的成果是当它与另一个结合时。增强现实可以与 GPS 定位相结合，在不同情况下提供视觉方向。今天，我们有许多导航器，引导用户从 A 点到 b 点。问题是，用户在现实世界中看不到这条路径。只在地图上。增强现实来帮助我们了。让我们构建一个增强现实应用程序，它将沿着地图上显示的路径引导我们。为了执行增强现实开发，我将使用 Swift 语言，但是这些例子可以很容易地转换成 Objective-C 代码。

## 一点点数学

在进入增强现实实现之前，我们需要清楚地了解我们计划做什么。地球表面的每个位置都可以用两个数字来表示:纬度和经度。
**纬度**是北极或南极与赤道(地球两极中间的假想圆)之间的距离。赤道以北的地方从 0 到 90，赤道以南的地方从 0 到-90。
**经度**是本初子午线(一条从北到南穿过英国格林威治的假想线)到西方或东方一点的距离。本初子午线以东的地方从 0 度到 180 度，本初子午线以西的地方从 0 度到-180 度。例如，如果你在巴西，你的纬度和经度将是负的，因为你在地球的西南侧:

[![Map](../Images/0dbaadb986f4e8443dbc8ff3ae0e066d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tztCQxkO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://issart.com/blog/wp-content/uploads/2018/06/earth_map.png)

我们的应用程序会将您的位置考虑在内，并在该位置放置 AR 场景上的标记。然后，我们获取路径数组中的第一个位置，并使用前一个标记(用户位置的标记)来构建它。然后，我们将获取路径数组中的第二个位置，并使用第一个位置的标记来构建它，依此类推。为了计算下一个航路点位置，我们需要两个值:

1.  两点之间的距离(下一个和当前位置)
2.  地球北(或南)线与当前点和下一点连线的夹角，称为[方位](https://en.wikipedia.org/wiki/Bearing_%28navigation%29)。

[![Bearing](../Images/2711314529e05091be928ee98db2f4c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w5IfF0Lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://issart.com/blog/wp-content/uploads/2018/06/schema.png)

如果我们谈论的是一个[с自流坐标系](https://en.wikipedia.org/wiki/Cartesian_coordinate_system)，我们可以通过应用[勾股定理](https://en.wikipedia.org/wiki/Pythagorean_theorem)和一些简单的三角学，用[正弦](https://en.wikipedia.org/wiki/Sine)和[余弦](https://en.wikipedia.org/wiki/Law_of_cosines)运算得到那些计算。但是我们谈论的是地球的纬度和经度。由于地球表面不是平的，数学变得更加复杂。通过调用类 [CLLocation](https://developer.apple.com/documentation/corelocation/cllocation) 的[方法](https://developer.apple.com/documentation/corelocation/cllocation/1423689-distance)来计算距离。它使用[哈弗辛公式](https://www.igismap.com/haversine-formula-calculate-geographic-distance-earth/)，该公式从两个不同的纬度/经度值对中，通过沿着地球曲率在它们之间描绘一条线来计算距离。另一方面，我们必须手动计算两个不同纬度/经度值对之间的方位角。[这是公式](https://www.igismap.com/formula-to-find-bearing-or-heading-angle-between-two-points-latitude-longitude/):

**atan2 ( X，Y )**

其中 X 等于:
**sin(long 2-long 1)* cos(long 2)**

而 Y 等于:
**cos(la T1)* sin(la T2)-sin(la T1)* cos(la T2)* cos(long 2-long 1)π**

另一件要考虑的事情是，对于矩阵变换，我们将不得不使用弧度而不是度作为角度单位。由于整个圆周的长度等于 2π弧度(360°)，一个[弧度](https://en.wikipedia.org/wiki/Radian)等于 180/π度。因此，我们的计划是，当我们从谷歌服务接收到一系列路点时，使用上面解释的公式，在 AR 世界中相对于您的位置的第一个路点的位置放置一个 3D 模型(或任何其他对象)。然后，我们放置相对于第一个路标的第二个路标，依此类推。

## 初始设置

首先，我们需要将谷歌地图添加到我们的项目中。你可以使用[谷歌开发者控制台](https://console.developers.google.com/apis/dashboard)来完成。为此，我们需要在我们的项目中添加 iOS 版 Google Maps SDK。你可以使用 [CocoaPods](https://cocoapods.org/about) 或者手动安装。无论哪种方式，请参考谷歌指南的[谷歌地图 SDK](https://developers.google.com/maps/documentation/ios-sdk/start) 的正确安装步骤，根据所选的方法。然后我们初始化视图和相机来显示我们的地图。就几行:

```
let camera = GMSCameraPosition.camera(withLatitude: lat, longitude: long, zoom: 18.0)
mapView = GMSMapView.map(withFrame: defaultMapView.bounds, camera: camera)
mapView.isMyLocationEnabled = true
mapView.delegate = self
view.addSubview(mapView) 
```

Enter fullscreen mode Exit fullscreen mode

代替*‘lat’*和*‘long’*变量，你可以插入任何你想要的坐标来使你的相机在开始时居中。或者您可以使用 *mapView* 对象的 *myLocation* 属性来获取您的当前位置，并将您的地图置于该位置的中心。之后，我们需要实现在地图上放置标记的功能，我们将使用这些标记来指示路径的起点和终点。在地图上添加标记是一项非常简单且常见的任务，只需在视图控制器类中实现 delegate 方法:

```
public func mapView(_ mapView: GMSMapView, didTapAt coordinate: CLLocationCoordinate2D) {
destinationMarker?.map = nil
destinationMarker = GMSMarker(position: coordinate)
destinationMarker!.position.latitude = coordinate.latitude
destinationMarker!.position.longitude = coordinate.longitude
destinationMarker!.map = mapView
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用谷歌服务

接下来，我们需要添加“获取路径”按钮。此按钮的回调代码将向 Google 服务发送一个请求，提供用户位置的坐标和我们地图上的目的地标记位置。根据您自己的偏好和需求，有许多方法可以执行对服务器的 GET 请求，您可以按照自己的方式实现它。我们需要使用以下格式向服务发送请求:[https://maps . Google APIs . com/maps/API/directions/* * output format？参数**其中](https://maps.googleapis.com/maps/api/directions/**outputFormat?parameters**%C2%A0where) **outputFormat** 是服务器回答的期望格式(可以是 XML 或者 JSON，我们会用 JSON)和**参数**——请求参数。我们将只使用其中的五个，但是你可以在这里找到一个完整的可能参数列表。我们需要发送:

*   原点——我们路径起点的坐标；
*   目的地——路径的终点；
*   模式-选择我们的运动类型；
*   关键-需要提供我们的应用程序的 Googlekey。

你的要求应该是这样的:[https://maps.googleapis.com/maps/api/directions/**json?起点=纬度，lng &终点=纬度，lng &传感器=真&模式=行走&按键=googleKey**](https://maps.googleapis.com/maps/api/directions/**json?origin=lat,lng&destination=lat,lng&sensor=true&mode=walking&key=googleKey**) 。我们将 JSON 设置为我们的响应格式，而不是 **lat** 和 **lng** 你应该放置你的起点和终点的纬度和经度。我将模式参数设置为行走值，但是您可以选择任何您想要的模式。最后，您需要用您的应用程序 Google ID 替换 **googleKey** 。作为对此请求的响应，您将收到一个包含大量数据的大 JSON。我们不需要所有这些数据，我们需要解析响应并从中提取路由数组。通常这个数组只包含一条路线(如果可以在提供的坐标之间建立一条路线)，在响应处理过程中，我们将简单地从这个数组中选择第一条路线。服务响应中的路由由特殊格式的字符串表示。你需要做的就是:

*   解析服务响应并获取路由数组；
*   从此数组中取出第一个路由字符串；
*   使用此字符串创建 GMSPath 对象:GMS path . init(from encodedpath:route string)；
*   获取 GMSPath 对象航路点作为 CLLocations。

列表的最后一项可以使用下面的代码实现:

```
for index in 0...path!.count() - 1 {
let pinLocation = CLLocation(coordinate: (path?.coordinate(at: index))!, altitude: 236)
self.locations.append(pinLocation)
} 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经有了一个实现了 Google 地图显示和路径查找功能的应用程序。我们可以从 Google 服务请求一条路径，并处理响应以获取作为 CLLocation 的路点。现在我们可以进入这项工作的主要部分-AR 部分。

## 实现增强现实

首先，我们将创建一个新的视图控制器。并添加 [ARSCNView](https://developer.apple.com/documentation/arkit/arscnview) 或 [ARSKView](https://developer.apple.com/documentation/arkit/arskview) (如果你想在场景中添加 3D 对象，应该使用第一个，后者用于 2D 图像和精灵)来显示相机和增强现实对象。为了完成 AR 场景的初始设置，我们需要将视图添加到这个场景中。

```
public override func viewDidLoad() {
super.viewDidLoad()
let scene = SCNScene()
sceneView.delegate = self
sceneView.scene = scene
} 
```

Enter fullscreen mode Exit fullscreen mode

```
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(animated)
let configuration = ARWorldTrackingConfiguration()
configuration.worldAlignment = .gravityAndHeading
sceneView.session.run(configuration)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
override func viewWillDisappear(_ animated: Bool) {
super.viewWillDisappear(animated)
sceneView.session.pause()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要导入表示增强现实场景中的路点的对象。正如上面提到的，我们可以使用 3D 模型或 2D 精灵来达到这个目的。在上面的代码中，我们使用了 *ARSCNView* ，对于航路点，我们将使用 3D 模型。如果您使用 XCode 的增强现实项目模板创建了您的项目，那么您的项目工作区中应该有*art . scna sets*文件夹。如果您使用了任何其他模板，您需要自己创建该文件夹。接下来，打开新建文件对话框，向下滚动选择*资产目录*类型。在名称字段中输入:*art . scnassets*并确认扩展名更改(从 xcassets 到 scna sets)。

当你的项目中有 art.scnassets 文件夹时，你需要在其中添加所有需要的视觉资源(精灵、纹理、模型等)。iOS AR 应用中最常用的 3D 模型格式有。我们需要将模型文件复制到 art.scnassets 文件夹中。然后返回 XCode，打开场景图形视图，选择模型的主节点，在 properties 选项卡中，给它一个名称，您将使用它在代码中引用它，例如:*‘waypoint’*。

## 实现位置计算

现在我们将回到本文开头的公式，并在代码中实现它们。为了在增强现实场景坐标中转换地理位置坐标，我们将使用函数:

```
func translateNode (_ location: CLLocation) -> SCNVector3 {
let locationTransform = transformMatrix(matrix_identity_float4x4, userLocation, nextWaypoint)
return positionFromTransform(locationTransform)
} 
```

Enter fullscreen mode Exit fullscreen mode

主要计算隐藏在 **transformMatrix** 函数后面。为了计算变换矩阵，我们:

1.  用一个单位矩阵(我们不一定要用相机的矩阵之类的，航点的位置和方向与你的位置和方向无关)。
2.  使用上一节中说明的公式计算方位角:atan2(sin(long 2-long 1)* cos(long 2)，cos(la t1)* sin(la T2)-sin(la t1)* cos(la T2)* cos(long 2-long 1))
3.  使用一个单位矩阵，得到一个使用该方位的 y 轴旋转矩阵。
4.  距离由 z 轴给出，因此用 z 位置的距离创建一个四元素向量来获得一个平移矩阵。
5.  将两个矩阵相乘(记住，顺序很重要)来组合它们。
6.  通过将上一步的结果乘以作为参数传递的矩阵，获得最终的转换。

这些步骤可以通过以下方式实现(主变换功能):

```
func transformMatrix(_ matrix:simd_float4x4,_ originLocation:CLLocation, _ waypointLocation: CLLocation) -> simd_float4x4 {
let bearing = bearingBetweenLocations(userLocation, waypointLocation)
let rotationMatrix = rotateAroundY(matrix_identity_float4x4, Float(bearing))
let position = vector_float4(0.0, 0.0, -distance, 0.0)
let translationMatrix = getTranslationMatrix(matrix_identity_float4x4, position)
let transformMatrix = simd_mul(rotationMatrix, translationMatrix
return simd_mul(matrix, transformMatrix)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是上面序列中第二步的函数。我们计算方位角，注意我们使用弧度而不是角度，正如上面提到的:

```
func bearingBetweenLocations(_ originLocation: CLLocation, _ waypointLocation: CLLocation) -> Double {
let lat1 = originLocation.coordinate.latitude.toRadians()
let lon1 = originLocation.coordinate.longitude.toRadians()
let lat2 = waypointLocation.coordinate.latitude.toRadians()
let lon2 = waypointLocation.coordinate.longitude.toRadians()
let longitudeDiff = lon2 - lon1
let y = sin(longitudeDiff) * cos(lat2);
let x = cos(lat1) * sin(lat2) - sin(lat1) * cos(lat2) * cos(longitudeDiff);
return atan2(y, x)
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个旋转矩阵:

```
func rotateAroundY(_ matrix: simd_float4x4, _ degrees: Float) -> simd_float4x4
{
var matrix = matrix
matrix.columns.0.x = cos(degrees)
matrix.columns.0.z = -sin(degrees)
matrix.columns.2.x = sin(degrees)
matrix.columns.2.z = cos(degrees)
return matrix.inverse
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要转换矩阵来处理距离值。

```
func getTranslationMatrix(_ matrix:simd_float4x4, _ translation:vector_float4)->simd_float4x4 {
var matrix = matrix
matrix.columns.3 = translation
return matrix
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要做的就是将我们的 **transformMatrix** 函数应用于来自谷歌服务的所有坐标，并在转换后的坐标上放置标记或放置 3D 模型(而不是 **waypoint.dae** 你应该使用你的模型的名称):

```
let modelScene = SCNScene(named: "art.scnassets/waypoint.dae")!
self.modelNode = modelScene.rootNode.childNode(withName: rootNodeName, recursively: true)!
self.modelNode.position = translateNode(location)
sceneView.scene.rootNode.addChildNode(self.modelNode) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这个 AR 应用程序可以用来引导用户沿着整个路径从一个路点到另一个路点。这不是增强现实技术和移动开发的新趋势。我们有一些 AR 应用程序在机场大楼内指导用户。这些应用程序与我们的示例之间的主要区别在于，我们使用真实的用户地理定位和位置，并将它们转换为增强现实坐标。

在当前的实现中，增强现实解决方案对于用户来说不是很舒服。这个演示的主要目的是展示如何使用真实的地理位置，并将其应用于增强现实。为了使这项技术可行，我们应该为 HoloLens 或谷歌眼镜开发这种应用程序。