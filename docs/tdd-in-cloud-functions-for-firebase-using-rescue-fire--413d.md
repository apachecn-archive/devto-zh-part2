# 云函数中的 TDD 用于使用救援消防的消防基地

> 原文：<https://dev.to/star__hoshi/tdd-in-cloud-functions-for-firebase-using-rescue-fire--413d>

云功能部署非常慢。部署通常在 30 秒内完成，但有时需要 10 分钟以上。仅仅重写一行就等几分钟是浪费时间。

[云函数模拟器](https://firebase.google.com/docs/functions/local-emulator)很有用。然而，很难创建测试数据 json，也不可能编写测试。

让我们用 [rescue-fire](https://github.com/starhoshi/rescue-fire) 在本地模拟功能，并进行 TDD。

[![logo\.png \(733×207\)](img/458a2ca1754eca138dd8a8e9f9b133bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I6pPzl35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/starhoshi/rescue-fire/master/docs/logo.png)

# 如何

云函数从一个名为`event`的变量开始。

```
exports.updateUser = functions.firestore.document('users/{userId}')
  .onCreate(event => {
    console.log('old name', event.data.data().name)
    return event.data.ref.update({name: 'new name'})
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你能创建这个`event`，你就可以使用[管理软件开发工具包](https://firebase.google.com/docs/admin/setup)来运行几乎和云函数一样的代码。
[救-火](https://github.com/starhoshi/rescue-fire)使此`event`。

## 1。装置

```
npm install rescue-fire --only=dev
yarn add --dev rescue-fire 
```

Enter fullscreen mode Exit fullscreen mode

## 2。准备 Google 云帐户凭据

下载服务帐户密钥 json 文件。

[https://firebase.google.com/docs/admin/setup?authuser = 0 # add _ firebase _ to _ your _ app](https://firebase.google.com/docs/admin/setup?authuser=0#add_firebase_to_your_app)

这个 json 文件很敏感，要小心。

> 重要事项:此文件包含敏感信息，包括您的服务帐户的私有加密密钥。请保密，不要将它存储在公共存储库中。

## 3。安装测试库

请使用您喜欢的测试库。

比如在 [Jest](https://facebook.github.io/jest/) 的情况下:

```
npm install jest --only=dev
yarn add --dev jest 
```

Enter fullscreen mode Exit fullscreen mode

### 4。写一个测试

让我们创建一个函数，在创建用户时更新名称。该函数的代码如下。

此示例是用 TypeScript 编写的。

```
const changeName = (event: functions.Event<DeltaDocumentSnapshot>) => {
  console.log('old name', event.data.data().name)
  return event.data.ref.update({ name: 'new name' })
} 
```

Enter fullscreen mode Exit fullscreen mode

测试会是这样的。

```
import 'jest'
import * as admin from 'firebase-admin'
import * as functions from 'firebase-functions'
import * as Rescue from 'rescue-fire'

// Set up to run firebase in local.
beforeAll(() => {
  const serviceAccount = require('./your-firebase-adminsdk.json')
  admin.initializeApp({
    credential: admin.credential.cert(serviceAccount)
  })
})

test('update name', async () => {
  // prepare
  const data = {name: 'name'}
  const user = await admin.firestore().collection('user').add(data)
  const event = Rescue.event(user, data)

  // start Cloud Functions
  await changeName(event)

  // expect name changed
  const updatedUser = await admin.firestore().collection('user').doc(user.id).get()
  expect(updatedUser.data()!.name).toBe('new name')
}) 
```

Enter fullscreen mode Exit fullscreen mode

云功能可以用 TDD 开发。(严格来说不是 TDD🙃)

这是一个很小的功能，但是 [orderable.test.ts](https://github.com/starhoshi/orderable.ts/blob/master/orderable/src/__tests__/orderable.test.ts) 正在用 rescue-fire 测试巨大的功能。

可选参数 defenitions 是[这里的](https://github.com/starhoshi/rescue-fire/blob/master/src/index.ts#L18)。

### 4。最后，创建函数

```
exports.updateUser = functions.firestore
  .document('users/{userId}')
  .onCreate(event => {
    return changeName(event)
}) 
```

Enter fullscreen mode Exit fullscreen mode

🎉

## 备注

救援火灾造成的事件尚未完成。我认为写测试就足够了，但是要记住它不同于实际事件。

请使用 [starhoshi/rescue-fire:云功能的测试助手。](https://github.com/starhoshi/rescue-fire)！