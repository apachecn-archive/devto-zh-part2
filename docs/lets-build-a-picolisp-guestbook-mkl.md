# 让我们建立一个 picolisp 留言簿

> 原文：<https://dev.to/cess11/lets-build-a-picolisp-guestbook-mkl>

## 简介

这是我不久前为练习而写的一篇关于留言簿网络应用的短文。

它呈现一个表单，用于输入消息并将其发布到留言簿对象。

要运行它，你需要安装 picolisp，将下面的源代码保存为“gub.l ”,然后

```
$ pil gub.l +
: (new! '(+Gbk) 'nm "Your guestbook name here")
-> {2}
:(go)(wait) 
```

或者将最后一行替换为

```
:(bye)
$ pil gub.l -go -wait 
```

现在，您可以将 web 浏览器指向 localhost:8080。

## 操作指南

开始开发 web 应用程序的一个好地方是设计您的数据模型。对于留言簿，从松散的消息对象到包含用户、角色和子留言簿的复杂模式，几乎任何东西都可以在短时间内构思出来。

在这个例子中，我选择了介于最简单的松散消息和一个模型之间的一种方法，该模型使用一个对象将消息保存在一个字段的列表中，对它们进行命名，这样就可以很容易地替换留言簿，而不必一次删除所有消息。

```
(class +Msg +Entity)
(rel hdr (+String))
(rel bdy (+String))
(rel sdr (+String))
(rel dor (+String))
(rel book (+Joint) msgs (+Gbk))

(class +Gbk +Entity)
(rel nm (+Ref +String))
(rel msgs (+List +Joint) book (+Msg)) 
```

有了这样一个模型，我们可以通过调整以下代码行在 GUI 中公开某个'+Gbk 对象:

```
*Gbk (car (collect 'nm '+Gbk))

# for example, (db 'nm '+Gbk "Certain Name") 
```

一个更适合生产的方法是用一个浏览和选择可用留言簿的功能来代替它。

从 GUI 表单获取数据是通过将值保存在三个全局变量中，然后将它们作为方法参数传递给保存在' *Gbk '中的对象来完成的。

该方法如下所示:

```
(dm addM> (Hdr Bdy Sdr) 
    (let  
        D (new! '(+Msg) 
            'hdr Hdr # header
            'bdy Bdy # message body
            'sdr Sdr # sender
            'dor (stamp)
            'book This )
        (put!> This 'msgs D)
    (commit 'upd) )) 
```

有可能窝‘放！>和“新！但是读取和扩展会更困难，例如，如果新创建的对象应该被添加到数据库中的更多位置，那么使用“let”范围的符号来这样做通常比依赖隐式符号传递的嵌套更方便。

通常不直接使用“ht:print ”,它是 GUI 组件使用的一个相当低级的函数，但是它对于实验和学习(当然，还有新组件的构造)非常有用。在 pilog 表达式中使用'+Chart '更符合习惯，也是更好的实践，但是它也需要更多的代码，并且更难阅读。

```
 (for X (get *Gbk 'msgs) # one can use 'get 
                (with X # as well as 'with and the shortcuts 
                    (<h3> NIL (ht:Prin (: hdr)) )
                    (<br>)
                    (<p> NIL (ht:Prin (: bdy)) )
                    (<br>)
                    (<p> NIL (ht:Prin (pack (: sdr) " - " (: dor))) ) 
```

对于改进这个应用程序的下一步，用“url >或图表结构替换这个表示代码可能是一个好主意，或者用另一个表单预先考虑这个表单，让用户在可用的留言簿中进行选择，也许可以选择创建一个新的留言簿。

## 整个源代码

```
(load "@lib/http.l" "@lib/xhtml.l" "@lib/form.l")

# in a real application you would use 'allowed to restrict access to 
# certain directories and functions. 
# you would also put httpGate, nginx or somesuch between the Internet 
# and the port where your program is listening.

(class +Msg +Entity)
(rel hdr (+String))
(rel bdy (+String))
(rel sdr (+String))
(rel dor (+String))
(rel book (+Joint) msgs (+Gbk))

# here a TODO could be to write a method that outputs message contents

(class +Gbk +Entity)
(rel nm (+Ref +String))
(rel msgs (+List +Joint) book (+Msg))

(dm addM> (Hdr Bdy Sdr) 
    (let D (new! '(+Msg) 
        'hdr Hdr 
        'bdy Bdy
        'sdr Sdr 
        'dor (stamp)
        'book This )
        (put!> This 'msgs D)
    (commit 'upd) ))

(pool "gb.db")

(setq 
    *Gbk (car (collect 'nm '+Gbk)) # decide which guestbook we'll serve
     )

(de work ()
    (app) # sets up session handling
    (action # says there will be action in some web forms
        (html 0 "Guestbook" "@lib.css" NIL 
            (<h2> NIL "Picolisp guestbook")
            (<hr>)
            (form NIL 
        (<p> NIL "Message header:")
        (gui 'hdr '(+Var +TextField) '*MsgHdr 30)
                (do 2 (<br>))
        (<p> NIL "Message text:")
                (gui 'bdy '(+Var +TextField) '*MsgBdy 30 5)
                (do 2 (<br>))
        (<p> NIL "Your name:")
                (gui 'sdr '(+Var +TextField) '*MsgSdr 30)
                (do 2 (<br>))
                (gui '(+Button) "Send" 
                    '(ask "Post this message?" 
                        (addM> *Gbk *MsgHdr *MsgBdy *MsgSdr) )) )
            (<br>)
        (<hr>)
            (for X (get *Gbk 'msgs) # instead of '+Chart, '+QueryChart or 'url>
                (with X
                    (<h3> NIL (ht:Prin (: hdr)) )
                    (<br>)
                    (<p> NIL (ht:Prin (: bdy)) )
                    (<br>)
                    (<p> NIL (ht:Prin (pack (: sdr) " - " (: dor))) )
            (<br>) )) )))

(de go ()
    (server 8080 "!work") ) 
```

## 结束

如果你认为这值得一读，可以考虑捐赠一两杯咖啡。