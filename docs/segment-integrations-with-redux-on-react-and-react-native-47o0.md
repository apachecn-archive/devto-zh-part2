# react 和 react-native 上与 redux 的分段集成

> 原文：<https://dev.to/wonism/segment-integrations-with-redux-on-react-and-react-native-47o0>

有人使用细分市场进行数据分析吗？
细分市场是收集客户数据的基础。
它支持如此多的分析工具，如谷歌分析，Mixpanel，脸书像素等..

而 [redux-segment-node](https://github.com/wonism/redux-segment-node) 是一个 redux 中间件，帮助开发者轻松使用 segment。

现在，让我们看看如何使用它。

## 安装

```
$ npm i -S redux-segment-node 
```

Enter fullscreen mode Exit fullscreen mode

## 演示

```
# replace the `key` with your `Segment API key` in `config.json`
$ npm run dev
# and visit localhost:7777 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
// import { applyMiddleware, createStore } from 'redux';
import createSegmentTracker from 'redux-segment-node';

const segmentMiddleware = createSegmentTracker({
  key: 'API_KEY',
  flushAfter: 1000,
});

const middleware = applyMiddleware(segmentMiddleware);

// const store = createStore(/* ... */);

// identify (recommended: pass `userId` in `eventPayload`)
store.dispatch({
  type: 'SIGN_IN',
  analytics: {
    eventType: 'identify',
    eventPayload: {
      userId: 'UUID',
    },
  },
});

// track (required: pass `event` in `eventPayload`)
// If you pass the `signout` or `logout` as an `event` in `track` type,
// The `userId` will be removed.
store.dispatch({
  type: 'CLICK_CTA_BUTTON',
  analytics: {
    eventType: 'track',
    eventPayload: {
      event: 'Click CTA Button',
    },
  },
});

// page (recommended: pass `name` in `eventPayload`)
// screen (similar with `page`)
store.dispatch({
  type: 'VIEW_PAGE',
  analytics: {
    eventType: 'page', // or 'screen'
    eventPayload: {
      name: 'LANDING_PAGE',
    },
  },
});

// group (required: pass `groupId` in `eventPayload`)
store.dispatch({
  type: 'GROUP',
  analytics: {
    eventType: 'group',
    eventPayload: {
      groupId: 'UUID',
    },
  },
});

// alias (required: pass `userId` in `eventPayload`)
store.dispatch({
  type: 'ALIAS_USER',
  analytics: {
    eventType: 'alias',
    eventPayload: {
      userId: 'NEW_UUID',
    },
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 配置

> createSegmentTracker 参数

| 财产 | 类型 | 注意 |
| --- | --- | --- |
| 键 | 字符串(必需) |  |
| 平台 | 字符串(可选) | [ `android`、`ios`或`undefined`中的一种 |
| 宿主 | 字符串(可选) | 将向其发送报告的主机。对调试有用。 |
| 冲向 | 号码(可选) | 刷新前要排队的消息数。 |
| 冲洗后 | 号码(可选) | 自动刷新队列之前等待的毫秒数。 |

## 事件类型(分段 API 规范)

*   [`identify`](https://segment.com/docs/spec/identify/) :可以把用户和他们的行为联系起来，记录他们的特征。
*   [`track`](https://segment.com/docs/spec/track/) :可以跟踪用户执行的任何动作。
*   [`page`](https://segment.com/docs/spec/page/) :可以记录用户停留的页面。
*   [`screen`](https://segment.com/docs/spec/screen/) :可以记录用户停留的屏幕。(对于移动应用程序)
*   [`group`](https://segment.com/docs/spec/group/) :可以将单个用户关联到一个群组。
*   [`alias`](https://segment.com/docs/spec/alias/) :可以合并两个用户身份。

## 段的文档

[https://segment.com/libraries/node](https://segment.com/libraries/node)