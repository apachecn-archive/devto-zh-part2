# 在 Vue.js 中构建三个常见的表单界面

> 原文：<https://dev.to/raymondcamden/building-three-common-form-interfaces-in-vuejs-55ib>

今天我想分享三个简单的(大部分是简单的)Vue.js 示例，它们展示了一些常见的表单 UX 模式。在每一种情况下，我都完全希望我可以使用现有的 Vue 组件，但一如既往，我坚信自己构建东西是实践所学的一种方式。记住这一点，让我们开始吧！

# 复制字段

对于第一个演示，我将展示一个表单示例，它允许您“复制”一组字段来输入附加数据。这可能没什么意义，所以我们先从演示开始，这样你就能明白我的意思了:

[https://codepen.io/cfjedimaster/embed/EEEdja/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/EEEdja/?height=600&default-tab=result&embed-version=2)

该表单由两部分组成。顶部是一组基本的静态字段。底部是你可以输入朋友信息的地方。因为我们不知道你可能有多少朋友，所以使用一个字段来添加额外的行。让我们看看它的标记。

```
<form id="app">

  <fieldset>
    <legend>Basic Info</legend>
    <p>
      <label for="name">Name</label>
      <input id="name" v-model="name">
    </p>

    <p>
      <label for="age">Age</label>
      <input id="age" v-model="age" type="number">
    </p>
  </fieldset>

  <fieldset>
    <legend>Friends</legend>

    <div v-for="(f,n) in friends">
      <label :for="'friend'+n">Friend {{n+1}}</label>
      <input :id="'friend'+n" v-model="friends[n].name">
      <label :for="'friendage'+n">Friend {{n+1}} Age</label>
      <input :id="'friendage'+n" v-model="friends[n].age" type="number">
    </div>

    <p>
      <button @click.prevent="newFriend">Add Friend</button>
    </p>
  </fieldset>

  <p>Debug: {{friends}}</p>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

顶部是香草 Vue 装订。底部是有趣的部分。首先，我迭代一个朋友列表。这就是点击按钮时“生长”的东西。注意`(f,n)`的用法。这使我可以访问每个朋友以及一个计数器。这是一个从零开始的数，所以当我渲染它的时候，我给它加 1。还要注意我如何正确地使用带有动态 ID 值的标签:`:id="'friend'+n"`。一开始写起来有点奇怪，但是效果很好。

JavaScript 非常简单:

```
const app = new Vue({
  el:'#app',
  data:{
    name:null,
    age:null,
    friends:[{name:'',age:''}]
  },
  methods:{
    newFriend() {
      //New friends are awesome!
      this.friends.push({name:'', age:''});
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一真正有趣的部分是用第一组值默认朋友，所以我在 UI 中至少得到了`Friend 1`。

# 发货与计费相同

我想构建的下一个 UX 是你通常在订单结帐中看到的东西，“发货与计费相同”(反之亦然)。基本上，让用户跳过输入同一个地址两次。这是完成的演示:

[https://codepen.io/cfjedimaster/embed/zWWWzP/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/zWWWzP/?height=600&default-tab=result&embed-version=2)

我认为这很简单，我想也是，但是我不确定一旦复选框被选中，它会有什么反应。我的意思是，你说发货是一样的，是不是要一直更新？我的意思是，如果您更改了账单街道，我们会再次更新发货街道吗？但是如果修改了航运街呢？如果您使用复选框，我们应该禁用运输吗？但是，如果您想使用这个特性来设置大多数字段，然后调整一个字段，该怎么办呢？是啊，会变得很乱。我决定吻一下，然后复制一份(如果你正在检查的话)，然后就不用担心了。我肯定有人会说我完全错了。下面是标记:

```
<form id="app">
  <fieldset>
    <legend>Billing Address</legend>

    <p>
      <label for="bstreet">Street</label>
      <input id="bstreet" v-model="billing_address.street">
    </p>

    <p>
      <label for="bcity">City</label>
      <input id="bcity" v-model="billing_address.city">
    </p>

    <p>
      <label for="bstate">State</label>
      <select id="bstate" v-model="billing_address.state">
        <option value="ca">California</option>
        <option value="la">Louisiana</option>
        <option value="va">Virginia</option>
      </select>
    </p>

    <p>
      <label for="bzip">Zip</label>
      <input id="bzip" v-model="billing_address.zip">
    </p>

  </fieldset>

  <fieldset>
    <legend>Shipping Address</legend>

    <input type="checkbox" @change="copyBilling" id="sSame" v-model="sSame"> <label for="sSame" class="sSame">Shipping Same as Billing</label><br/>

    <p>
      <label for="sstreet">Street</label>
      <input id="sstreet" v-model="shipping_address.street">
    </p>

    <p>
      <label for="scity">City</label>
      <input id="scity" v-model="shipping_address.city">
    </p>

    <p>
      <label for="sstate">State</label>
      <select id="sstate" v-model="shipping_address.state">
        <option value="ca">California</option>
        <option value="la">Louisiana</option>
        <option value="va">Virginia</option>
      </select>
    </p>

    <p>
      <label for="szip">Zip</label>
      <input id="szip" v-model="shipping_address.zip">
    </p>

  </fieldset>

  <!-- debug -->
  <p>
    sSame {{sSame}}<br/>
    Billing {{billing_address}}<br/>
    Shipping {{shipping_address}}
  </p>

</form> 
```

Enter fullscreen mode Exit fullscreen mode

这里是 JavaScript:

```
const app = new Vue({
  el:'#app',
  data:{
    sSame:false,
    billing_address:{
      street:null,
      city:null,
      state:null,
      zip:null
    },
    shipping_address:{
      street:null,
      city:null,
      state:null,
      zip:null
    }

  },
  methods:{
    copyBilling() {
      if(this.sSame) {
        for(let key in this.billing_address) {
          this.shipping_address[key] = this.billing_address[key];
        }
      }
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

有趣的部分在`copyBilling`里。我为`sSame`这个名字道歉——它有点糟糕。

# 从左向右移动

在最后一个演示中，我做了一个“东西”,左边有项目，右边有项目，你可以点击来来回回移动它们。可能有一个更好的名字，如果你有，请在下面留言。这是演示。

[https://codepen.io/cfjedimaster/embed/mxxxXZ/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/mxxxXZ/?height=600&default-tab=result&embed-version=2)

这种方法的棘手之处在于，用于存储数据的选择字段只要求您在想要移动项目时选择它们。所以我需要记录每个盒子里的所有物品，以及你选择的时间。这是标记。

```
<form id="app">

  <div class="grid">
    <div class="left">
      <select v-model="left" multiple size=10>
        <option v-for="item in leftItems" :key="item.id" 
                :value="item">{{item.name}}</option>
      </select>
    </div>

    <div class="middle">
      <button @click.prevent="moveLeft">&lt;-</button>
      <button @click.prevent="moveRight">-&gt;</button>
    </div>

    <div class="right">
      <select v-model="right" multiple size=10>
         <option v-for="item in rightItems" :key="item.id" 
                :value="item">{{item.name}}</option>       
      </select>
    </div>
  </div>

  <!-- debug -->
  <p>
    leftItems: {{ leftItems}}<br/>
    left: {{ left}}<br/>
    rightItems: {{ rightItems }}<br/>
    right: {{ right }}
  </p>

</form> 
```

Enter fullscreen mode Exit fullscreen mode

这是 JavaScript。这次有点复杂。

```
const app = new Vue({
  el:'#app',
  data:{
    left:[],
    right:[],
    leftItems:[],
    rightItems:[],
    items:[
      {id:1,name:"Fred"},
      {id:2,name:"Ginger"},
      {id:3,name:"Zeus"},
      {id:4,name:"Thunder"},
      {id:5,name:"Midnight"}
    ]

  },
  created() {
    this.leftItems = this.items;
  },
  methods:{
    moveRight() {
      if(this.left.length === 0) return;
      console.log('move right');
      //copy all of this.left to this.rightItems
      //then set this.left to []
      for(let x=this.leftItems.length-1;x>=0;x--) {
        let exists = this.left.findIndex(ob => {
          return (ob.id === this.leftItems[x].id);
        });
        if(exists >= 0) {
          this.rightItems.push(this.leftItems[x]);
          this.leftItems.splice(x,1);
        }
      }
    },
    moveLeft() {
      if(this.right.length === 0) return;
      console.log('move left');
      for(let x=this.rightItems.length-1;x>=0;x--) {
        let exists = this.right.findIndex(ob => {
          return (ob.id === this.rightItems[x].id);
        });
        if(exists >= 0) {
          this.leftItems.push(this.rightItems[x]);
          this.rightItems.splice(x,1);
        }
      }
    }

  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

基本上在点击按钮的时候，我会查看所有的条目，对于每一个条目，我会查看它是否存在于所选条目的列表中，如果存在，它会向左或向右移动。我觉得这可能会有点瘦(我会再次提醒人们，我是一个骄傲的失败的谷歌受访者)，但它的工作。记得你可以叉我的代码笔，所以我想看到这个更光滑的版本。

所以-你觉得怎么样？请在下面给我留下您的建议、修改或错误修复的评论！