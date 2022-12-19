# 构建 react 应用程序时路线不起作用

> 原文：<https://dev.to/gihanrangana/routes-not-working-when-react-app-is-build-2p75>

我创建了 react app react router 4 .但是我使用 npm 运行构建路由 URL 来构建我的项目，却无法运行并显示 404 页面.我该如何解决这个问题？我不知道。这是我的代码

```
import React from "react";
import ReactDOM from "react-dom";
import registerServiceWorker from "./registerServiceWorker";
import { BrowserRouter as Router,Switch,Route } from "react-router-dom";

import { createMuiTheme, MuiThemeProvider } from "@material-ui/core/styles";

import "react-datepicker/dist/react-datepicker-cssmodules.css";
import "./styles/main.scss";

import App from "../App";
import AdminHome from "../pages/admin/home";
import Login from "../pages/login";
import PrivateRoute from "./privateRoute";

const theme = createMuiTheme({
    palette: {
        type: "dark"
    }
});

reactdom.render(未发现}/>

), document.getElementById("root"));

registerServiceWorker();

```

有人能帮我解决这个问题吗？