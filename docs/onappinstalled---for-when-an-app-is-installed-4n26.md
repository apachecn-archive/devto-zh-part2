# onappinstalled -安装应用程序时使用。

> 原文：<https://dev.to/paul_kinlan/onappinstalled---for-when-an-app-is-installed-4n26>

Chrome 最近(至少在 [2017](https://crbug.com/621393) 实现了`window.onappinstalled` [事件](https://developer.mozilla.org/en-US/docs/Web/API/Window/onappinstalled)。当用户通过添加到主屏幕 API(通过`onbeforeinstallprompt`事件传递的事件的 prompt()函数)*或*(现在更重要的是通过添加到主屏幕的手动方法)安装渐进式 web 应用程序时，它会被触发。

这是一个非常有用的补充，因为它允许你在提示上看到参与度，而不是使用系统横幅或菜单按钮来安装渐进式 web 应用程序的人。

我已经将它添加到 [Airhorner](https://airhorner.com) 中，所以如果连接了 DevTools，你可以看到它的运行。下面的代码管理`onbeforeinstallprompt`和`onappinstalled`——在这种情况下，我使用 onbeforeinstallprompt 将安装提示推迟到一个自定义按钮，使用`onappinstalled`清理 UI 并做一些基本的分析。

```
const Installer = function(root) {
  let promptEvent;

  const install = function(e) {
    if(promptEvent) {
      promptEvent.prompt();
      promptEvent.userChoice
        .then(function(choiceResult) {
          // The user actioned the prompt (good or bad).
          // good is handled in 
          promptEvent = null;
          ga('send', 'event', 'install', choiceResult);
          root.classList.remove('available');
        })
        .catch(function(installError) {
          // Boo. update the UI.
          promptEvent = null;
          ga('send', 'event', 'install', 'errored');
          root.classList.remove('available');
        });
    }
  };

  const installed = function(e) {
    promptEvent = null;
    // This fires after onbeforinstallprompt OR after manual add to homescreen.
    ga('send', 'event', 'install', 'installed');
    root.classList.remove('available');
  };

  const beforeinstallprompt = function(e) {
    promptEvent = e;
    promptEvent.preventDefault();
    ga('send', 'event', 'install', 'available');
    root.classList.add('available');
    return false;
  };

  window.addEventListener('beforeinstallprompt', beforeinstallprompt);
  window.addEventListener('appinstalled', installed);

  root.addEventListener('click', install.bind(this));
  root.addEventListener('touchend', install.bind(this));
}; 
```

Enter fullscreen mode Exit fullscreen mode