# 等待 Javascript 加载脚本

> 原文：<https://dev.to/timber/wait-for-a-script-to-load-in-javascript-579k>

曾经需要等待脚本加载后再使用它吗？这里有一个快速简单的方法来做到这一点。非常适合您无法控制的第三方脚本，如 Google Analytics、Segment、Intercom 等。

你所需要的就是这个小课堂:

```
export default class ScriptLoader {
  constructor (options) {
    const { src, global, protocol = document.location.protocol } = options
    this.src = src
    this.global = global
    this.protocol = protocol
    this.isLoaded = false
  }

  loadScript () {
    return new Promise((resolve, reject) => {
      // Create script element and set attributes
      const script = document.createElement('script')
      script.type = 'text/javascript'
      script.async = true
      script.src = `${this.protocol}//${this.src}`

      // Append the script to the DOM
      const el = document.getElementsByTagName('script')[0]
      el.parentNode.insertBefore(script, el)

      // Resolve the promise once the script is loaded
      script.addEventListener('load', () => {
        this.isLoaded = true
        resolve(script)
      })

      // Catch any errors while loading the script
      script.addEventListener('error', () => {
        reject(new Error(`${this.src} failed to load.`))
      })
    })
  }

  load () {
    return new Promise(async (resolve, reject) => {
      if (!this.isLoaded) {
        try {
          await this.loadScript()
          resolve(window[this.global])
        } catch (e) {
          reject(e)
        }
      } else {
        resolve(window[this.global])
      }
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 示例用法

```
const loader = new Loader({
    src: 'cdn.segment.com/analytics.js',
    global: 'Segment',
})

// scriptToLoad will now be a reference to `window.Segment`
const scriptToLoad = await loader.load() 
```

Enter fullscreen mode Exit fullscreen mode