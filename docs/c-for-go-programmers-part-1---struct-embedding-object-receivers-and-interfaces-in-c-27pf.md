# 面向 Go 程序员的 C++:第 1 部分——c++中的结构嵌入、对象接收器和接口

> 原文：<https://dev.to/dayvonjersen/c-for-go-programmers-part-1---struct-embedding-object-receivers-and-interfaces-in-c-27pf>

> **免责声明** : *本系列标题的灵感来自[Go Wiki](https://github.com/golang/go/wiki/GoForCPPProgrammers)上的一篇精彩文章，但与那篇文章不同，本系列中的任何信息都不应被视为合理的建议。作者假定读者在现代 C++和 Go 方面的核心能力超过了作者自己的能力。这是一个不加掩饰的求助，求助者是一个有太多空闲时间并且对传统 OOP 有着非理性恐惧的人。[参见第 0 部分的介绍](#coming-soon-i-promise)。*

### 结构嵌入

Go 提供了 struct 嵌入作为继承的唯一替代选择 <u>和</u>:

```
package main

type parent struct {
  something int
}

type child struct {
  parent
  somethingElse float64
}

func main() {
  c := child{}
  c.something = 42
} 
```

但是如果你`fmt.Printf("%#v", c)`你会看到这实际上是如何工作的:

```
main.child{
  parent:main.parent{something:42},
  somethingElse:0
} 
```

事实上，你也可以通过做`c.parent.something`来访问`something`。

因此，至少在概念层面上，C++的等价物是:

```
struct parent {
  int something;
};

struct child {
  parent parent;
  double somethingElse;
};

int main() {
  child c = {0};
  c.parent.something = 42;

  return 0;
} 
```

除了一些`typedef`之外，C 语言也是如此，这并不新鲜。c 程序员一直都在做这种事情。

因此，我们可以通过使用结构字段来继承成员。

### 对象接收器

Go 允许我们在已定义的类型上定义方法。不仅仅是结构，让我们保持简单。

```
type myStruct struct {
  something int
}

func (s *myStruct) Method() {
  s.something = 42
} 
```

对象接收器是指针类型的，而不是作为一个值，以便在调用时实现更改

```
s1 := &myStruct{}
s1.Method() // s1.something == 42 
```

从概念上讲，这相当于:

```
func Method(s* myStruct) {
  s.something = 42
} 
```

事实上，C/C++的等价物在 C 代码中非常常见:

```
struct my_struct {
  int something;
};

my_struct_method(my_struct* s) {
  s->something = 42;
} 
```

对象接收器作为作用于结构的函数的第一个参数，是 C 语言中在“对象”上声明“方法”的实际方式，并且是有效的 C++。这不是什么新鲜事。

### 界面

让我们把目前已经建立的东西结合起来，做一些完全疯狂的事情。

套用派克司令的话来说，围棋的界面模型是

> 如果一个对象拥有一个接口的所有方法，那么它满足这个接口。

有一些代码:

```
#include <stdlib.h>

#include <functional>
#include <iostream>
#include <vector>

struct some_interface {
  std::function<void()>* Method;
};

some_interface* new_some_interface() {
  return (some_interface*) calloc(1, sizeof(some_interface));
}

struct parent {
  some_interface* some_interface;
};

parent* new_parent() {
  parent* p = (parent*) calloc(1, sizeof(parent));
  p->some_interface = nullptr;
  return p;
}

struct child {
  parent* parent;
  int something;
};

child* new_child() {
  return (child*) calloc(1, sizeof(child));
}

child_method(child* c) {
  c->something = 42;
}

int main() {
  child* c1  = new_child();
  child* c2  = new_child();
  c1->parent = new_parent();
  c2->parent = new_parent();

  c1->parent->some_interface = new_some_interface();
  c1->parent->some_interface->Method = new std::function<void()>([=]() -> void {
    child_method(c1);
  });

  std::vector<parent*> parents;
  parents.push_back(c1->parent);
  parents.push_back(c2->parent);

  for(auto p : parents) {
    if(p->some_interface != nullptr) {
      (*p->some_interface->Method)();
    }
  }

  std::cout << "c1.something: " << c1->something << "\n";
  std::cout << "c2.something: " << c2->something << "\n";

  return 0;
} 
```

现在，如果你还和我在一起，并且你没有完全厌恶地关闭浏览器标签，让我解释这一切，以及为什么我认为它比多态和继承更容易考虑(即使它看起来更难)。

#### 工作原理

`parent`是一个祖先，在某些方面相当于一个抽象基类。它保存了指向“子类”可能拥有的所有接口的指针，但与抽象虚函数不同，所有这些方法不一定必须由子类定义。当在父类上调用方法时，只需在调用所需的方法之前检查接口是否已被定义。

`parent`被子类如`child`所“继承”，但是人们可以想象`step_child`和`adopted_orphan`也有一个`parent`字段。`parent`是被传递的，因为它们暴露了它们的`children`定义的接口。

这些定义以 C++ `lambda`函数<sup>的形式出现，这是 c++11 中的新功能！</sup>因为它们是闭包，可以捕获周围范围内的变量。

但是它们也可以是普通的 C 函数指针，只是需要从 void*到显式类型来回转换:

```
// sorry that this example is so different from the one above but it's what i had on hand

#include <stdio.h>

typedef struct _interface {
    int(*CommonMethod)(void*);
} _interface;

typedef struct object_a {
    int prop;
    _interface iface;
} object_a;

typedef struct object_b {
    int different_prop;
    _interface iface;
} object_b;

void func_that_takes_interface(void* obj, _interface iface) {
    printf("%d\n", iface.CommonMethod(obj));
}

int object_a_common_method(void* obj) {
    object_a* a = (object_a*)obj;
    return a->prop * a->prop;
}

int object_b_common_method(void* obj) {
    object_b* b = (object_b*)obj;
    return b->different_prop * 2;
}

int main() {
    object_a a = {0};
    object_b b = {0};

    a.iface.CommonMethod = object_a_common_method;
    b.iface.CommonMethod = object_b_common_method;

    func_that_takes_interface((void*)&a, a.iface);
    func_that_takes_interface((void*)&b, b.iface);

    a.prop = 9;
    b.different_prop = 9;

    func_that_takes_interface((void*)&a, a.iface);
    func_that_takes_interface((void*)&b, b.iface);

    a.prop = 4;
    b.different_prop = 10;

    func_that_takes_interface((void*)&a, a.iface);
    func_that_takes_interface((void*)&b, b.iface);

    return 0;
} 
```

> 应该注意的是，C++ `lambda`不仅让你放弃了这种不便，还让你可以内联定义一个函数体(这正是它们的设计初衷...)但这与我接下来要说的相悖:

在这两种情况下，我们都可以提供一个常规函数，它使用一个`object receiver`作为满足接口方法的函数。以这种方式，接口不需要与作用于一组数据的特定方法接口，但是可以在出现这样的情况时使用，即尽管定义了相似的方法，但是数据集是不同的类型。

#### 实现细节

`std::function<>`是保存指向`lambda`的指针所必需的。上面定义的那个实际上是类型`main::lambda<void()>`。

即使在这个玩具示例中，堆分配也是必要的。

为了清楚起见，上面省略了相应的`free`功能，但它们仍然是必要的:

```
free_parent(parent* p) {
  if(p->some_interface != nullptr) {
    delete p->some_interface->Method;
  }
  free(p);
}

free_child(child* c) {
  free_parent(c->parent);
  free(c);
} 
```

#### 在练功

我在做游戏。每个`player`、`enemy`和`npc`都有一个`entity`结构字段。该实体具有由游戏循环调用的接口，如`renderable`和`updateable`。到目前为止，它按预期工作。接下来我需要添加`collidable`和`interactable`，但是我太忙了，不能做一个`nodev`