# NodeJS -单元测试-不触及数据库的测试。困惑。

> 原文：<https://dev.to/kubaapk/nodejs---unit-tests---testing-without-hitting-database-confusings-3448>

我正在努力学习 TDD 方法。对于简单的代码，我不需要模仿或存根任何外部方法或依赖项，但在为一些基于数据库的代码编写测试时，我会感到有点困惑。

对我来说很清楚，单元测试应该测试一小段不依赖于其他服务等等的代码。

假设我想对一个创建用户的特性进行单元测试。

我知道在 TDD 中我应该从写失败的测试开始——但是我不知道它应该是什么样子。；)

所以，这里有个例子，简化服务:

```
class UserService {

  // typeORM repository
  constructor(userRepository: Repository<User>) {}

  async createAccount(user) {

   // Saves user to database and return created entity object
   return await this.userRepository.save(user);
  } 

} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我已经能够监视方法并检查是否正确调用了，参数是否正确等等。(我用的是 Jest)

```
it('Should create a user.', async () => {

  const USER = {
   email: 'test@user.com',
   username: 'user',
   password: 'user',   
  };

  const SPY = jest.fn(() => USER);
  jest
    .spyOn(userService, 'createAccount')
    .mockImplementation(() => SPY(USER));

  await userService.createAccount(USER);
  expect(SPY).toHaveBeenCalledTimes(1);
  expect(SPY).toHaveBeenCalledWith(USER);

}); 
```

Enter fullscreen mode Exit fullscreen mode

我的主要问题来了。失败的测试应该是什么样的？我的意思是，如果我像上面那样 stub createAccount 方法，它总是会通过。即使该方法为空。它只需要方法声明。我应该模仿返回 typeORM 应该返回的内容的存储库方法吗？例如:

```
const REPOSITORY_MOCK = jest.fn(() => ({
    save: jest.fn().mockImplementation((user) => user),
})); 
```

Enter fullscreen mode Exit fullscreen mode

我想我错过了什么。:/

或者我是不是想把它搞得太复杂了，我应该只测试方法是否被正确调用？整个数据库相关的东西应该由集成测试测试，我可以在数据库上工作？

我期待任何形式的帮助。提前感谢。

干杯，库巴。