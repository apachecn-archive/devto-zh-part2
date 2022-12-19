# 使用 WarShield 保护您的数据

> 原文：<https://dev.to/quantumsheep/protect-your-data-with-warshield-3n6i>

安全随着信息技术的发展而发展，人们找到了保护数据和隐私的新方法，因为我们需要安全感。有时你想深入安全领域，你开始到处设置密码。这就是 WarShield 被创建的原因。

# 什么是 WarShield？

WarShield 是一个 CLI 工具，用于使用 AES-256 的密码加密和解密您的文件。这是为了确保一个 USB 闪存驱动器或外部硬盘驱动器内的完整文件保护。如果这些丢失了，恶意的人可以接管你的文件和他们的数据。

这时，WarShield 推出了快速可靠的解决方案。

# 如何使用 WarShield？

战盾可以安装 NPM:

```
npm install -g warshield 
```

(非常)简短的安装之后，您可以使用`warshield` CLI。

```
warshield (encrypt|decrypt) (file) 
```

第一个参数是`encrypt`或`decrypt`，描述想要的动作。不要担心，如果你试图解密的东西没有加密，它不会这样做。

接下来是您想要加密/解密的文件(或文件夹)。如果它是一个文件夹，其中的每个文件都将被递归处理。

启动该命令将使程序询问您想要或需要用来加密或解密文件的密码。

当你启动加密/解密时，WarShield 将首先扫描所有想要的文件。如果所选文件在文件夹中，可能需要一段时间，这取决于文件的重量。

https://www.npmjs.com/package/warshield/
Github 仓库:[https://github.com/QuantumSheep/warshield/](https://github.com/QuantumSheep/warshield/)

由于💖如果你喜欢这篇文章:)