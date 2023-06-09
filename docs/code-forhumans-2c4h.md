# 人类的代码

> 原文：<https://dev.to/spidergears/code-forhumans-2c4h>

###### 空格也是代码...

太棒了，你的代码在生产中可以工作，而且足够快。
祝贺你，但是...

*   可读性够吗？
*   对于一个新的开发者来说，学会并理解它有多容易？
*   通读代码是否为构建的系统提供了一个良好的心智模型？

这不是一个详尽的列表，但是如果以上任何一个问题的答案是否定的，那么你应该考虑重新审视并做一些修饰性的改变。

以下是我在写/读代码时遵循的一些要点

#### 空格

```
- white-spaces are code too

*   在方法定义之间留一个空行

*   当方法体内的上下文发生变化时，出现一个空行 
```

#### 缩进

```
- indent code well
*   遵循一致的缩进长度，2 个空格或 4 个空格
*   保持制表符或空格的使用一致，选择一个并在整个团队中坚持使用
```

#### 变量和方法命名

```
- use concise and meaningful names
*   变量名应该反映它们引用的值
*   方法名应该反映所包含的流程/业务逻辑
```

#### 文档

```
- unless it is a framework, module, class, method, variable names should be self documenting without need of additional documentation
*   注释不应该成为证明混乱代码段的工具
```

#### 设计代码

```
- every code is a piece of art uniquely conceived by it's developer
*   在开始编码之前，想象对象之间的交互
*   在开始编码之前，设想所需的消息交换
```

#### 权利抽象

```
- abstraction does not mean hiding the complexity detail
*   了解要模仿的行为特征
*   有 api 规范可以参考
*   事先知道要公开的方法、变量和常数
*   不要根据推测做决定
*   需求是基石
```

#### 语言成语

```
- make use of language idioms
*   Ruby 示例:[1，2，3，4]。总和 v/s [1，2，3，4]。每个{|ele| sum = sum + ele}
```

## 人类的代号

<small>封面图片致谢:[Unsplash](https://unsplash.com/photos/cvBBO4PzWPg)T3】</small>