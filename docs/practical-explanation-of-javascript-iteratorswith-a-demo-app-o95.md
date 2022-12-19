# Javascript 迭代器的实用解释(带演示应用)

> 原文：<https://dev.to/bjhaid_93/practical-explanation-of-javascript-iteratorswith-a-demo-app-o95>

### 迭代器是什么？

用外行人的话来说，迭代器用于遍历一组对象。迭代器是 ES6(EcmaScript6)的一个特性，它是一个可以暂停的高级循环，迭代器提供了返回序列中下一个项目的 next()方法，value 属性可用于访问当前项目的值，当 next()方法返回一个 done 属性设置为 true 的对象时，迭代器被视为终止。
下面是一个例子

```
 function Iterator(names){
        //set the index to 0
        let nextIndex = 0;
        return {
            next() {
            return nextIndex < names.length 
                    ?
                {value:names[nextIndex++], done:false}
                    : 
                {done:true}
            }
        }
    }
    //Create an array
    let names = ['wale', 'ali', 'john', 'bubu'];
    //pass the array into the Iterator function
    let name = Iterator(names);
    console.log(name.next().value);//wale
    console.log(name.next().value);//ali
    console.log(name.next().value);//john
    console.log(name.next().value);//bubu
    console.log(name.next().value);//undefined 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中，前四个调用给出了数组中前四项的值，最后一个返回 undefined，因为数组中不再有要迭代的项，所以迭代终止。
下面是控制台输出
[![output](../Images/0ba7d9b6ff6d275887a63ead62b05b92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AGm5XM-S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ksj5h825b6lf36zyukbz.png)

我将通过构建一个演示应用程序来解释迭代器，这样它会给我们一个迭代器在现实应用中用途的概述，在这个应用程序中，我将从[https://api.github.com/users](https://api.github.com/users)获取数据，这将使我们能够查看前 46 个用户的配置文件。
这里是 HTML 结构

```
 <!doctype html>
<html lang="en">
  <head>
    Profile Scroller
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css" integrity="sha384-PsH8R72JQ3SOdhVi3uxftmaW6Vc51MKb0q5P2rRUpPvrszuE4W1povHYgTpBfshb" crossorigin="anonymous">
  </head>
  <body>
    <div class="container">
      <div class="row">
        <div class="col-md-6 mx-auto text-center">
          <h1 class="mb-3">Profile Scroller</h1>
          <div id="imageDisplay"></div>
          <br>
          <div id="profileDisplay"></div>
          <br>
          <button id="next" class="btn btn-dark btn-block">Next</button>
        </div>
      </div>
    </div>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js" integrity="sha384-vFJXuSJphROIrBnz7yo7oB41mKfc8JzQZiCq4NCceLEaO4IHwicKwpJf9c9IpFgh" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/js/bootstrap.min.js" integrity="sha384-alpBpkh1PFOepccYVYDB4do5UnbKysX5WZXm3XxPqe5iKTfUKjNkCk9SaVuEZflJ" crossorigin="anonymous"></script>
    <script src="app.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

下面是 Javascript 代码

```
 //create the function
  function Iterator(profile) {
    //set the index to 0
    let nextIndex = 0;

    return {
      next() {
        return nextIndex < profile.length 
              ?
          {value: profile[nextIndex++], done: false}
              :
          {done: true}
      }
    }
  }
  //html classes and ids stored in object
  let selector = {
    next : 'next',
    profile : 'profileDisplay',
    image: 'imageDisplay'
  }

//Using AJAX to fetch data
  var xhr = new XMLHttpRequest();

  xhr.open('GET', 'https://api.github.com/users', true);

  xhr.onload = function() {
    if (this.status === 200) {
      let data = JSON.parse(this.responseText);
      //pass the data coming in from the API into the iterator function
      let profile = Iterator(data);
      //call the load function to load the first profile
      loadProfile();
      //create an event listener for the button
      document.getElementById(selector.next).addEventListener('click', loadProfile);

      function loadProfile() {
        //get the value of the current and next data
        const currentProfile = profile.next().value
          //check if the current value is not undefined
          if(currentProfile !== undefined){
            document.getElementById(selector.profile).innerHTML =
            `<ul class="list-group">
                    <li class="list-group-item">Login: ${currentProfile.login}</li>
                    <li class="list-group-item">Id: ${currentProfile.id}</li>
                    <li class="list-group-item">Type: ${currentProfile.type}</li>
              </ul>
            `
            document.getElementById(selector.image).innerHTML = `<img src="${currentProfile.avatar_url}" class="w-25">`;
          }
        else {
          //reload the page after the last iteration
          window.location.reload();
        }
      }
    }
  }
  xhr.send() 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，迭代器函数被创建，从 [API](https://api.github.com/users) 获取的数据被传递到迭代器函数，从 [API](https://api.github.com/users) 我们可以访问浏览器中显示的头像、登录、id 和类型，在最后一次迭代后，页面重新加载，函数 loadProfile 被再次调用。

下面是浏览器中输出的
[![output](../Images/2d51d22d19943de1bc3139fffd1d850a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rve7suFD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0u02iqw7tm2jwwl2flkb.gif)

## 注

我们也有[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)，但是[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)与迭代器略有不同，获取的数据可以被创建，数据也可以来自任何外部 API。
这里有一个[链接](https://abejide001.github.io/javascript-iterators-es6-/)到 app
感谢您的阅读，快乐编码！