# 连接 CGAL 和 qt-creator 问题

> 原文：<https://dev.to/nourhan_y_a/linking-cgal-with-qt-creator-proplem--576e>

0
向下投票
最喜欢的

我正在 windows 操作系统上使用 qt-CREATOR 和 VS c++编译器创建一个 QT 项目，

的。专业文件:

HEADERS = \
main window . h \
myqglwidget . h \
My _ CGAL _ config . h \
My _ Vertex _ base . h \
My _ half edge _ base . h \
My _ Face _ base . h \
My _ 多面体. h
SOURCES = \
viewer . cpp \
main window . CPP \
myqglwidget . CPP \
My _ 多面体. CPP

INCLUDEPATH +=。
include path+= C:/dev/CGAL-4.9/auxiliary/GMP/include
include path+= C:/dev/CGAL-4.9/include
include path+= C:/local/boost _ 1 _ 59 _ 0
include path+= C:/dev/libQGLViewer-2 . 7 . 1/QGLViewer

LIBS+=-LC:/dev/CGAL-4.9/lib
LIBS+=-LC:/local/boost _ 1 _ 59 _ 0/lib
LIBS+=-LC:/dev/libQGLViewer-2 . 7 . 1/QGLViewer-lqglviewer 2

qglviewer.h '，' config.h '分别在指定的文件夹(C:/dev/libQGLViewer-2 . 7 . 1/QGLViewer，C:\dev\CGAL-4.9\include\CGAL)中，所以我认为警告信息“构建目录需要与源目录在同一级别”中的问题。

编译输出消息:

构建/部署项目 dr _ mine(kit:Desktop Qt 5 . 7 . 1 msvc 2013 64 bit)时出错
kit Desktop Qt 5 . 7 . 1 msvc 2013 64 bit 存在配置问题，这可能是此问题的根本原因。
当执行步骤“Make”时

我很感激任何帮助，谢谢