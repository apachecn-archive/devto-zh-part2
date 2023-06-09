# 使用 TDD 构建 JavaScript 认证系统(第 2 部分)

> 原文：<https://dev.to/uf4no/building-a-javascript-auth-system-using-tdd-part-2-37lg>

这是关于使用 TDD 构建 JavaScript 认证系统的系列文章的第二部分。在第一部分的[中，我们创建了一个 Express 应用程序，它公开了两个端点，用于注册新用户(通过一些验证)和登录。由于我们没有在数据库中保存用户的详细信息，所以我们无法实现正确的登录验证，所以这就是我们在本文中要做的。我们走吧！](https://antonioufano.com/articles/building-a-javascript-jwt-auth-system-using-tdd-part-1-13 "first part of the article")

### 在 MongoDB 中存储用户详细信息

首先要做的是获得 Mongo 数据库的连接细节。您可以在本地安装它，也可以使用一个 [Mongo Atlas 实例](https://www.mongodb.com/cloud/atlas "Mongo Atlas")。使用这些选项中的任何一个，我们只需要主机、数据库、用户名和密码。在我的例子中，我在我的 PC 上安装了 MongDB，所以我的主机和数据库是“127.0.0.1:27017/authSys”(我用 Mongo CLI 创建了数据库)。为了将所有这些细节放在同一个地方，让我们创建一个 config 文件夹，其中包含一个 *local.js* 文件。在这个文件中，我们将导出一个带有数据库连接细节的对象。

```
/**
 * config/local.js
 * exports an object with configuration params
 */

module.exports = {
  APP_PORT: "1337",
  DB_HOST: "YOUR_MONGO_HOST/DATABASE",
  DB_USER: "MONGO_USER",
  DB_PASS: "MONGO_PASS",
  JWT_KEY: "thisIsMyJwtKeyUsedToEncodeTheTokens"
}
```

如您所见，我还包含了我们在本文第一部分配置的 JWT 键，它被硬编码在我们的 *app.js* 文件中。现在，在我们的 *app.js* 中，让我们移除硬编码的 JWT_KEY，并从配置文件中加载我们所有的环境变量:

```
/**
 * app.js
 * exports an Express app as a function
 */

..................

//load ENV Variables from config file
const config = require('./config/local');
process.env.APP_PORT = config.APP_PORT;
process.env.DB_HOST = config.DB_HOST;
process.env.DB_USER = config.DB_USER;
process.env.DB_PASS = config.DB_PASS
process.env.JWT_KEY = config.JWT_KEY;
...................
```

在更改任何其他内容之前，让我们运行我们的测试，以确保这一更改没有造成任何损害:)

[![Original tests pass ok](img/c0d132565969c7caba56291292691b4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aJCeMl1u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.antonioufano.com/image_uploads/Part2_test1OK.png)T3】

我们的应用程序将使用**mongose**模块与数据库进行交互，我们将使用 **bcrypt** 模块在保存用户密码之前对其进行加密。我们可以安装这两个通过 NPM 运行 *npm 安装猫鼬 bcrypt。*

接下来，我们必须在我们的 *app.js* 中导入 mongoose 模块，并将连接细节传递给 *connect()* 方法，该方法返回一个承诺。在我们的例子中，我们将只记录一条控制台消息，通知连接是成功还是失败。如果是这样，我们将停止我们的应用程序。

```
/**
 * app.js
 * exports an Express app as a function
 */

..................

//interact with MongoDB
const mongoose = require('mongoose');
//compose connection details
let dbConn = "mongodb://" + process.env.DB_USER + ":" + process.env.DB_PASS + "@" + process.env.DB_HOST;
//connect to the database
mongoose.connect(dbConn, {useNewUrlParser: true}).then( () => {
  console.log('Connected to the database');
}).catch( err => {
  console.log('Error connecting to the database: ' + err);
  process.exit();
})
...................
```

现在，如果我们用 **node app.js** 启动我们的应用程序(或者 npm start，如果我们已经将它添加到我们的 package.json 文件中)，我们将看到我们的应用程序连接到数据库:

[![app connects to the database](img/8559866ec99048113f40b2f841571f3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lePGvUiW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.antonioufano.com/image_uploads/app_connected.png)T3】

为了确保我们的用户详细信息存储在数据库中，让我们修改我们在文章的第一部分创建的“用户注册”测试，并期望接收用户详细信息，它将包含 id 和创建日期:

```
 it('/register should return 201 and confirmation for valid input', (done) => {
    //mock valid user input
    let user_input = {
      "name": "John Wick",
      "email": "john@wick.com",
      "password": "secret"
    }
    //send /POST request to /register
    chai.request(app).post('/register').send(user_input).then(res => {
      //validate
      expect(res).to.have.status(201);
      expect(res.body.message).to.be.equal('User registered');

      //new validations to confirm user is saved in database
      expect(res.body.user._id).to.exist;
      expect(res.body.user.createdAt).to.exist;

      //done after all assertions pass
      done();
    }).catch(err => {
      console.log(err);
    });
  })
```

现在我们已经添加了新的验证，我们的测试失败了，所以让我们修复它。为了在我们的数据库中存储用户详细信息，我们必须定义一个模式和一个模型，它将详细描述我们的用户将具有的不同属性。在我们的例子中，它将是名称、电子邮件和密码，因为这些是我们将从测试中发送的内容，此外，我们将保存一个 id 来帮助我们唯一地识别用户，以及创建和更新的日期。我们刚刚安装的 mongoose 模块包含了定义模式和模型所需的函数。使用以下代码在 *api* 文件夹中创建一个新文件 *user.js* :

```
/**
 * api/user.js
 * Defines the User Schema and exports a mongoose Model
 */

const mongoose = require('mongoose');
const userSchema = mongoose.Schema({
  _id: mongoose.Schema.Types.ObjectId,
  name: {type: String, required: true},
  email: {type: String, required: true, unique: true},
  password: {type: String, required: true}

}, 
{
  timestamps: true
});

module.exports = mongoose.model('User', userSchema, 'users');
```

请注意，我们必须定义字段的类型(字符串和 ObjectId ),我们还可以定义它是必需的还是唯一的。您可以在[mongose 模式文档](https://mongoosejs.com/docs/guide.html "Mongoose Schema documentation")中找到更多相关信息。

在第一部分中，我们包含了一个验证，以确保所有预期的字段都被接收到，如果没有问题，我们返回一个 201 代码和一条消息“用户创建”。现在，我们将在用户模型中保存用户详细信息，将其发送到我们的数据库，**只有正确保存，**我们才会发送响应。

我们必须导入 mongoose 模块和我们刚刚在 routes.js 中创建的模型。然后使用 *new User()* 构造函数，将我们在请求体中收到的字段分配给用户的属性。然后我们将使用 *save()* 方法将其存储在数据库中。这个方法返回一个承诺，所以如果它被解决，我们将发送我们的响应(包括我们刚刚创建的用户)，如果它被拒绝，我们将发送错误的详细信息。我们完整的注册路径如下所示:

```
 /**
 * /api/routes.js
 * exports an express router.
 */ 

..............................

//database
const mongoose = require('mongoose');
//import User
const User = require('./user');

router.post('/register', (req, res, next) => {
  let hasErrors = false ;
  let errors = [];

  if(!req.body.name){
  //validate name presence in the request
    errors.push({'name': 'Name not received'})
    hasErrors = true;
  }
  if(!req.body.email){
    //validate email presence in the request
    errors.push({'email': 'Email not received'})
    hasErrors = true;
  }
  if(!req.body.password){
    //validate password presence in the request
    errors.push({'password': 'Password not received'})
    hasErrors = true;
  }

  if(hasErrors){
    //if there is any missing field
    res.status(401).json({
      message: "Invalid input",
      errors: errors
    });

  }else{
  //if all fields are present
    //create the user with the model
    const new_user = new User({
      //assign request fields to the user attributes
      _id : mongoose.Types.ObjectId(),
      name: req.body.name,
      email: req.body.email,
      password: req.body.password
    });
    //save in the database
    new_user.save().then(saved_user => {
    //return 201, message and user details
      res.status(201).json({
        message: 'User registered',
        user: saved_user,
        errors: errors
      });
    }).catch(err => {
    //failed to save in database
      errors.push(new Error({
        db: err.message
      }))
      res.status(500).json(errors);
    })
  }

});
```

现在，我们添加到用户注册测试中的断言将会通过....一次。如果我们多次运行我们的测试，我们将尝试每次存储同一个用户，因为在我们的模型中，我们将电子邮件定义为唯一的，如果我们再次尝试存储，它将抛出一个错误。为了避免这种情况发生，我们可以在运行测试套件之前从表中删除所有用户。我们可以在我们的 *test.js* 的开头添加一个 *before()* 块，并使用我们的用户模型的 *deleteMany()* 函数:

```
 /**
 * test/test.js
 * All endpoint tests for the auth API
 */

...................

//import User model
const User = require('../api/user')

describe('App basic tests', () => {

  before( (done) => {
    //delete all users 
    User.find().deleteMany().then( res => {
      console.log('Users removed');
      done();
    }).catch(err => {
      console.log(err.message);
    });
  });

  ................

}
```

另一个选择是在测试后删除我们的用户。我们可以在()块的最后用*来做。无论如何，现在我们可以想运行多少次测试就运行多少次。*

### 加密密码

我们应该始终加密我们的用户的密码，以便在有人访问我们的数据库，他们将无法使用细节登录到我们的系统。我们可以很容易地使用 bcrypt 模块加密密码，我们可以使用 **npm install bcrypt** 安装该模块。为了确保我们正在加密密码，我们可以做的一个很好的测试是检查我们发送到后端的密码是否与我们收到的密码相同。让我们继续将这个断言添加到我们的“用户注册”测试中:

```
/**
 * test/test.js
 * All endpoint tests for the auth API
 */

....................

it('/register should return 201 and confirmation for valid input', (done) => {
    //mock valid user input
    let user_input = {
      "name": "John Wick",
      "email": "john@wick.com",
      "password": "secret"
    }
    //send /POST request to /register
    chai.request(app).post('/register').send(user_input).then(res => {
      //validate
      expect(res).to.have.status(201);
      expect(res.body.message).to.be.equal('User registered');
      console.log(res.body.user);
      //new validations to confirm user is saved in database
      expect(res.body.user._id).to.exist;
      expect(res.body.user.createdAt).to.exist;
      //validation to confirm password is encrypted
      expect(res.body.user.password).to.not.be.eql(user_input.password);

      //done after all assertions pass
      done();
    }).catch(err => {
      console.log(err);
    });
  })
```

如果我们现在运行我们的测试，它将失败，并显示消息“*assertion error:expected ' secret '不等于 deep ' secret '*”。让我们继续在我们的 *routes.js* 文件中修复这个问题。首先，我们需要导入 bcrypt 模块，然后在将用户的详细信息存储到数据库之前，我们需要使用 *hash()* 函数。正如在 [bcrypt 文档](https://www.npmjs.com/package/bcrypt "bcrypt documentation")中详细描述的，有几种不同的方法来散列我们的密码。我将使用第二个，它接收我们想要散列的密码和 salt 轮数(我将使用 10)。然后，它返回散列密码或回调函数中的错误。如果没有错误，我们只需将散列密码分配给我们的用户模型，并像以前一样将其保存在数据库中。它看起来会像这样:

```
/**
 * /api/routes.js
 * exports an express router.
 */ 

.........................

//to encrypt
const bcrypt = require('bcrypt');

..................
 if(hasErrors){
    //if there is any missing field
    res.status(401).json({
      message: "Invalid input",
      errors: errors
    });

  }else{
  //if all fields are present
    //encrypt user password
    bcrypt.hash(req.body.password, 10, (err, hashed_password) => {
      if(err){
        //error hashing the password
        errors.push({
          hash: err.message
        });
        return res.status(500).json(errors);
      }else{
        //if password is hashed
        //create the user with the model
        const new_user = new User({
          //assign request fields to the user attributes
          _id : mongoose.Types.ObjectId(),
          name: req.body.name,
          email: req.body.email,
          password: hashed_password
        });
        //save in the database
        new_user.save().then(saved_user => {
        //return 201, message and user details
          res.status(201).json({
            message: 'User registered',
            user: saved_user,
            errors: errors
          });
        }).catch(err => {
        //failed to save in database
          errors.push(new Error({
            db: err.message
          }))
          res.status(500).json(errors);
        })
      }
    });
  }
```

如果我们现在运行我们的测试，我们将回到绿色:)

### 登录时验证电子邮件和密码

现在我们将用户的详细信息存储在数据库中，我们可以正确地验证它们。在本文的第一部分中，我们的登录路径只是检查电子邮件和密码是否是硬编码的值(req . body . email = = ' John @ wick . com ' & & req . body . password = = ' secret ')，但是现在我们可以检查提供的详细信息是否与数据库中的任何记录相匹配。此外，由于我们存储加密的密码，我们将不得不再次使用 bcrypt 模块来确认所提供的密码是否与我们的请求中接收到的密码相匹配。我们的响应将是相同的，因此在这种情况下，我们不需要修改我们的测试:

```
 it('should return 200 and token for valid credentials', (done) => {
    //mock invalid user input
    const valid_input = {
      "email": "john@wick.com",
      "password": "secret"
    }
    //send request to the app
    chai.request(app).post('/login')
      .send(valid_input)
        .then((res) => {
          //assertions
          expect(res).to.have.status(200);
          expect(res.body.token).to.exist;
          expect(res.body.message).to.be.equal("Auth OK");
          expect(res.body.errors.length).to.be.equal(0);
          done();
        }).catch(err => {
          console.log(err.message);
        })
  });
```

在我们的 *routes.js* 文件的登录路径中，我们要做的第一件事是使用我们的用户模型的 *findOne()* 方法尝试找到一个用户，该用户的电子邮件与我们在请求正文中收到的电子邮件相同。该方法接收一个对象，该对象包含我们要搜索的字段和值( *{'email': req.body.email}* )。如果我们找到它，我们将使用 *bcrypt.compare()* 方法来验证密码是否匹配，如果有效，我们将发送与之前相同的响应，其中包括一条 200 消息、一条“Auth OK”消息和一个令牌。我们的登录路径如下所示:

```
/**
 * /api/routes.js
 * exports an express router.
 */ 

...................

router.post('/login', (req, res, next) => {
  let hasErrors = false ;
  let errors = [];

  //validate presence of email and password
  if(!req.body.email){
    errors.push({'email': 'Email not received'})
    hasErrors = true;
  }
  if(!req.body.password){
    errors.push({'password': 'Password not received'})
    hasErrors = true;
  }

  if(hasErrors){
  //return error code an info
    res.status(422).json({
      message: "Invalid input",
      errors: errors
    });

  }else{
  //check if credentials are valid
    //try to find user in database by email
    User.findOne({'email': req.body.email}).then((found_user, err) => {
      if(!found_user){
        //return error, user is not registered
        res.status(401).json({
          message: "Auth error, email not found"
        });
      }else{
        //validate password
        bcrypt.compare(req.body.password, found_user.password, (err, isValid) => {
          if(err){
            //if compare method fails, return error
            res.status(500).json({
              message: err.message
            }) 
          }
          if(!isValid){
            //return error, incorrect password
            res.status(401).json({
              message: "Auth error"
            }) 
          }else{
            //generate JWT token. jwt.sing() receives payload, key and opts.
            const token = jwt.sign(
              {
                email: req.body.email, 
              }, 
              process.env.JWT_KEY, 
              {
                expiresIn: "1h"
              }
            );
            //validation OK
            res.status(200).json({
              message: 'Auth OK',
              token: token,
              errors: errors
            })
          }
        });
      }
    });

  }
});
```

既然我们能够正确地存储用户数据并登录，那么让我们使用登录时收到的令牌来访问受保护的路由。

### 使用 JWT 访问受保护的路线

像往常一样，我们要做的第一件事是定义一个新的测试。由于这个测试将针对一个新的端点，我将创建一个新的 *describe()* 块。我们希望发送一个有效的令牌来访问端点'*/受保护的*'，我们希望收到一个 200 代码，一个包含用户名和用户电子邮件的欢迎消息。为了获得有效的令牌，我们需要使用有效的凭证登录，因此我们的测试将有两个请求:登录和受保护的:

```
/**
 * test/test.js
 * All endpoint tests for the auth API
 */

...................
describe('Protected route', () => {

  it('should return 200 and user details if valid token provided', (done) => {
    //mock login to get token
    const valid_input = {
      "email": "john@wick.com",
      "password": "secret"
    }
    //send login request to the app to receive token
    chai.request(app).post('/login')
      .send(valid_input)
        .then((login_response) => {
          //add token to next request Authorization headers as Bearer adw3R£$4wF43F3waf4G34fwf3wc232!w1C"3F3VR
          const token = 'Bearer ' + login_response.body.token;
          chai.request(app).get('/protected')
            .set('Authorization', token)
            .then(protected_response => {
              //assertions
              expect(protected_response).to.have.status(200);
              expect(protected_response.body.message).to.be.equal('Welcome, your email is john@wick.com ');
              expect(protected_response.body.user.email).to.exist;
              expect(protected_response.body.errors.length).to.be.equal(0);

              done();
            }).catch(err => {
              console.log(err.message);
            });
        }).catch(err => {
          console.log(err.message);
        });
  })

  after((done) => {
    //stop app server
    console.log('All tests completed, stopping server....')
    process.exit();
    done();
  });

}); 
```

对*/登录*端点的请求类似于我们在登录测试中发送的请求，但是我们发送给*/受保护的*端点的请求略有不同。我们使用 *set()* 方法将令牌添加到“*授权*报头中，并向其添加“*载体*”来标识身份验证的类型。像往常一样，这个测试将失败，并出现 404 错误，因为还没有定义 */protected* 端点。让我们解决这个问题。

回到我们的 *routes.js* 让我们添加我们的*/受保护的*路线，并返回一个基本响应:

```
/**
 * /api/routes.js
 * exports an express router.
 */ 

.......................

router.get('/protected', (req, res, next)=> {
  res.status(200).json({
    message: 'Welcome!',
    errors: [],
  })
})
```

显然，这不是检查令牌是否有效，所以我们可以在这里添加验证，但是，从长远来看，如果我们想在其他项目中重用这段代码，将令牌验证提取到另一个文件，一个中间件，将是一个更好的主意。快速中间件是访问*请求*和*响应*对象和*下一个*函数的函数，触发后续的中间件或函数。你可以在[快速文档](https://expressjs.com/en/guide/writing-middleware.html "Express middleware documentation")中读到更多关于它们的内容。在我们的中间件中，我们将使用 jsonwebtoken 的 *verify()* 函数来验证我们的令牌，如果它无效，我们将返回一个错误，但是如果它有效，我们将触发下一个函数。

```
/**
 * /api/middleware/check-auth.js
 * Exports an arrow funtion used as middleware by the express app.
 * Validates presence of a valid auth token in request header
 */
const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  try{
    //get the token from header. Remove 'Bearer ' with split()[].
    const token = req.headers.authorization.split(" ")[1];
    //verify method verifies and decodes the token
    const decoded = jwt.verify(token, process.env.JWT_KEY)
    //add userData from the JWT to the request
    req.userData = decoded;
    next();
  }catch(err){
    res.status(401).json({
      message: 'Auth failed',
    });
  }

}
```

这样，我们可以将这个中间件附加到应用程序中的多个路线上。现在，让我们将它添加到我们的受保护路线中:

```
/**
 * /api/routes.js
 * exports an express router.
 */ 

.......................

//import check-auth middleware
const checkAuth = require('./middleware/check-auth');

router.get('/protected', checkAuth, (req, res, next)=> {
  res.status(200).json({
    message: 'Welcome, your email is ' + req.userData.email,
    user: req.userData,
    errors: [],
  })
})
```

现在我们的测试应该通过了。

### 结论

我希望这篇文章能够帮助您理解如何使用 mongoose 模块定义模式并将数据存储在我们的数据库中，如何使用 bcrypt 模块加密密码并在登录时验证密码，以及如何创建快速中间件来分离我们后端的逻辑，就像我对检查 JWT 的中间件所做的那样。所有这些都使用 TDD 方法来确保如果我们代码中的任何变化破坏了现有的测试，我们可以很容易地识别它并修复它。

这只是一个基本的认证系统，可以通过更多的验证来改进，甚至可以改变项目结构来包含一个控制器，这样我们的路由文件就简化了。我把这些改进留给你。记住这篇文章的所有代码都可以在后面的[repo](https://gitlab.com/uF4No/authsysjs/tree/article-part-2 "code in GitLab")中找到。

本文原载于[我的网站](https://antonioufano.com/articles/building-a-javascript-jwt-auth-system-using-tdd-part-2-31)。请随时访问我，并在[联系人](http://antonioufano.com/contact)部分给我一些反馈。

编码快乐！