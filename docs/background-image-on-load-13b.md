# 加载背景图像

> 原文：<https://dev.to/alirezavalizade/background-image-on-load-13b>

# 背景-图像-加载

检查`background-image`加载的简单方法。

### 安装

npm:

```
npm install background-image-on-load --save 
```

Enter fullscreen mode Exit fullscreen mode

纱线:

```
yarn add background-image-on-load 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
import React, { Component } from 'react';
import BackgroundImageOnLoad from 'background-image-on-load';

class App extends Component {

  state = {
    bgIsLoaded: false
  };

  render() {
    const { bgIsLoaded } = this.state;
    return (
        <div
          style={{ 
            height: 300,
            backgroundPosition: 'center',
            backgroundSize: 'cover',
            backgroundImage: `url(${!bgIsLoaded ? 'https://picsum.photos/310/310?blur' : 'https://picsum.photos/1600/310'})`
          }}
        >
          <BackgroundImageOnLoad
            src={'https://unsplash.it/1200/310?random'}
            onLoadBg={() =>
              this.setState({
              bgIsLoaded: true
            })}
            onError={err => console.log('error', err)}
          />
        </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢这个库，请点击 github 上的开始按钮。

[https://github.com/alirezavalizade/background-image-on-load](https://github.com/alirezavalizade/background-image-on-load)