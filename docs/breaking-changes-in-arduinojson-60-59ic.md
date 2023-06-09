# ArduinoJson 6.0 中的突破性变化

> 原文：<https://dev.to/wiaio/breaking-changes-in-arduinojson-60-59ic>

如果你正在用任何 Arduino 相关的项目处理 JSON，我们有一个快速更新给你。

如果您正在使用库`ArduinoJson`来使您的生活变得更容易，您可能会遇到之前编写的教程(包括我们的一些教程)的问题，您的代码无法编译，并出现与`jsonBuffer`声明相关的错误。

别担心，我们有适合您的解决方案。

ArduinoJson 6.0 版本最近发布，它包含了一个突破性的变化，这很可能是 Json 相关问题的原因。

在最新版本中，`JsonBuffer`的概念已经被`JsonDocument`的概念所取代。

以前，您可以像这样创建一个 JSON 对象:

`StaticJsonBuffer<512> jsonBuffer;
JsonObject& root = jsonBuffer.createObject();` 
有了新的变化，你可以这样做:

`StaticJsonDocument<512> jsonBuffer;
JsonObject& root = jsonBuffer.to<JsonObject>();` 
在之前版本的 ArduinoJson 中，你会使用`printTo()`方法将 Json 序列化成一个字符串，例如:

`data.printTo(dataStr);`

现在，您需要使用新的`serializeJson`函数:

`serializeJson(data, dataStr);`

关于 ArduinoJson 6.0 中其他变化的更多信息，请查看本文。