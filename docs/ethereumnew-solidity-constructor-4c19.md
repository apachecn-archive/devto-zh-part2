# 以太坊:新的实体构造器

> 原文：<https://dev.to/sot528/ethereumnew-solidity-constructor-4c19>

从 [Solidity v0.4.23](https://github.com/ethereum/solidity/releases/tag/v0.4.23) 开始，Solidity 有了新的构造器符号。

与旧的一种被弃用。

# 旧

```
pragma solidity 0.4.23;

contract Foo {
  function Foo() public {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 新增

```
pragma solidity 0.4.23;

contract Foo {
  constructor() public {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 注

旧的在下面升起。

```
Warning: Defining constructors as functions with the same name as the contract is deprecated. Use "constructor(...) { ... }" instead. 
```

Enter fullscreen mode Exit fullscreen mode