# 在 Fedora 27 (curl)上安装节点版本管理器

> 原文：<https://dev.to/psnebc/install-node-version-manager-on-fedora-27-curl-54ce>

```
psnebc@localhost  ~  curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 12862  100 12862    0     0  12862      0  0:00:01 --:--:--  0:00:01 42448
=> Downloading nvm from git to '/home/psnebc/.nvm'
=> Klone nach '/home/psnebc/.nvm' ...
remote: Counting objects: 264, done.
remote: Compressing objects: 100% (229/229), done.
remote: Total 264 (delta 31), reused 106 (delta 25), pack-reused 0
Empfange Objekte: 100% (264/264), 116.46 KiB | 4.02 MiB/s, Fertig.
Löse Unterschiede auf: 100% (31/31), Fertig.
=> Compressing and cleaning up git repository

=> Appending nvm source string to /home/psnebc/.zshrc
=> Appending bash_completion source string to /home/psnebc/.zshrc
npm ERR! invalid: minizlib@1.0.3 /usr/lib/node_modules/npm/node_modules/tar/node_modules/minizlib
=> You currently have modules installed globally with `npm`. These will no
=> longer be linked to the active version of Node when you install a new node
=> with `nvm`; and they may (depending on how you construct your `$PATH`)
=> override the binaries of modules installed with `nvm`:

/usr/lib
├── localtunnel@1.8.3
=> If you wish to uninstall them at a later point (or re-install them under your
=> `nvm` Nodes), you can remove them from the system Node as follows:

     $ nvm use system
     $ npm uninstall -g a_module

=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
 psnebc@localhost  ~  source ~/.bashrc

 psnebc@localhost  ~  source .bashrc  
 psnebc@localhost  ~  source .zshrc 
 psnebc@localhost  ~  nvm current                                                                   
system
 psnebc@localhost  ~  nvm ls-remote
        v0.1.14
        v0.1.15
        v0.1.16
        v0.1.17
        v0.1.18
        v0.1.19
        v0.1.20
        v0.1.21
        v0.1.22
        v0.1.23
        v0.1.24
        v0.1.25
        v0.1.26
        v0.1.27
        v0.1.28
        v0.1.29
        v0.1.30
        v0.1.31
        v0.1.32
        v0.1.33
        v0.1.90
        v0.1.91
        v0.1.92
        v0.1.93
        v0.1.94
        v0.1.95
        v0.1.96
        v0.1.97
        v0.1.98
        v0.1.99
       v0.1.100
       v0.1.101
       v0.1.102
       v0.1.103
       v0.1.104
         v0.2.0
         v0.2.1
         v0.2.2
         v0.2.3
         v0.2.4
         v0.2.5
         v0.2.6
         v0.3.0
         v0.3.1
         v0.3.2
         v0.3.3
         v0.3.4
         v0.3.5
         v0.3.6
         v0.3.7
         v0.3.8
         v0.4.0
         v0.4.1
         v0.4.2
         v0.4.3
         v0.4.4
         v0.4.5
         v0.4.6
         v0.4.7
         v0.4.8
         v0.4.9
        v0.4.10
        v0.4.11
        v0.4.12
         v0.5.0
         v0.5.1
         v0.5.2
         v0.5.3
         v0.5.4
         v0.5.5
         v0.5.6
         v0.5.7
         v0.5.8
         v0.5.9
        v0.5.10
         v0.6.0
         v0.6.1
         v0.6.2
         v0.6.3
         v0.6.4
         v0.6.5
         v0.6.6
         v0.6.7
         v0.6.8
         v0.6.9
        v0.6.10
        v0.6.11
        v0.6.12
        v0.6.13
        v0.6.14
        v0.6.15
        v0.6.16
        v0.6.17
        v0.6.18
        v0.6.19
        v0.6.20
        v0.6.21
         v0.7.0
         v0.7.1
         v0.7.2
         v0.7.3
         v0.7.4
         v0.7.5
         v0.7.6
         v0.7.7
         v0.7.8
         v0.7.9
        v0.7.10
        v0.7.11
        v0.7.12
         v0.8.0
         v0.8.1
         v0.8.2
         v0.8.3
         v0.8.4
         v0.8.5
         v0.8.6
         v0.8.7
         v0.8.8
         v0.8.9
        v0.8.10
        v0.8.11
        v0.8.12
        v0.8.13
        v0.8.14
        v0.8.15
        v0.8.16
        v0.8.17
        v0.8.18
        v0.8.19
        v0.8.20
        v0.8.21
        v0.8.22
        v0.8.23
        v0.8.24
        v0.8.25
        v0.8.26
        v0.8.27
        v0.8.28
         v0.9.0
         v0.9.1
         v0.9.2
         v0.9.3
         v0.9.4
         v0.9.5
         v0.9.6
         v0.9.7
         v0.9.8
         v0.9.9
        v0.9.10
        v0.9.11
        v0.9.12
        v0.10.0
        v0.10.1
        v0.10.2
        v0.10.3
        v0.10.4
        v0.10.5
        v0.10.6
        v0.10.7
        v0.10.8
        v0.10.9
       v0.10.10
       v0.10.11
       v0.10.12
       v0.10.13
       v0.10.14
       v0.10.15
       v0.10.16
       v0.10.17
       v0.10.18
       v0.10.19
       v0.10.20
       v0.10.21
       v0.10.22
       v0.10.23
       v0.10.24
       v0.10.25
       v0.10.26
       v0.10.27
       v0.10.28
       v0.10.29
       v0.10.30
       v0.10.31
       v0.10.32
       v0.10.33
       v0.10.34
       v0.10.35
       v0.10.36
       v0.10.37
       v0.10.38
       v0.10.39
       v0.10.40
       v0.10.41
       v0.10.42
       v0.10.43
       v0.10.44
       v0.10.45
       v0.10.46
       v0.10.47
       v0.10.48
        v0.11.0
        v0.11.1
        v0.11.2
        v0.11.3
        v0.11.4
        v0.11.5
        v0.11.6
        v0.11.7
        v0.11.8
        v0.11.9
       v0.11.10
       v0.11.11
       v0.11.12
       v0.11.13
       v0.11.14
       v0.11.15
       v0.11.16
        v0.12.0
        v0.12.1
        v0.12.2
        v0.12.3
        v0.12.4
        v0.12.5
        v0.12.6
        v0.12.7
        v0.12.8
        v0.12.9
       v0.12.10
       v0.12.11
       v0.12.12
       v0.12.13
       v0.12.14
       v0.12.15
       v0.12.16
       v0.12.17
       v0.12.18
    iojs-v1.0.0
    iojs-v1.0.1
    iojs-v1.0.2
    iojs-v1.0.3
    iojs-v1.0.4
    iojs-v1.1.0
    iojs-v1.2.0
    iojs-v1.3.0
    iojs-v1.4.1
    iojs-v1.4.2
    iojs-v1.4.3
    iojs-v1.5.0
    iojs-v1.5.1
    iojs-v1.6.0
    iojs-v1.6.1
    iojs-v1.6.2
    iojs-v1.6.3
    iojs-v1.6.4
    iojs-v1.7.1
    iojs-v1.8.1
    iojs-v1.8.2
    iojs-v1.8.3
    iojs-v1.8.4
    iojs-v2.0.0
    iojs-v2.0.1
    iojs-v2.0.2
    iojs-v2.1.0
    iojs-v2.2.0
    iojs-v2.2.1
    iojs-v2.3.0
    iojs-v2.3.1
    iojs-v2.3.2
    iojs-v2.3.3
    iojs-v2.3.4
    iojs-v2.4.0
    iojs-v2.5.0
    iojs-v3.0.0
    iojs-v3.1.0
    iojs-v3.2.0
    iojs-v3.3.0
    iojs-v3.3.1
         v4.0.0
         v4.1.0
         v4.1.1
         v4.1.2
         v4.2.0   (LTS: Argon)
         v4.2.1   (LTS: Argon)
         v4.2.2   (LTS: Argon)
         v4.2.3   (LTS: Argon)
         v4.2.4   (LTS: Argon)
         v4.2.5   (LTS: Argon)
         v4.2.6   (LTS: Argon)
         v4.3.0   (LTS: Argon)
         v4.3.1   (LTS: Argon)
         v4.3.2   (LTS: Argon)
         v4.4.0   (LTS: Argon)
         v4.4.1   (LTS: Argon)
         v4.4.2   (LTS: Argon)
         v4.4.3   (LTS: Argon)
         v4.4.4   (LTS: Argon)
         v4.4.5   (LTS: Argon)
         v4.4.6   (LTS: Argon)
         v4.4.7   (LTS: Argon)
         v4.5.0   (LTS: Argon)
         v4.6.0   (LTS: Argon)
         v4.6.1   (LTS: Argon)
         v4.6.2   (LTS: Argon)
         v4.7.0   (LTS: Argon)
         v4.7.1   (LTS: Argon)
         v4.7.2   (LTS: Argon)
         v4.7.3   (LTS: Argon)
         v4.8.0   (LTS: Argon)
         v4.8.1   (LTS: Argon)
         v4.8.2   (LTS: Argon)
         v4.8.3   (LTS: Argon)
         v4.8.4   (LTS: Argon)
         v4.8.5   (LTS: Argon)
         v4.8.6   (LTS: Argon)
         v4.8.7   (LTS: Argon)
         v4.9.0   (LTS: Argon)
         v4.9.1   (Latest LTS: Argon)
         v5.0.0
         v5.1.0
         v5.1.1
         v5.2.0
         v5.3.0
         v5.4.0
         v5.4.1
         v5.5.0
         v5.6.0
         v5.7.0
         v5.7.1
         v5.8.0
         v5.9.0
         v5.9.1
        v5.10.0
        v5.10.1
        v5.11.0
        v5.11.1
        v5.12.0
         v6.0.0
         v6.1.0
         v6.2.0
         v6.2.1
         v6.2.2
         v6.3.0
         v6.3.1
         v6.4.0
         v6.5.0
         v6.6.0
         v6.7.0
         v6.8.0
         v6.8.1
         v6.9.0   (LTS: Boron)
         v6.9.1   (LTS: Boron)
         v6.9.2   (LTS: Boron)
         v6.9.3   (LTS: Boron)
         v6.9.4   (LTS: Boron)
         v6.9.5   (LTS: Boron)
        v6.10.0   (LTS: Boron)
        v6.10.1   (LTS: Boron)
        v6.10.2   (LTS: Boron)
        v6.10.3   (LTS: Boron)
        v6.11.0   (LTS: Boron)
        v6.11.1   (LTS: Boron)
        v6.11.2   (LTS: Boron)
        v6.11.3   (LTS: Boron)
        v6.11.4   (LTS: Boron)
        v6.11.5   (LTS: Boron)
        v6.12.0   (LTS: Boron)
        v6.12.1   (LTS: Boron)
        v6.12.2   (LTS: Boron)
        v6.12.3   (LTS: Boron)
        v6.13.0   (LTS: Boron)
        v6.13.1   (LTS: Boron)
        v6.14.0   (LTS: Boron)
        v6.14.1   (Latest LTS: Boron)
         v7.0.0
         v7.1.0
         v7.2.0
         v7.2.1
         v7.3.0
         v7.4.0
         v7.5.0
         v7.6.0
         v7.7.0
         v7.7.1
         v7.7.2
         v7.7.3
         v7.7.4
         v7.8.0
         v7.9.0
        v7.10.0
        v7.10.1
         v8.0.0
         v8.1.0
         v8.1.1
         v8.1.2
         v8.1.3
         v8.1.4
         v8.2.0
         v8.2.1
         v8.3.0
         v8.4.0
         v8.5.0
         v8.6.0
         v8.7.0
         v8.8.0
         v8.8.1
         v8.9.0   (LTS: Carbon)
         v8.9.1   (LTS: Carbon)
         v8.9.2   (LTS: Carbon)
         v8.9.3   (LTS: Carbon)
         v8.9.4   (LTS: Carbon)
        v8.10.0   (LTS: Carbon)
        v8.11.0   (LTS: Carbon)
        v8.11.1   (Latest LTS: Carbon)
         v9.0.0
         v9.1.0
         v9.2.0
         v9.2.1
         v9.3.0
         v9.4.0
         v9.5.0
         v9.6.0
         v9.6.1
         v9.7.0
         v9.7.1
         v9.8.0
         v9.9.0
        v9.10.0
        v9.10.1
 psnebc@ 
```

Enter fullscreen mode Exit fullscreen mode