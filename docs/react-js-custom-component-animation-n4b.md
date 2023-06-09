# React JS 自定义组件动画

> 原文：<https://dev.to/mcdappdev/react-js-custom-component-animation-n4b>

我有一个 ReactJS 组件，看起来像这样:

```
import React from 'react';

class Circle extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            error: null,
            isLoaded: false,
            percent: null
        };
    }    

    componentDidMount() {
        const url = "base url here" + this.props.endpoint;
        fetch(url)
            .then(res => res.json())
            .then(
                (result) => {
                    this.setState({
                        isLoaded: true,
                        percent: result.percent
                    });
                },
                (error) => {
                    this.setState({
                        isLoaded: true,
                        error: error
                    });
                }
            )
    }

    render() {
        return (
            <div className={"col-md-" + this.props.size + " progress-circles"} data-animate-on-scroll="on">
                <div className="progress-circle" data-circle-percent={"" + this.state.percent + ""} data-circle-text="">
                <h4>{this.props.title}</h4>
                    
                        <circle r="80" cx="90" cy="90" fill="transparent" strokeDasharray="502.4" strokeDashoffset="0"></circle>
                        <circle className="bar" r="80" cx="90" cy="90" fill="transparent" strokeDasharray="502.4" strokeDashoffset="0"></circle>
                    
                </div>
            </div>
        );
    }
}

export default Circle; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这工作像一个总的魅力。唯一的问题是，有一个与这个元素相关联的动画填充了进度条，但在我设置了`componentDidMount`函数的值之后，它没有出现。如果我通过父组件中的 props 设置值，动画**会发生**吗？我错过了什么吗？我刚开始有所反应，所以事情可能很简单。提前感谢！