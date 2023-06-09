# 用 Mocha & Chai 测试节点/快速应用程序

> 原文：<https://dev.to/kiddeveloper/testing-a-nodeexpress-application-with-mocha--chai-4lho>

[![alt text](img/b4b30068ff730f2bcfe4d8e558ec89a2.png "Testing A Node/Express Application With Mocha & Chai Image")](https://res.cloudinary.com/practicaldev/image/fetch/s--OoYw0PNT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/us2i7gtlfzhsj6wlcakg.jpeg) 
你已经花费了大量的时间和精力编写了一个相当中等的应用程序，比如说一个大约 1000 行的代码库，并且你已经手动测试了这个应用程序以确保一切运行良好。你把你的代码推给 Github，有人决定为你的工作贡献他们自己的配额。他们推送他们的代码，创建一个拉取请求，然后你把它合并进去，现在你的应用程序不再运行，一切都坏了，这都是因为你合并进去的代码。换句话说，为了避免这种类型问题以及软件开发带来的更多问题，您需要将测试集成到您的工作流程中。

测试要求你编写测试，覆盖软件可能接收的各种输入和相应的输出。这样你就可以确保应用程序完全按照你的预期运行，这可以防止很多错误。在新代码添加到您的代码库之前编写一个测试总是很重要的，这样您可以确保新代码不会给您的代码库带来任何错误，并且它还可以帮助您预先知道新代码是否会破坏您的代码库的任何部分。

在本文中，我们将编写一个简单的 Node/Express API 应用程序，同时使用 mocha & chai JavaScript 测试包进行测试。

根据其网站上的描述，Mocha 是一个测试框架，它使异步测试变得简单而有趣。它将提供测试环境，使我们能够轻松地运行 chai。

Chai 是一个断言库，可以与任何测试框架配对。这是我们实际用来编写测试的库。

#### 设置我们的示例应用程序

我们将构建一个从非持久学生记录数据中读取信息的应用程序。要继续，我们需要创建以下文件和文件夹:

```
---controllers/
------studentController.js
---dummy/
------students.js
---routes/
------index.js
---tests/
------test.js
---.babelrc
---server.js
---package.json 
```

Enter fullscreen mode Exit fullscreen mode

为了设置我们的虚拟数据，我们需要将数据包含在`dummy/students.js`文件中:

```
const students = [
   {
     id: 1,
     name: 'Sean Grey',
     age: 24,
   },
   {
     id: 2,
     name: 'John Doe',
     age: 26,
   },
   {
     id: 3,
     name: 'Janet Dane',
     age: 19,
   },
];
export default students; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码块分配了一个对象数组，每个对象保存一个学生的详细信息。

现在让我们设置 package.json，这样我们就可以安装构建和测试应用程序所需的所有包。

```
{
   "name": "student-record",
   "version": "1.0.0",
   "description": "An API to manage student record",
   "main": "server.js",
   "author": "Samuel Afolaranmi",
   "license": "MIT",
   "scripts": {
        "test": "mocha --require babel-register tests/*.js --exit",
        "dev": "nodemon --exec babel-node --presets babel-preset-env ./server.js"
   }
   "dependencies": {
        "body-parser": "^1.18.3",
        "express": "^4.16.3"
   },
   "devDependencies": {
        "babel-cli": "^6.26.0",
        "babel-preset-env": "^1.7.0",
        "chai": "^4.1.2",
        "chai-http": "^4.0.0",
        "mocha": "^5.1.1",
        "nodemon": "^1.17.4"
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`package.json`文件中，我们包含了我们的`mocha`和`chai`，我们将用它们来编写我们的测试。我们还需要包含`chai-http`，它是一个插件，允许我们运行带有 chai 断言的 HTTP 集成。我们现在可以运行`npm install`来安装这些包，并准备好完成应用程序的设置。

下一步是创建我们的`routes`和`server.js`文件，但是首先，我们应该创建我们的`controller`文件，因为我们需要将它导入到我们的`routes`文件中。在`controllers/studentController.js`文件中，我们应该包括:

```
import students from '../dummy/students.js';
class StudentController {
    // Get all students
    static getAllStudents(req, res) {
          return res.status(200).json({
                students,
                message: "All the students",
          });
    }
    // Get a single student
    static getSingleStudent(req, res) {
           const findStudent = students.find(student => student.id === parseInt(req.params.id, 10));
           if (findStudent) {
               return res.status(200).json({
                     student: findStudent,
                     message: "A single student record",
               });
           }
           return res.status(404).json({
                 message: "Student record not found",
           });
    }
}
export default StudentController; 
```

Enter fullscreen mode Exit fullscreen mode

在`controllers/studentController.js`文件中，我们导入了我们的虚拟数据，创建了一个类来保存我们的控制器方法，并创建了两个静态方法，每一个都是我们想要用控制器类实现的。第一种方法`getAllStudents`，顾名思义，获取虚拟数据中的所有学生记录，并返回一个 200 HTTP 状态代码，而第二种方法`getSingleStudent`，获取单个学生的记录，并返回一个 200 HTTP 状态。如果没有找到记录，则返回 404 HTTP 状态代码。

既然我们已经设置了控制器，现在我们可以继续工作路线和`server.js`。在我们的`routes/index.js`文件中，我们应该添加以下代码:

```
import { Router } from 'express';
import StudentController from '../controllers/studentController.js';
const routes = Router();
routes.get('/', StudentController.getAllStudents);
routes.get('/:id', StudentController.getSingleStudent);
export default routes; 
```

Enter fullscreen mode Exit fullscreen mode

我们从`express`中导入`Router (express router)`并将其分配给 routes，我们还从`controllers/studentController`中导入了`StudentController`类。js 文件。我们使用导入的路由器创建了两条路由，分别绑定到它们对应的控制器方法。

现在我们应该创建我们的`server.js`文件，这样我们就可以测试我们编写的代码是否有效。

```
import express from 'express';
import bodyParser from 'body-parser';
import routes from './routes/index';
// Instantiate express
const app = express();
// Set our port
const port = process.env.PORT || 8000;
// Configure app to user bodyParser
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
// Register our routes in app
app.use('/', routes);
// Start our server
app.listen(port, () => {
    console.log(`Server started on port ${port}`);
});
// Export our app for testing purposes
export default app; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在编写 ES6 代码，所以我们需要 babel 来编译我们的代码，为此，我们需要将下面的代码添加到我们的`.babelrc`文件中:

```
{
   "presets": ["env"]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置好了我们的应用程序，我们可以继续运行`npm run dev`来运行我们的应用程序，并使用 Postman 测试我们的端点。

#### 为我们的应用程序编写测试

我们的应用程序运行良好，但是我们需要为它编写测试。以确保我们不打破它，同时也涵盖所有的边缘情况。在我们的`tests/test.js`文件中，我们将编写我们的测试。

```
// Import the dependencies for testing
import chai from 'chai';
import chaiHttp from 'chai-http';
import app from '../server';
// Configure chai
chai.use(chaiHttp);
chai.should();
describe("Students", () => {
    describe("GET /", () => {
        // Test to get all students record
        it("should get all students record", (done) => {
             chai.request(app)
                 .get('/')
                 .end((err, res) => {
                     res.should.have.status(200);
                     res.body.should.be.a('object');
                     done();
                  });
         });
        // Test to get single student record
        it("should get a single student record", (done) => {
             const id = 1;
             chai.request(app)
                 .get(`/${id}`)
                 .end((err, res) => {
                     res.should.have.status(200);
                     res.body.should.be.a('object');
                     done();
                  });
         });

        // Test to get single student record
        it("should not get a single student record", (done) => {
             const id = 5;
             chai.request(app)
                 .get(`/${id}`)
                 .end((err, res) => {
                     res.should.have.status(404);
                     done();
                  });
         });
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在文件的开始，我们导入了测试运行所需的所有包，然后我们配置 chai 使用`chai-http`插件。我们还通过运行`chai.should()`将 chai 配置为使用 should 接口。每个`describe`块用于将我们的测试分组在一起，以便于访问和更好的组织。

第一个`it`块是在第一个端点上运行的测试，从数据中获取所有学生记录，它断言响应的状态应该为 200，并且应该返回一个对象。第二个`it`块是一个测试，它在第二个端点上运行以获得一个学生请求。假设学生存在，它断言响应的状态应该为 200，并且应该返回一个对象。最后，第三个`it`块是一个测试，它也在第二个端点上运行以获得一个请求。假设学生不存在，它断言响应的状态应该是 404。

剩下的就是我们运行`npm run test`了，我们会看到我们的测试在我们眼前通过。很美，不是吗？