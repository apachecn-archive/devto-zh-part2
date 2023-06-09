# 反应-统一:可编辑的布局系统

> 原文：<https://dev.to/mizchi/react-unite-editable-layout-system-4pg4>

我刚刚向 npm 发布了`react-unite`。

[https://github.com/mizchi/react-unite](https://github.com/mizchi/react-unite)

[![](img/01a0af0df567466faf0ad04be49e0d46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5aqnxBE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.gyazo.com/a4781fc08dd2dd96b0db5aea61563d57.gif)

[演示](https://admiring-curie-8355d7.netlify.com)

灵感来自 unity3d 布局系统。

## 举例

```
yarn add react@16.7.0-alpha.0 react-dom@16.7.0-alpha.0 react-unite 
```

TypeScript 示例。

```
import React from "react";
import ReactDOM from "react-dom";
import { LayoutData, Windowed, EditableLayout } from "react-unite";

const initialLayoutData: LayoutData = {
  grid: {
    columns: ["1fr", "1fr"],
    rows: ["40px", "1fr", "1fr"],
    areas: [
      ["header", "header"],
      ["preview", "inspector"],
      ["assets", "inspector"]
    ]
  },
  windowMap: {
    "#scene": { displayName: "Scene", id: "#scene" },
    "#project": { displayName: "Project", id: "#project" },
    "#hierachy": { displayName: "Hierachy", id: "#hierachy" },
    "#inspector": { displayName: "Inspector", id: "#inspector" },
    "#services": { displayName: "Services", id: "#services" }
  },
  containers: [
    {
      id: "preview",
      displayName: "Preview",
      selectedId: "#scene",
      windowIds: ["#scene"]
    },
    {
      id: "assets",
      displayName: "Preview",
      selectedId: "#project",
      windowIds: ["#project", "#hierachy"]
    },
    {
      id: "inspector",
      displayName: "Inspector",
      selectedId: "#inspector",
      windowIds: ["#inspector", "#services"]
    }
  ]
};

// To fill window, Set css `html, body { margin: 0;}`
const MyLayout = () => {
  return (
    <Windowed>
      {(width, height) => (
        <EditableLayout
          width={width}
          height={height}
          layout={initialLayoutData}
          renderTab={data => {
            return <span>{data.displayName}</span>;
          }}
          renderWindow={win => {
            return (
              <div>
                {win.id}: {win.displayName}
              </div>
            );
          }}
        />
      )}
    </Windowed>
  );
};

const root = document.querySelector(".root");
ReactDOM.render(<MyLayout />, root); 
```

## 样本项目

[https://github.com/mizchi-sandbox/react-unite-example](https://github.com/mizchi-sandbox/react-unite-example)