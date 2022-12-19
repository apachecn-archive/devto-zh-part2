# 如何过滤 Angular 中的请求？

> 原文：<https://dev.to/zeyduzgun/how-to-filter-requests-in-angular-4l7i>

大家好，
我在我的项目中使用 Angular 已经有一段时间了，我想知道有什么方法可以过滤我的请求响应或请求本身？例如，我有一个事件模型，这个模型有许多会话模型。对于这种关系，我使用弱关系，会话知道事件，但反之则不然。出于这个原因，我必须首先获取事件，然后用它的 id 获取所有会话，尽管我只需要其中的一个。那么，当我请求事件的会话等时，我可以限制我的响应吗？
我知道这听起来很复杂，所以请随时询问有关情况的更多细节。

谢谢大家！

下面是一个代码示例:

```
getExperiences() {
    this.experienceService.listApprovedExperiences().subscribe(res => {
      this.experiences = res;
      for (const experience of res) {
        this.getSessionList(experience.id);
    });
  }

  getSessionList(id: string) {
    this.experienceService.getSessionListByExperienceId(id).subscribe(res => {
      if (res.length !== 0) {
        this.session.set(id, res[0]);

      } else {

      }
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode