# 使用 GoogleTest 测试 CPP

> 原文：<https://dev.to/nina_rallies/testing-cpp-using-googletest-4p72>

我一直在尝试使用 googleTest 测试一段非常简单的代码，这里讨论了！，我花了几天时间才搞定。对我来说，主要的问题是能够设置环境。这篇文章是关于如何设置环境的，后面是我犯过的错误列表。
基本上，我想做的是将我的项目与测试分开，这样测试就可以进入一个单独的项目。

1.  去 [GoogelTest - Github](https://github.com/google/googletest) 那里你可以找到关于如何使用它的文档、代码和几个例子。您可以将其克隆到您的本地或下载。
2.  打开 Eclipse。假设我们正在测试一个名为 base_project 的项目，现在您可能需要创建另一个名为 unit_test 的项目。
3.  在您的测试项目中，您需要创建以下内容:
    1.  存放 googletest 库的文件夹，姑且称之为 googleTestLib
    2.  存放单元测试的文件夹，我们称之为测试
4.  使用您的命令提示符，cd 进入您存储 googleTest 的位置。在这个路径\googletest\googletest\scripts 中，应该有一个名为 fuse_gtest_files.py 的文件。\ fuse _ gtest _ files.py {项目位置}\ googleTestLib。刷新您的 unit_test 项目，googleTestLib 现在应该有一个名为 gtest 的文件夹，其中包含 gtest.h 和 gtest-all.cc 文件。
5.  右键单击 googleTestLib 文件夹，转到资源配置，并将其从“发布”中排除
6.  右键点击 googleTestLib 文件夹，进入属性，在 C/C++ Build 下点击设置，找到 GCC C++编译器下的 include 链接，点击它。现在点击 add 按钮，将文件夹(googleTestLib)作为包含路径添加到这里。
7.  假设在你的 base_project 中，你有一个名为 base_class 的类，你想为它写一个测试。base_class 可能位于./src 文件夹中，您需要将该文件夹链接到 unit_test 项目，以便以后可以将它包含在头文件中。为此，右键单击您的 unit_test 项目，转到 properties，在 C/ C++ General 下找到路径和符号，单击 Source Location，找到 Link Folder 按钮，勾选 Link to Folder in the file system 选项，浏览并找到包含所有。cpp 和。运行测试所需的 hpp 文件。
8.  一旦完成上述步骤，您将看到显示 base_project 链接的文件夹，这些链接显示在您的 unit_test 项目中。确保您将这些从“构建”和“发布”中排除。这些文件夹的名称将与它们在 base_project 中的名称相同。
9.  回想一下，您已经创建了一个测试文件夹来存放您的测试。现在您可以在这个文件夹中创建您的测试源文件了，但是，您必须在这个文件夹中“包含”所有必要的文件夹。为此，右键单击测试文件夹，转到属性，在 C/C++构建下单击设置，找到 GCC C++编译器下的包含链接并单击它。包括 googleTestLib 和从 base_project 链接的文件夹。请确保您将这些内容包含在您的 unit_test 项目中，而不是 base_project 中。

所以我犯的错误是:

1.  我在 base_project 中有一个 main 函数，它会与测试源文件中的 main 函数冲突。我不得不完全排除或移除它。
2.  我没有从 base_project 中排除我链接到 unit_test 的文件夹，所以我得到了重新定义错误。
3.  我从构建中排除了带有 main 函数的测试源文件，所以我得到了一个 winmain@16 错误。我不知道我为什么这样做，然后我花了 2 个小时试图弄清楚为什么 eclipse 认为我需要一个 winmain 函数而不是 main :-D
4.  我没有包含我链接的文件夹，而是包含了基础项目中的文件夹。:-D
5.  我的一个文件的扩展名是. c，而不是。cpp(我知道！:-D)