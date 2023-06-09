# 你用什么代码片段？

> 原文：<https://dev.to/itachiuchiha/what-code-snippets-do-you-use-1g66>

你好。有时，我们创建代码片段来帮助我们。在一个项目中，我需要创建一个元素，并且应该已经设置了它的属性。我没有使用 jQuery 或类似的库。我已经创建了一个类似的 JavaScript 函数。

```
const element = (data) => {
    const el = document.createElement(data["tag"])

    if(data["type"]) {
        el["type"] = data["type"]
    }

    if(data["id"]) {
        el["id"] = data["id"]
    }

    if(data["class"]) {
        const classList = data["class"]

        classList.map(m => {
            el.classList.add(m)
        })
    }

    if(data["text"]) {
        el["textContent"] = data["text"]
    }

    if(data["html"]) {
        el["innerHTML"] = data["html"]
    }

    if(data["attr"]) {
        const attr = data["attr"]
        const keys = Object.keys(data["attr"])

        keys.map(m => {
            el.setAttribute(m, attr[m])
        })
    }

    if(data["event"]) {
        const attr = data["event"]
        const keys = Object.keys(data["event"])

        keys.map(m => {
            const eventName = "on" + m
            el.setAttribute(eventName, attr[m])
        })
    }

    if(data["addTo"]) {
        const target = document.querySelectorAll(data["addTo"])

        target.forEach((m, i) => {
            m.appendChild(el)
        })
    }

    if(data["style"]) {
        const attr = data["style"]
        const keys = Object.keys(data["style"])
        let styleCode = '';
        keys.map(m => {
            const props = attr[m]
            const prop_keys = Object.keys(attr[m])

            styleCode += `${m} {`

            prop_keys.map(p => {
                styleCode += `${p}:${props[p]};`
            })

            styleCode += '}'
        })

        const style = document.createElement('style');
        style.type = 'text/css';
        style.innerHTML = styleCode;
        document.getElementsByTagName('head')[0].appendChild(style);
    }

    return el
} 
```

Enter fullscreen mode Exit fullscreen mode

这个功能对我总是有效。如果你想使用这个功能，你可以这样使用；

```
// Example Usage

element({
    'tag': 'button',
    'type': 'button',
    'addTo': '.test-body',
    'html': '<img src="https://cdn-images-1.medium.com/max/2000/0*uORWkYMjiF3LA-K8" />', // or text
    'id': 'user-card',
    'class': ['profile-link', 'avatar'],
    'attr': {
        'name': 'user-name',
        'value': 'Ali',
        'data-id': 1,
        'data-path': '/profile/1'
    },
    'event': {
        "click": `console.log(123)`
    },
    'style': {
        '.profile-link': {
            "color": "#fff",
            "background-color": "#000"
        },
        ".profile-link:hover": {
            "color": "#000",
            "background-color": "#fff",
            "border": "1px dashed #000;"
        },
        ".avatar": {
            "width": "300px",
            "height": "300px",
            "border": "1px dashed #e3e3e3;"
        },
        ".avatar img": {
            "width": "285px",
            "height": "285px"
        }
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

[用密码笔检查](https://codepen.io/aligoren/pen/MqdWPz?editors=1010)

反正我就纳闷了，你用的是什么代码片段？