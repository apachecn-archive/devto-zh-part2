# 使用可转换的“when”语句

> 原文：<https://dev.to/engonzal/using-ansible-when-statements-d47>

使用 ansi ble“when”语句允许您设置任务何时结束的参数。我收集了一些我遇到的如何使用基本 when 语句的例子。

## 布尔值(真或假)

在下面的示例剧本中，当“test_var”变量为真时，我打印“Hello world”。在下面的例子中，我们使用“bool”过滤器将 test_var 评估为布尔值(即真或假)，然后确保它为真:

```
- hosts: localhost
  user: engonzal
  connection: local
  tasks:
    - name: test_var is "{{ test_var }}"
      debug: msg="Hello world."
      when: test_var|bool == true 
```

注意，不要使用“test_var|bool == true”，可以简单地使用“when: test_var”。要评估 test_vars 何时为 false，可以使用“test_var|bool == false”

## 定义的变量

有时，您会希望在运行任务或另一个 when 语句之前检查变量是否已定义。下面的示例有两个任务，第一个任务在定义“test_var”时运行。第二个在没有定义“test_var”时运行。因为我们在行动手册的“var”部分定义了“test_var ”,所以第二个任务将显示为“跳过”

```
- hosts: localhost
  user: engonzal
  connection: local
  vars:
    test_var: true
  tasks:
    - name: test_var is "{{ test_var }}" (when defined)
      debug: msg="test_var is defined"
      when: test_var is defined

    - name: test_var is "{{ test_var }}" (when not defined)
      debug: msg="test_var is not defined"
      when: test_var is not defined 
```

## 精梳试验

我们也可以选择用“和”来组合变量。

```
- hosts: localhost
  user: engonzal
  connection: local
  vars:
    dog: true
  tasks:
    - name: dog is defined, cat is defined
      debug: msg="Hello dog and cat."
      when: dog is defined and cat is defined

    - name: dog is defined or cat is defined
      debug: msg="Hello dog or cat."
      when: dog is defined or cat is defined 
```

您还可以将“和”语句格式化为“when”项

```
 - name: dog is defined, cat is defined
      debug: msg="Hello dog and cat."
      when:
        - dog is defined
        - cat is defined 
```

## 字符串测试

还可以使用 when 语句来验证变量是否等于字符串。在下面的例子中，我们检查“狗”是否等于“骨头”。注意我用的是“狗|下”。这会将 dog 变量中的所有字符转换为小写。

```
- hosts: localhost
  user: engonzal
  connection: local
  vars:
    dog: bone
  tasks:
    - name: dog is bone, cat is undefined
      debug: msg="dog is equal to bone"
      when: dog|lower == "bone" 
```

现在让我们看看 cat，注意，因为我们没有在 vars 部分定义 cat，所以我们想首先检查是否定义了 cat。此任务将被跳过，因为未定义 cat，并且不会计算“cat | lower = =”fish。

```
 - name: cat is equal to fish
      debug: msg="cat likes fish"
      when:
        - cat is defined
        - cat|lower == "fish" 
```

或者，您可以使用默认值过滤器为“cat”使用默认值

```
 - name: cat is equal to fish
      debug: msg="cat is equal to fish"
      when: cat | default('fish') | lower == "fish" 
```

## 验证文件是否存在

您可以使用“stat”模块来检查文件或目录是否存在。你需要做的就是运行 stat 模块，并添加“注册:我的新变量”。然后我们可以评估这个变量:

```
- hosts: localhost
  user: engonzal
  connection: local
  tasks:
    - name: Check if /tmp exists
      stat:
        path: /tmp
      register: stat_tmp

    - name: Say Hello when "/tmp" exists
      debug: msg="Hi! the directory /tmp exists"
      when:
        - stat_tmp.stat.exists|bool == true 
```

## 查找字符串

您还可以使用查找过滤器来检查变量中是否存在某些内容。在下面的例子中，我们检查变量“cat”是否包含单词“plays”。

```
- hosts: localhost
  user: engonzal
  connection: local
  vars:
    cat: "cat plays with yarn"
  tasks:
    - name: Check if cat "plays"
      debug: msg="The cat does play"
      when: cat.find("plays") != -1 
```

Ansible 的一个非常有用的特性是可以灵活地只在需要的时候运行任务。内置测试允许您在如何构建剧本和角色方面有很大的自由度。这些是一些基本例子，但是它们可能会变得非常复杂。Ansible 的文档中也有一些关于如何使用“测试”的很好的例子。

将来我会收集一些更有趣的例子！如果你是新手，可以看看用 virtualenv 设置 ansible 的指南。

使用 ansi ble“when”语句的帖子最早出现在[建立家庭实验室](https://buildahomelab.com)上。