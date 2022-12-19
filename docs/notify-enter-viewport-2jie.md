# Notify Enter Viewport

> 原文：<https://dev.to/ipasqualito/notify-enter-viewport-2jie>

有时我们测试一个页面元素或组件，当页面加载后，它位于视窗之外。这有一个简单的结果，就是你不应该把这个页面负载算作你测试的访问者。只有当您正在测试的元素滚动到视图中时，访问者才能被添加到您的测试中。为此，我们需要一些代码来跟踪元素在页面上相对于页面尺寸的位置。

这个仍然依赖于 jQuery，我将很快上传一个 JS 版本。

```
 var notifyEnterViewport = function(o) {
        var raf = window.requestAnimationFrame || window.webkitRequestAnimationFrame || window.mozRequestAnimationFrame || window.msRequestAnimationFrame || window.oRequestAnimationFrame,
            l = $(window).height(),
            r = $(window),
            t = r.scrollTop(),
            enter = function(e) {
                e.addClass("MM_inside_viewport"), e.hasClass("MM_outside_viewport") && e.removeClass("MM_outside_viewport")

            },
            exit = function(e) {
                e.addClass("MM_outside_viewport"), e.hasClass("MM_inside_viewport") && e.removeClass("MM_inside_viewport")
            },
            loop = function() {
                var e = r.scrollTop();
                t !== e && (t = e, o.length && $.each(o, function(e) {
                    var o = $(this),
                        raf = o.height(),
                        t = o.offset().top,
                        loop = t - r.scrollTop(),
                        n = t - r.scrollTop() + raf;
                    0 <= loop && n <= l && !o.hasClass("MM_inside_viewport") && (console.log("test: element " + (e + 1) + " scrolled into view"), enter(o)), (n < 0 || l < loop) && !o.hasClass("MM_outside_viewport") && (console.log("test: element " + (e + 1) + " scrolled out of view"), exit(o))
                })), raf(loop)
            };
        raf && loop();
    }
    elementsToCheck = $("div#checkThisElement");
notifyEnterViewport(elementsToCheck); 
```

Enter fullscreen mode Exit fullscreen mode

*   更新！

```
 var notifyEnterViewport = function(el) {
    var w = window,
        raf = w.requestAnimationFrame,
        vpHeight = w.innerHeight,
        lastPgYO = w.pageYOffset,
        scroll = function() {
            var height = el.offsetHeight,
                elOffset = offSet(el),
                top = elOffset.top,
                pTop = top - w.pageYOffset,
                pBottom = pTop + height;
            if (pTop >= 0 && pBottom <= vpHeight && !el.classList.contains('mm_enter')) {
                enter(el);
            }
            if ((pBottom < 0 || pTop > vpHeight) && !el.classList.contains('mm_exit')) {
                exit(el);
            }
        },
        offSet = function(el) {
            var rect = el.getBoundingClientRect(),
                pgxo = w.pageXOffset,
                pgyo = w.pageYOffset;
            return {
                top: rect.top + pgyo,
                left: rect.left + pgxo
            };
        },
        enter = function(el) {
            //console.log("enter: ", el);
            el.classList.add('mm_enter');
            el.classList.remove('mm_exit');
        },
        exit = function(el) {
            //console.log("exit: ", el);
            el.classList.add('mm_exit');
            el.classList.remove('mm_enter');
        },
        loop = function() {
            var PgYO = w.pageYOffset;
            if (lastPgYO === PgYO) {
                raf(loop);
                return;
            } else {
                lastPgYO = PgYO;
                scroll();
                raf(loop);
            }
        };
    if (raf) loop();
};

// call:
notifyEnterViewport(document.querySelector('ul#sub_nav > li.active:not(.first)')); 
```

Enter fullscreen mode Exit fullscreen mode

通知进入视窗的帖子[首先出现在](https://www.recoveryarea.nl/ab-testing/notify-enter-viewport/)[回收区](https://www.recoveryarea.nl)。