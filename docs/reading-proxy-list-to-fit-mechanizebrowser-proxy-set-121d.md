# 读取代理列表以适应 Mechanize。浏览器()

> 原文：<https://dev.to/lyjoker/reading-proxy-list-to-fit-mechanizebrowser-proxy-set-121d>

# [T1】！/usr/bin/python](#usrbinpython)

导入随机
导入 yaml

banner = " " " "
从文件中读取代理列表
Formated->Socks(4-5)\ HTTP \ HTTPS:IP:PORT

示例:
cat proxylist.txt

http:1 . 1 . 1 . 1:8080
https:2 . 2 . 2 . 2:9090

" " "打印(横幅)

proxyfilepath = str(raw_input("输入代理列表路径，
，内容格式为 Socks(4-5)\HTTP\HTTPS:IP:PORT)此处: "))

proxy _ dict = { }
x = open(proxy file path，' r ')。读取行()

plines = len(x)
print "文件中的代理数量为:%s " % plines

x:
proxy _ num+= 1
proxy _ type，proxy_server，proxy_port = line.strip("\n ")。split(":)
proxy _ dict[proxy _ num]= " { "+" ' "+proxy _ type+" ' "+':'+" ' "+proxy _ server+':'+proxy _ port+" ' "+" } "

print "\n 显示存储在 Proxy_dict 中的 Dict 格式的代理行\n"
print proxy_dict

计数器= 1

aplst= []

while counter<= plines:
sign = proxy _ dict[(counter)]
counter+= 1
aplst . append(sign)

print "\n\n 完成签名\n\n"
print "显示字典格式代理列表\ n "
print aplist

打印" \n\n 显示列表中的随机选择及其数据类型..\ n "
print(random . choice(aplst))、type(random . choice(aplst))
print
print " \ n 因此我们应该使用(yaml)转换令牌代理，以通过协议正确映射\ n "
converted = YAML . load((random . choice(aplst)))
print converted，type(converted)

* * *

-这个命令(random.choice(aplst))可以在
Mechanize 内部使用。以(yaml)为例，将列表中的字符串形式的“代理”令牌转换为“Dict”后的 Browser()代理选项:

pr _ token = YAML . load((random . choice(aplst)))
br = mechanize。browser()
br . set _ proxy(pr _ token)

# LyJoker 迎向达。掌握