# React-Rails app -如何验证唯一性？

> 原文：<https://dev.to/patrishio/react-rails-app---how-to-validate-uniqueness-1g3e>

你好，
这是我的第一篇帖子。如果我做错了什么，请告诉我。我是 Ruby on Rails 开发人员，开始使用模型视图和抽象形式。

我正在建立一个管理仓库的应用程序。它已经完成并投入生产(使用 Rails、Turbolinks 和 jQery 创建)。现在我想改变这个应用反应单页应用反应-原生移动版本。

我现在知道了 React、Redux、React-Native 的基本知识，并开始改变流程。

我创建了一个简单的 react-rails 应用程序。我在 react 中创建了一个表单，在 rails 中创建了 db，如果他的名字是唯一的，我想创建记录。

我通过 axios 连接到 rails，当我试图创建没有唯一名称的记录时，axios 发回反应操作验证错误。

//ACTION
export const startAddingLocation =(name)=>{

return(dispatch)= > {

const location = { name }

return axios . post('/API/locations '，{ location })
。然后(response =>{
if(response . data[' status ']= = = ' SUCCESS '){
const id = response . data . data . id；

const created _ at = response . data . data . created _ at；

分派(addLocation({id，...location，created _ at }))；

}
else {
dispatch(nameChangedError(name，response . data[' message '])；
}
})。catch(error =>{
dispatch(nameChangedError(name，error+"无法保存在 DB 中"))
})；
}
}

我需要找到一种方法，当有一个验证错误时，不要从重定向。做这件事的最佳路线是什么？我试图重定向时，行动名称改变错误填补错误在 redux，但这是一个异步的行动，当我检查错误时，在组件仍然是空的。我应该以某种方式改变行动方向吗？

//COMPONENT ON SUBMIT
onButtonPress(){

const { name，error } = this.props

if(name&T15】name . length<30){

this . props . startaddinglocation(name)；

如果(！error){
this . props . history . push('/locations ')；
}
} else {
this . props . namechangederror(name，"不正确的值")
}

谢谢！