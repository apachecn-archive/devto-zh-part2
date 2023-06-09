# 包含 vue.js 但不包含 vue 库的 google map 示例

> 原文：<https://dev.to/terrierscript/example-for-google-map-with-vuejs-without-vue-library--3gf5>

我想在 vue.js 中使用谷歌地图，它可以像 vue 2-谷歌地图库一样使用，但我想更直接地使用谷歌 API。

# 输出

演示:[https://vue-google-map-provider-sample.netlify.com/](https://vue-google-map-provider-sample.netlify.com/)
来源:[https://github . com/inuscript/example-vue-inject-provide-Google-map](https://github.com/inuscript/example-vue-inject-provide-google-map)

# 器物

我创造那些组件。

*   index.html
*   MyMap.vue(地图父级。组合一些组件)
*   MapLoader.vue(仅处理谷歌地图回调)
*   MapProvider.vue(仅使用 vue.js `provide`)
*   child marker . vue(`marker`子元素的例子)

## index.html

```
 <!-- index.html -->
    <div id="app">
      <my-map :markers='[
        {"lat":35.6432027,"lng":139.6729435},
        {"lat":35.5279833,"lng":139.6989209},
        {"lat":35.6563623,"lng":139.7215211},
        {"lat":35.6167531,"lng":139.5469376},
        {"lat":35.6950961,"lng":139.5037899}
      ]'>
      </my-map>
    </div> 
```

一般是`index.html`

## MyMap.vue

组件父示例(不太重要)。

```
<!-- MyMap.vue -->
<template>
  <div>
    <h1>Map</h1>
    <map-loader 
      :map-config="mapConfig"
      apiKey="YOUR API KEY"
    >
      <template v-for="marker in markers">
        <child-marker :position="marker" />
      </template>
    </map-loader>
  </div>
</template>

<script>
import MapLoader from "./MapLoader.vue"
import ChildMarker from './ChildMarker'

export default {
  props: {
    markers: Array
  },
  data(){
    return {
      mapConfig: {
        zoom: 12,
        center: this.markers[0]
      }
    }
  },
  components: {
    MapLoader,
    ChildMarker
  }
}
</script> 
```

这叫`map-loader`，在内部用`child-marker`。

## MapLoader

这是最核心的实现(第 1 部分)。

```
<!-- MapLoader.vue -->
<template>
  <div>
    <div id="map"></div> <!-- point 1 -->
    <template v-if="!!this.google && !!this.map"> <!-- point 2 -->
      <map-provider
        :google="google"
        :map="map"
      >
        <slot/>
      </map-provider>
    </template>
  </div>
</template>

<script>
import GoogleMapsApiLoader from 'google-maps-api-loader'
import MapProvider from './MapProvider'

export default {
  props:{
    mapConfig: Object,
    apiKey: String
  },
  components: {
    MapProvider
  },
  data(){
    return {
      google: null,
      map: null
    }
  },
  mounted () { // point 3
    GoogleMapsApiLoader({
      apiKey: this.apiKey
    }).then((google) => {
      this.google = google
      this.initializeMap()
    })
  },
  methods: {
    initializeMap (){
      const mapContainer = this.$el.querySelector('#map') // point 1
      const { Map } = this.google.maps
      this.map = new Map(mapContainer, this.mapConfig)
    }
  }
}
</script>

<style scoped>
#map {
  height: 100vh;
  width: 100%;
}
</style> 
```

这个成分有些分。

*   要点 1:这使用了`#map`和原始 DOM。装上`this.$el`后
*   第二点:核心的核心。此**暂停**挂载子节点，直到`google`或`map`值有效。这个使可以用`provide/inject`。
*   第三点:调用`mounted()`生命周期时调用`GoogleMapApiLoader`。

## 地图提供者

核心实现(第二部分)
这个设置`provide`并传递子值给`google`和`map`值。

Vue 的`provide`仅在第一次评估。
如果在`map-loader`上设置`provide`，`google`和`map`值可能为空值，并且不会更新。

在这个实现中，`map-loader`通过评估`google`和`map`的值，`map-provider`可以得到有效值。

```
<!-- MapProvider.vue -->
<template>
  <div>
    <slot />
  </div>
</template>

<script>
export default {
  props: {
    google: Object,
    map: Object,
  },
  provide() {
    return {
      google: this.google,
      map: this.map
    }
  },
}
</script> 
```

## 儿童标记

```
<!-- ChildMarker.vue -->
<template></template>
<script>
export default {
  inject: ["google", "map"],
  props: {
    position: Object
  },
  data(){
    return { marker: null}
  },
  mounted(){
    const { Marker } = this.google.maps
    this.marker = new Marker({
      position: this.position,
      map: this.map,
      title: "Child marker!"
    })
  }
}
</script> 
```

标记子的示例。
与`MapLoader`一样使用`inject`和`mounted()`

# 不使用提供/注入模式(使用插槽范围)

在官方文件中，`provide / inject`不是推荐的通用应用。

如果不使用`provide / inject`，我们必须将值传递给孩子。
在这个实现中，我们也需要`slot-scope`。

`MapLoader`去掉`MapProvider`，将`google`和`map`传给`slot`T5

```
<!-- MapLoader.vue -->
<template>
  <div>
    <div id="map"></div>
    <template v-if="!!this.google && !!this.map">
      <slot
        :google="google"
        :map="map"
      />
    </template>
  </div>
</template>

<script>
 :
</script> 
```

接下来，在`MyApp`中，使用`slot-scope`并将值传递给`child-marker`T3】

```
<!-- MyMap -->
<template>
  <div>
    <h1>Map</h1>
    <map-loader 
      :map-config="mapConfig"
      apiKey="YOUR API KEY">
      <template slot-scope="scopeProps"> <!-- slot-scope -->
        <child-marker 
          v-for="(marker,i) in markers"
          :key="i"
          :position="marker" 
          :google="scopeProps.google"
          :map="scopeProps.map"
        />
      </template>
    </map-loader>
  </div>
</template> 
```

`ChildMarker`去掉`inject`，用`props`得到数值。

```
<!-- ChildMarker -->
<template></template>
<script>
export default {
  props: {
    google: Object,
    map: Object,
    position: Object
  },
  data(){
    return { marker: null }
  },
  mounted(){
    const { Marker } = this.google.maps
    this.marker = new Marker({
      position: this.position,
      map: this.map,
      title: "Child marker!"
    })
  }
}
</script> 
```