# 在 windows 中安装和配置 oracledb 节点模块

> 原文：<https://dev.to/mohanant/install--configure-oracledb-node-module-in-windows-41fg>

嗨，朋友们，我是这个社区的新成员，这是我的第一篇帖子。TADA！！。好了，回到最近的话题，当我遇到一个使用 oracledb 作为 nodejs 服务器后端的需求时。在阅读了 oracle 及其 npm 库支持的文档后，我相信 oracledb 的 npm 安装就足够了，但这只是迟早的事。我想通了，这将是一项艰巨的任务。我花了一天时间安装并建立 nodejs 和 oracle 服务器之间的连接。

# 安装 oracledb

根据 oracledb node github page[Link](https://oracle.github.io/node-oracledb/INSTALL.html#quickstart)，命令 *npm install oracledb* 必须足以安装 oracledb 软件包，但当且仅当您的计算机系统有 Oracle db 的二进制文件时，我们才不会有它。因此，为了安装它，我遵循了以下步骤，我建议每个人都按顺序来做，以便立即开始。

1.  安装 [Python](https://www.python.org/downloads/)
    *   安装 python 后，为 python 设置环境变量。在路径中的系统变量下，添加/追加 python.exe 的路径。
2.  安装支持 C++ 11 的 C 编译器
    *   安装 Visual Studio 2015 社区版[链接](https://visualstudio.microsoft.com/downloads/)
    *   以防万一，如果编译器不工作，我们需要设置它。这不是一个单调乏味的过程，因为如果我们尝试创建一个 C++项目，然后安装所需的编译器并设置环境，这可以在内部完成。
    *   这里要注意的一点是，检查你是否有必要的 C++可再发行版。
3.  用于 Windows 的 Oracle 即时客户端
    *   下载即时客户端软件包-基础版和即时客户端软件包- SDK [链接](http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html)
    *   将两个 zip 文件中的内容复制到 C:\Oracle\instantclient。
    *   按照前面的说明设置系统变量。包括 instantclient 的 location to PATH 变量，如 PATH: C:\Oracle\instantclient。
    *   创建一个名为 OCI _ 库 _ 目录的新变量，并将值添加为 C:\ Oracle \ instant client \ SDK \ LIB \ msvc。`OCI_LIB_DIR: C:\Oracle\instantclient\sdk\lib\msvc`
    *   创建一个名为 OCI 目录的新变量，并将值添加为 C:\ Oracle \ instant client \ SDK \ include。`OCI_INC_DIR: C:\Oracle\instantclient\sdk\include`
4.  运行`npm install oracle/node-oracledb.git#v2.3.0`。这应该足够了，tada oracledb 节点模块在二进制编译后安装到我们的工作目录中。
5.  别忘了安装 github :-)

#### 面临的问题

1.  此错误主要是由于依赖项不匹配造成的。所有版本必须在相同的架构下。
2.  事后看来，不要使用 12c，因为它在从 oracle 检索时会修剪返回文本