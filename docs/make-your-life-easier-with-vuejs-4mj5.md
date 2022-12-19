# 使用 Vue.js 让您的生活更轻松

> 原文：<https://dev.to/tsanak/make-your-life-easier-with-vuejs-4mj5>

## 语境

这个迷你项目的目的是展示现代前端框架的价值。在这个迷你项目中，我们将两次构建同一个应用程序；一次用 Jquery，一次用 Vue。
这是一个非常基础的应用(无路由，无商店)。这也是我写的第一个帖子，所以请原谅任何打字错误和语法错误🙂。

## 应用要求

1.  所有待办事项的列表
2.  显示还剩多少待办事项的计数器
3.  插入新待办事项的方法
4.  一种去除 todos 的方法
5.  没有待办事项时的消息

这两种方法都有一些预先插入的待办事项。

## 我们开始吧

### Jquery 实现

我以下面的 html 代码结束:

```
 <div class="center-me">
        <div id="container">
            <h2 class="text-center">Best todo app</h2>
            <h3 class="text-center"><span id="total-todos"></span> things to do</h3>
            <div class="text-center">
                <input type="text" id="newTodo" placeholder="Go to the gym">
                <button id="addTodo">Add todo</button>
            </div>
            <div id="todos">
                <p class="text-center" id="no-todos">You don't have anything to do :(</p>
                <ul id="todo-list"></ul>
            </div>
        </div>
    </div> 
```

以及下面的 javascript 代码:

```
//Array to hold the todo items
    var todos = [
      { id:1, text: 'Learn JavaScript' },
      { id:2, text: 'Learn Vue' },
      { id:3, text: 'Build something awesome' },
      { id:4, text: 'Go to London' },
      { id:5, text: 'Kick ass' },
      { id:6, text: 'Much Profit' },
      { id:7, text: 'Wow' },
      { id:8, text: 'Manemizjef' },
      { id:9, text: 'etc' },
    ];
//Add todo items in the #todo-list
    function addTodosInDOM() {
        for (var i = 0; i < todos.length; i++) {
            var todo = todos[i];
            $('#todo-list').append("<li todo-id='" + todo.id + "'><span>" + todo.text + "</span> <span class='delete-todo'>X</span></li>");
        }
        if(todos.length == 0) {
            $('#no-todos').show();
        }
    }
//Remove a todo when clicking on #delete-todo
    function removeTodo(id) {
        for (var i = 0; i < todos.length; i++) {
            var todo = todos[i];
            if(todo.id == id) {
                todos.splice(i, 1);
                break;
            }
        }
    }
//Add todo item in the todos array and update the dom
    function addTodo() {
        var newId = todos.length;
            var todo = {
                id: newId,
                text: "",
            }
        //Get new todo text
        var todoText = $('#newTodo').val();
        if(todoText == "") return;
        todo.text = todoText;
        todos.push(todo);
        //Update the dom
        $('#no-todos').hide();    
        $('#todo-list').append("<li todo-id='" + todo.id + "'><span>" + todo.text + "</span> <span class='delete-todo'>X</span></li>");
        $('#newTodo').val("");
        $('#total-todos').text(todos.length);
    }

//When the DOM is ready for JavaScript code to execute
    $(document).ready(function(){
        addTodosInDOM();
        $('#total-todos').text(todos.length);

        $('body').on('click', '.delete-todo', function(e){
            var target = e.target;
            var li = $(target).parent();
            var id = $(li).attr('todo-id');
            //Remove element from dom
            $(li).remove();
            //Remove todo from the local data
            removeTodo(id);
            if(todos.length == 0) {
                $('#no-todos').show();
            }
            //Update todo counter
            $('#total-todos').text(todos.length);
        });
        //When clicking 'enter' inside the input addTodo
        $( "#newTodo" ).keyup(function(event) {
            if(event.which == 13) addTodo();
        });

        $('#addTodo').click(function(){
            addTodo();
        });
    }); 
```

如您所见，每当 todo 项目发生变化时，我们都需要手动更新 DOM。

### Vue 实施

我稍微修改了一下 html，最后得到了下面的代码:

