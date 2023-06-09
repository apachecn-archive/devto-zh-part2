# 用 Bash 在 Ubuntu 上切换 JVM

> 原文：<https://dev.to/ikysil/switching-jvm-on-ubuntu-with-bash-463h>

只需在您的`.bash_aliases`中添加以下内容:

```
alias resetjdkpath='
    PATH=`echo $PATH | sed -Ee 's/[/]usr[/]lib[/]jvm[^:]+//g' | sed -Ee 's/:+/:/g'`
'

alias orjdk7='
    resetjdkpath
    JAVA_HOME=/usr/lib/jvm/java-7-oracle
    J2SDKDIR=${JAVA_HOME}
    J2REDIR=${JAVA_HOME}/jre
    PATH=${JAVA_HOME}/bin:${JAVA_HOME}/db/bin:${JAVA_HOME}/jre/bin:$PATH
    DERBY_HOME=${JAVA_HOME}/db
    java -version
'

alias orjdk8='
    resetjdkpath
    JAVA_HOME=/usr/lib/jvm/java-8-oracle
    J2SDKDIR=${JAVA_HOME}
    J2REDIR=${JAVA_HOME}/jre
    PATH=${JAVA_HOME}/bin:${JAVA_HOME}/db/bin:${JAVA_HOME}/jre/bin:$PATH
    DERBY_HOME=${JAVA_HOME}/db
    java -version
'

alias orjdk9='
    resetjdkpath
    JAVA_HOME=/usr/lib/jvm/java-9-oracle
    J2SDKDIR=${JAVA_HOME}
    J2REDIR=${JAVA_HOME}/jre
    PATH=${JAVA_HOME}/bin:${JAVA_HOME}/db/bin:${JAVA_HOME}/jre/bin:$PATH
    DERBY_HOME=${JAVA_HOME}/db
    java -version
'

alias opjdk8='
    resetjdkpath
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    J2SDKDIR=${JAVA_HOME}
    J2REDIR=${JAVA_HOME}/jre
    PATH=${JAVA_HOME}/bin:${JAVA_HOME}/db/bin:${JAVA_HOME}/jre/bin:$PATH
    DERBY_HOME=${JAVA_HOME}/db
    java -version
' 
```

Enter fullscreen mode Exit fullscreen mode