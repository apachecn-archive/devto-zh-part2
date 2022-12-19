# Qemu KVM Centos 7.0 安装-构建 tmux 2.6

> 原文：<https://dev.to/psnebc/qemu-kvm-centos-70-install---build-tmux-26--2f4h>

```
 58  wget https://github.com/tmux/tmux/releases/download/2.6/tmux-2.6.tar.gz
   59  tar xzf tmux-2.6.tar.gz
   60  cd tmux-2.6
   61  ./configure && make
# Install it first 
   62  sudo yum install libevent-devel
   64  sudo yum install ncurses-devel
   65  sudo yum install glibc-static
# END  
   67  ./configure && make
   68  sudo yum install gcc
   69  ./configure && make

   74  sudo make install

   78  ps -u | grep tmux
   79  tmux -V 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  tmux-2.6 sudo make install
make[1]: Entering directory `/home/psnebc/tmux-2.6'
 /usr/bin/mkdir -p '/usr/local/bin'
  /usr/bin/install -c tmux '/usr/local/bin'
make  install-exec-hook
make[2]: Entering directory `/home/psnebc/tmux-2.6'
if test xmdoc = xmdoc; then \
        sed -e "s|@SYSCONFDIR@|/etc|g" ./tmux.1 \
                >./tmux.1.mdoc; \
else \
        sed -e "s|@SYSCONFDIR@|/etc|g" ./tmux.1| \
                gawk -f./mdoc2man.awk >./tmux.1.man; \
fi
/usr/bin/mkdir -p /usr/local/share/man/man1
/usr/bin/install -c -m 644 ./tmux.1.mdoc \
        /usr/local/share/man/man1/tmux.1
make[2]: Leaving directory `/home/psnebc/tmux-2.6'
make[1]: Für das Ziel »install-data-am« ist nichts zu tun.
make[1]: Leaving directory `/home/psnebc/tmux-2.6'
➜  tmux-2.6 tmux --version
usage: tmux [-2CluvV] [-c shell-command] [-f file] [-L socket-name]
            [-S socket-path] [command [flags]]
➜  tmux-2.6 tmux -v
[exited]
➜  tmux-2.6 ps -u | grep tmux
psnebc    5786  0.0  0.0 112672   984 pts/0    S+   18:21   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn tmux
➜  tmux-2.6 tmux -V
tmux 2.6 
```

Enter fullscreen mode Exit fullscreen mode