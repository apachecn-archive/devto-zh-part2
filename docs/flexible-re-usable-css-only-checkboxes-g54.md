# 完全可配置、灵活、可重用的 CSS 专用复选框切换

> 原文：<https://dev.to/vviikk/flexible-re-usable-css-only-checkboxes-g54>

可配置使用 CSS 自定义属性！。我在网上发现了很多很好的切换例子，但是它们都有某种形式的硬编码值。查看代码。使用行高并遵循给定的自然大小。随便偷:)

```
 <input class="switch" id="switch-55" type="checkbox"/>
  <label for="switch-55" style="
    --switch-width: 3rem; /* optional properties */
    --switch-knob-size: .8rem;
  ">Toggle</label> 
```

```
/* the following properties are configurable by simply overriding them: */
:{  
  --switch-background: silver;
  --switch-background-active: mediumseagreen;
  --switch-width: 3ch;
  --switch-knob-size: calc(var(--switch-width) / 3);
  --switch-knob-offset: calc(50% - (var(--switch-knob-size) / 2));
  --switch-knob-color: white;
  --switch-transition-duration: 0.3s;
} 
```

[https://codepen.io/piggyslasher/embed/rNaPKve?height=600&default-tab=result&embed-version=2](https://codepen.io/piggyslasher/embed/rNaPKve?height=600&default-tab=result&embed-version=2)