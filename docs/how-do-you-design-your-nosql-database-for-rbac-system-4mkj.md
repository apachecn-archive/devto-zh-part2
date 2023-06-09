# 你是如何为 RBAC 系统设计 noSQL 数据库的？

> 原文：<https://dev.to/mandaputtra/how-do-you-design-your-nosql-database-for-rbac-system-4mkj>

RBAC 就像一个用户角色系统，在 word-press 或任何 CMS 和 dev.to 拥有它。但是如何在 mongodb/nosql 中设计你的数据库呢？既然 nosql 有好有坏，它是不稳定的，不太关心我们的数据库设计(IMO ),你如何在 mongodb/noSQL 上实现它？以收藏为支点？和 SQL 数据库一样？

事情是这样的

1.  用户有一个角色
2.  用户有一个菜单
3.  有些用户只能看到菜单，但不能编辑/更新菜单
4.  一些用户完全获得菜单的所有访问权

编辑:

到目前为止，我的模式方法如下:

**menus.model.js**

```
module.exports = function (app) {
  const mongooseClient = app.get('mongooseClient');
  const { Schema } = mongooseClient;
  const menus = new Schema({
    name: { type: String, required: true },
    slug: { type: String, required: true },
    menu_roles: [{
      roles_id: { type: Schema.Types.ObjectId, ref : 'Roles' },
      roles_name: { type:  String },
      create: { type: Boolean },
      delete: { type: Boolean },
      update: { type: Boolean },
      read: { type: Boolean },
    }]
  }, {
    timestamps: true
  });

  return mongooseClient.model('menus', menus);
}; 
```

Enter fullscreen mode Exit fullscreen mode

roles.models.js

```
module.exports = function (app) {
  const mongooseClient = app.get('mongooseClient');
  const { Schema } = mongooseClient;
  const roles = new Schema({
    name: { type: String, required: true },
    slug: { type: String, required: true },
  }, {
    timestamps: true
  });

  return mongooseClient.model('roles', roles);
}; 
```

Enter fullscreen mode Exit fullscreen mode

users.models.js

```
module.exports = function (app) {
  const mongooseClient = app.get('mongooseClient');
  const { Schema } = mongooseClient
  const users = new mongooseClient.Schema({

    email: {type: String, unique: true, lowercase: true},
    password: { type: String },
    first_name: { type: String },
    last_name: { type: String },
    roles: { type: Schema.Types.ObjectId, ref : 'Roles' },

  }, {
    timestamps: true
  });

  return mongooseClient.model('users', users);
}; 
```

Enter fullscreen mode Exit fullscreen mode

还在工作中，非常欢迎反馈谢谢！