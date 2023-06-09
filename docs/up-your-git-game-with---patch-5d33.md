# 使用- patch 升级您的 git 游戏

> 原文：<https://dev.to/thejessleigh/up-your-git-game-with---patch-5d33>

我们都努力追求干净的、单一目的的承诺和有意义的信息。如果自上次提交以来已经进行了大量调试，这在实践中可能会很困难。我认识的很多人在开发和维护特性的时候都会用到`git commit -A`或者`git commit .`。这在进行小的更改时是没问题的，但是我通常不喜欢在提交多于一两行代码时使用这些选项。当我建议使用我最喜欢的 git add 选项时，许多人告诉我他们从未听说过或使用过它。

# `git add --patch`

`git add --patch`(或`git add -p`)允许您在相关块中暂存更改，而不是暂存您最近的所有更改。它弹出一个[交互式菜单](https://git-scm.com/docs/git-add#_interactive_mode)，允许对阶段变更进行粒度控制。交互式界面最初向您展示的是`git diff`输出的相同代码块。当你在交互菜单中看到每一个变化时，你可以选择`y`来展示大块，选择`n`来跳过，或者选择`s`来将大块分割成更小的块。

## 举例

假设我开始了一个新项目，我想要一个基本的用户类:

```
class User:
    """User class for my app"""

    def __init__(self, fist_name, last_name, role):
        self.first_name = fist_name
        self.last_name = last_name
        self.role = role

    def display_name(self):
        print("{} {}".format(self.fist_name, self.last_name))

    def display_role(self):
        print("User is a {}".format(self.role)) 
```

在我提交了这段代码之后，我注意到有几处我想修改。

首先，我意识到我已经把“First”拼写成“fist”很多次了，这需要纠正。其次，我意识到我不喜欢让“role”成为我的类中的一个字符串变量。我宁愿让不同的用户角色成为 user 的子类。所以我把文件修改成这样。

```
class User:
    """Base User class for my app"""

    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name

    def display_name(self):
        print("{} {}".format(self.first_name, self.last_name))

class Admin(User):
    'Admin level user'

    def __init__(self, first_name, last_name, email):
        super(Admin, self).__init__(first_name, last_name)
        self.email = email

class Guest(User):
    'Guest user'

    def display_name(self):
        print("Guest {} {}".format(self.first_name, self.last_name)) 
```

这是我想要捕捉的两个独立的想法，所以我应该在独立的提交中保存这些更改。使用`git add -p`使这变得容易，即使两个独立的想法出现在同一个文件中。

当我输入`git add -p`时，我看到了以下内容。

```
diff --git a/user.py b/user.py
index e0bb61a..1b59b32 100644
--- a/user.py
+++ b/user.py
@@ -1,13 +1,24 @@
class User:
-    """User class for my app"""
+    """Base User class for my app"""

-    def __init__(self, fist_name, last_name, role):
-        self.first_name = fist_name
+    def __init__(self, first_name, last_name):
+        self.first_name = first_name
         self.last_name = last_name
-        self.role = role

     def display_name(self):
-        print("{} {}".format(fist_name, last_name))
+        print("{} {}".format(self.first_name, self.last_name))

-    def display_role(self):
-        print "User is a {}".format(role)
+
+class Admin(User):
+   """Admin level user"""
+
+   def __init__(self, first_name, last_name, email):
+       super(Admin, self).__init__(first_name, last_name)
+       self.email = email
+
+
+class Guest(User):
+   """Guest user"""
+
+   def display_name(self):
+       print("Guest {} {}".format(self.first_name, self.last_name))
Stage this hunk [y,n,q,a,d,/,s,e,?]? 
```

由于我的`user.py`文件相对较短，所以它看起来是一大块。我可以通过选择`s`选项将其分离出来。

```
Stage this hunk [y,n,q,a,d,/,s,e,?]? s
Split into 5 hunks.
@@ -1,3 +1,3 @@
class User:
-    """User class for my app"""
+    """Base User class for my app"""

Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? n
@@ -3,4 +3,4 @@

-    def __init__(self, fist_name, last_name, role):
-        self.first_name = fist_name
+    def __init__(self, first_name, last_name):
+        self.first_name = first_name
         self.last_name = last_name
Stage this hunk [y,n,q,a,d,/,K,j,J,g,e,?]? e
@@ -6,4 +6,3 @@
         self.last_name = last_name
-        self.role = role

     def display_name(self):
Stage this hunk [y,n,q,a,d,/,K,j,J,g,e,?]? n
@@ -8,4 +7,4 @@

     def display_name(self):
-        print("{} {}".format(fist_name, last_name))
+        print("{} {}".format(self.first_name, self.last_name))

Stage this hunk [y,n,q,a,d,/,K,j,J,g,e,?]? y
@@ -11,3 +10,15 @@

-    def display_role(self):
-        print("User is a {}".format(role))
+
+class Admin(User):
+   """Admin level user"""
+
+   def __init__(self, first_name, last_name, email):
+       super(Admin, self).__init__(first_name, last_name)
+       self.email = email
+
+
+class Guest(User):
+   """Guest user"""
+
+   def display_name(self):
+       print("Guest {} {}".format(self.first_name, self.last_name))

Stage this hunk [y,n,q,a,d,/,K,g,e,?]? n 
```

```
git commit -m "Fix typos: 'fist' -> 'first'"
[master f91e0eb] Fix typos: 'fist' -> 'first'
 1 file changed, 3 insertions(+), 3 deletions(-) 
```

通过将这个文件的 diff 分成 5 个不同的块，我能够只选择与修复`fist`->-`first`错别字相关的更改，而不会影响代码的功能。

您可能会注意到，在`User.__init__()`方法中有两个不同的变化，我不希望它们出现在同一个提交中。首先是纠正`fist -> first`错别字。第二个是从函数声明中移除`role`参数。即使使用`s`排除变更也不能解决这个问题，因为变更在同一行。

你会注意到，我没有使用`y`、`n`或`s`，而是使用了`e`选项。`e`选项让您进入默认的终端编辑器(可能是 vim，除非您将它改成了其他的东西)，在这里您可以手动编辑文件以反映您想要进行的更改。保存文件并退出，您可以看到用`git diff --cached`提交的修改。

在这个例子中，我修改了这个模块

```
-    def __init__(self, fist_name, last_name, role):
-        self.first_name = fist_name
+    def __init__(self, first_name, last_name):
+        self.first_name = first_name 
```

提交前的样子

```
- def __init__(self, fist_name, last_name, role):
-     self.first_name = fist_name
+ def __init__(self, first_name, last_name, role):
+     self.first_name = first_name 
```

我可以再次使用`git add -p`来检查下一次提交的更改，或者我可以使用`git add user.py`,因为我知道无论如何我都要在文件中暂存所有剩余的更改。为了完成这个例子，我将再次使用`git add -p`。

```
diff --git a/user.py b/user.py
index 2a09e35..1b59b32 100644
--- a/user.py
+++ b/user.py
@@ -1,13 +1,24 @@
class User:
-    """User class for my app"""
+    """Base User class for my app"""

-     def __init__(self, first_name, last_name, role):
+     def __init__(self, first_name, last_name):
         self.first_name = first_name
         self.last_name = last_name
-        self.role = role

     def display_name(self):
         print("{} {}".format(self.first_name, self.last_name))

-    def display_role(self):
-        print("User is a {}".format(role))
+
+class Admin(User):
+   """Admin level user"""
+
+   def __init__(self, first_name, last_name, email):
+       super(Admin, self).__init__(first_name, last_name)
+       self.email = email
+
+
+class Guest(User):
+   """Guest user"""
+
+   def display_name(self):
+       print("Guest {} {}".format(self.first_name, self.last_name))
Stage this hunk [y,n,q,a,d,/,s,e,?]? y 
```

```
git commit -m "Break user roles out into different classes."
[master dea4c79] Break user roles out into different classes.
 1 file changed, 16 insertions(+), 5 deletions(-) 
```

当我回头看我的`git log`时，我看到两个独立的、简洁的提交。如果我决定需要恢复将`User.role`分成`Admin`和`Guest`类的提交，我不会在这个过程中丢失打字错误修复。

我使用这个选项将我的所有更改提交，即使它们只有一两行。它帮助我抓住错别字。我从来不需要担心将拼写错误修复或去林挺与重要的功能变化混淆。这也有助于我保持我的想法是独立的，即使我同时发展了那些想法。