```
 <div class="center-me" id="app">
        <div id="container">
            <h2 class="text-center">Best todo app</h2>
            <h3 class="text-center">{{ totalTodos }} things to do</h3>
            <div class="text-center">
                <input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo">
                <button @click="addTodo">Add todo</button>
            </div>
            <div id="todos">
                <p class="text-center" v-if="noTodos">You don't have anything to do :(</p>
                <ul id="todo-list" v-else>
                    <li v-for="(todo,index) in todos">
                        <span>{{ todo.text }}</span> <span class="delete-todo" @click="deleteTodo(index)">X</span>
                    </li>
                </ul>
            </div>
        </div>
    </div> 
```

以及下面的 javascript 代码:

```
//Initialize the vue instance
var app = new Vue({
        el: '#app',
        data: {
            //Preinserted todo items
            todos: [
                { text: 'Learn JavaScript' },
                { text: 'Learn Vue' },
                { text: 'Build something awesome' },
                { text: 'Go to London' },
                { text: 'Kick ass' },
                { text: 'Much Profit' },
                { text: 'Wow' },
                { text: 'Manemizjef' },
                { text: 'etc' },
            ],
            newTodo: ''
        },
/*
Computed properties provides us with a way to show the data dependecies
and bind our data to some properties.
E.g: the value of totalTodos is depended on the length of the todos array;
     so when an item is added/deleted from the todos array(the length of the array changes)
     Vue updates the value of the computed property totalTodos.
     Hence we do not need to worry about manually updating the DOM whenever 
     todos array changes
Read more about computed properties: https://vuejs.org/v2/guide/computed.html
*/
        computed: {
            noTodos() {
                return this.todos.length == 0;
            },
            totalTodos() {
                return this.todos.length;
            }
        },
        methods: {
            deleteTodo(index) {
                this.todos.splice(index, 1);
            },
            addTodo() {
                if(this.newTodo == "") return;
                this.todos.push({
                    text: this.newTodo
                });
                this.newTodo = '';
            }
        }
    }); 
```

### 小解释一下`{{}}`，v-if，v-else，v-for 等奇怪的符号。

#### 

Vue 使用 mustache ( `{{ }}`)模板语法以声明方式将数据呈现给 DOM。

```
<h3 class="text-center">{{ totalTodos }} things to do</h3>

<!-- 
    Will output 
-->
<h3 class="text-center">9 things to do</h3>
<!-- 
    if we add one more item to the todos array, it will output
-->
<h3 class="text-center">10 things to do</h3> 
```

#### v-if & v-else

Vue 使用这些指令来管理元素的条件呈现。例如

```
<!-- will only appear when noTodos === true -->
   <p class="text-center" v-if="noTodos">You don't have anything to do :(</p>
<!-- else the todo list will be rendered -->
   <ul id="todo-list" v-else>
       <li v-for="(todo,index) in todos">
           <span>{{ todo.text }}</span> <span class="delete-todo" @click="deleteTodo(index)">X</span>
       </li>
   </ul> 
```

#### v-for

v-for 指令可用于显示使用数组数据的项目列表

```
<!-- Loops through the todos array and outputs a li element foreach todo item -->
<li v-for="(todo,index) in todos">
<span>{{ todo.text }}</span> <span class="delete-todo" 
@click="deleteTodo(index)">X</span>
</li> 
```

#### v 型车

在 input 和 newTodo 之间创建双向数据绑定。每当其中一个发生变化，另一个就会更新。
。trim 是一个修饰符，它自动修剪用户输入。

```
<input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo"> 
```

#### @/v-开

@是 v-on 的简写。它的用途是将方法/函数绑定到一个事件

```
<!-- Here we bind the click event to the deleteTodo method -->
<span class="delete-todo" 
@click="deleteTodo(index)">X</span>
<!-- 
    Here we bind the keyup event to the addTodo method 
    .enter is a modifier on the keyup event, it means that we bind the addTodo,
    only when the user releases the 'enter' key
-->
<input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo"> 
```

## 参考

*   官方文件:[https://vuejs.org/v2/guide/](https://vuejs.org/v2/guide/)

我个人认为官方文档写得很好，很有帮助。

你可以在 gihub 上找到代码:[https://github.com/tsanak/todoApp](https://github.com/tsanak/todoApp)