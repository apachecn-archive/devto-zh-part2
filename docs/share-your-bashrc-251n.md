# 分享你的。bashrc

> 原文：<https://dev.to/mmphego/share-your-bashrc-251n>

不知道以前有没有这样做过，但是你愿意分享你的 bashrc 命令吗？

下面我列出了我所有的 bashrc
Note mac 用户，我运行的是 Ubuntu 18.04

`cat ~/.bashrc`

```
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# Define a few Colours
BLACK='\e[0;30m'
BLUE='\e[0;34m'
GREEN='\e[0;32m'
CYAN='\e[0;36m'
RED='\e[0;31m'
PURPLE='\e[0;35m'
BROWN='\e[0;33m'
LIGHTGRAY='\e[0;37m'
DARKGRAY='\e[1;30m'
LIGHTBLUE='\e[1;34m'
LIGHTGREEN='\e[1;32m'
LIGHTCYAN='\e[1;36m'
LIGHTRED='\e[1;31m'
LIGHTPURPLE='\e[1;35m'
YELLOW='\e[1;33m'
WHITE='\e[1;37m'
NC='\e[0m' # No Color

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=9999999999999000000
HISTFILESIZE=900000000999999999
PROMPT_COMMAND="history -a"
export HISTSIZE PROMPT_COMMAND

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
# case "$TERM" in
#     xterm-color) color_prompt=yes;;
# esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
# force_color_prompt=yes

# if [ -n "$force_color_prompt" ]; then
#     if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
#   # We have color support; assume it's compliant with Ecma-48
#   # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
#   # a case would tend to support setf rather than setaf.)
#   color_prompt=yes
#     else
#   color_prompt=
#     fi
# fi

# if [ "$color_prompt" = yes ]; then
#     PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
#     #PS1="\u@\h:\w\\$ \[$(tput sgr0)\]"
# else
#     PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
# fi
# unset color_prompt force_color_prompt

# # If this is an xterm set the title to user@host:dir
# case "$TERM" in
# xterm*|rxvt*)
#     PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
#     ;;
# *)
#     ;;
# esac

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# functions definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_functions, instead of adding them here directly.

if [ -f ~/.bash_functions ]; then
    . ~/.bash_functions
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
 if [ -f /usr/share/bash-completion/bash_completion ]; then
   . /usr/share/bash-completion/bash_completion
 elif [ -f /etc/bash_completion ]; then
   . /etc/bash_completion
 fi
fi

if [ -f /etc/bash_completion.d/git-prompt ]; then
    . /etc/bash_completion.d/git-prompt
fi

if [ -f /var/run/reboot-required ]; then printf "$(tput smso)$(tput setaf 1)[*** Hello ${USER}, you must reboot your machine ***]$(tput sgr0)\n";
fi

if [[ $- == *i* ]]; then bind '"\e[A": history-search-backward'
    bind '"\e[B": history-search-forward'
    bind '"\e[1;5C": forward-word'
    bind '"\e[1;5D": backward-word'
    bind '"\e[5C": forward-word'
    bind '"\e[5D": backward-word'
    bind '"\e\e[C": forward-word'
    bind '"\e\e[D": backward-word'
    bind 'set show-all-if-ambiguous on'
    bind 'set completion-ignore-case on'
fi hash -r
export PATH=$PATH:$HOME/bin

########### Welcome Message ###########
IP_ADD=`ip addr | grep -w inet | gawk '{if (NR==2) {$0=$2; gsub(/\//," "); print $1;}}'`
printf "${LIGHTGREEN}Hello, $USER@${IP_ADD}\n"
printf "Today is, $(date)\n";
printf "Sysinfo: $(uptime)${NC}\n" 
```

Enter fullscreen mode Exit fullscreen mode

我把我所有的别名都移到了一个不同的文件
`cat .bash_aliases`

```
 # enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'
    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias lh='ls -lh'
# Network Start, Stop, and Restart
alias light='xbacklight -set'

# Apt
alias update='sudo apt -y update'
alias upgrade='sudo apt-get -y update && sudo apt-get -y --allow-unauthenticated upgrade && sudo apt-get autoclean && sudo apt-get autoremove && exit 0'
alias search='sudo apt search'
alias links='links2'
# Install and Remove Packages
alias install='sudo apt-get -y install'
alias uninstall='sudo apt-get --purge autoremove '
alias search-installed='sudo dpkg --get-selections '
alias upgrade-pips='sudo pip freeze --local | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 sudo pip install -U
'
#alias cleanPC='sudo apt-get -y autoclean && sudo apt-get -y clean && sudo apt-get -y autoremove'

alias lsdir='ls -ld */'
alias display='eog -w'
alias emptyDir='find . -empty -type d -delete'
alias meng='cd ${HOME}/Dropbox/MEng_Stuff/MEng-Progress'
alias media='sshfs -o reconnect media@192.168.1.10:/mnt /home/"${USER}"/mnt/media_srv'
alias reboot='sudo shutdown -r now'
alias shutdown='sudo shutdown -h now'
alias paux='ps aux | grep'
alias cd.='cd ..'
alias ..='cd ..'
alias ...='cd ../../../'
alias ....='cd ../../../../'
alias .....='cd ../../../../'
alias .4='cd ../../../../'
alias .5='cd ../../../../..'
alias cd..='cd ..'
alias youtube="youtube-dl"

# Useful Alias
# Add an "alert" alias for long running commands.  Use like so:
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

alias killfirefox="pkill -9 firefox"
alias killslack="pkill -9 slack"
alias CD='cd'

# Log into to Server 
```

Enter fullscreen mode Exit fullscreen mode

我把我所有的功能转移到一个不同的文件
`cat .bash_functions`

```
 # Useful Function
function commiter() {
    # Add file, commit and push
    git add -f "$1";
    if [ "$2" == "" ]; then git commit -m"Updated $1";
    else git commit -m"$2";
    fi;
    $(git push -q >> /dev/null 2>&1) &
    }

function ssh() {
    # Always ssh with -X
    command ssh -X "$@"
}

function rsync(){
    command rsync --progress "$@"
}

function extract () {
     if [ -f $1 ] ; then
         case $1 in
             *.tar.bz2)   tar xjf $1    ;;
             *.tar.gz)    tar xzf $1    ;;
             *.bz2)       bunzip2 $1    ;;
             *.rar)       rar x $1      ;;
             *.gz)        gunzip $1     ;;
             *.tar)       tar xf $1     ;;
             *.tbz2)      tar xjf $1    ;;
             *.tgz)       tar xzf $1    ;;
             *.zip)       unzip $1      ;;
             *.Z)         uncompress $1 ;;
             *.7z)        7z x $1       ;;
             *)           echo "'$1' cannot be extracted via extract()" ;;
         esac
     else echo "'$1' is not a valid file"
     fi
}

function psgrep() {
    if [ ! -z $1 ] ; then echo "Grepping for processes matching $1..."
        ps aux | grep $1 | grep -v grep else echo "!! Need name to grep for"
    fi
}

#source $HOME/.opt/Xilinx/Vivado/2017.2/settings64.sh
#source $HOME/.opt/Vivado/2018.1/settings64.sh

#export YOCTODIR=$HOME/Documents/Xilinx/EmbeddedLinux/Yocto/poky
#export PETADIR=$HOME/Documents/Xilinx/EmbeddedLinux/Petalinux
function cd {
    # The 'builtin' keyword allows you to redefine a Bash builtin without
    # creating a recursion. Quoting the parameter makes it work in case there are spaces in
    # directory names.
    builtin cd "$@"
#    if [ "$PWD" == "$YOCTODIR" ] ;
#        then
#            bash $YOCTODIR/.source_yocto
#    elif [ "$PWD" == "$PETADIR" ] ;
#        then
#            bash $PETADIR/.source_petalinux
#    else
        ls -thor ;
#    fi
}

function pip() {
    if [[ "$1" == "install" ]]; then shift 1
        command pip install --no-cache-dir -U "$@"
    else command pip "$@"
    fi
}

#function sudo() {
#  if [[ "$1" == "su" ]]; then
#     shift 1
#     printf "$(tput smso)$(tput setaf 1) [*** With great power comes great responsibility ***] $(tput sgr0)\n\n\n"
#     command sudo bash -l
#  else
#     command sudo "$@"
#  fi
#}

git_branch () {
    # Get current Git branch
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
    }

git_last_update () {
    # Get last update on current Git branch
    git log 2> /dev/null | head -n 3 | grep ^Date | cut -f4- -d' ';
    }

export PS1="\[\033[0;32m\]\[\033[0m\033[0;38m\]\u\[\033[0;36m\]@\[\033[0;36m\]\h:\w\[\033[0;32m\]  \$(git_branch) \$(git_last_update)\n\[\033[0;32m\]└─\[\033[0m\033[0;31m\] [\D{%F %T}] \$\[\033[0m\033[0;32m\] >>>\[\033[0m\] "

function disconnect() {
    # Disconnect all mounted disks
    for DIR in $(ls "${HOME}/mnt"); do
        /bin/fusermount -u -z "${DIR}" || true done
    }

function connectSSHFS(){
    if timeout 2 ping -c 1 -W 2 192.168.4.23 &> /dev/null; then timeout 2 sshfs -o reconnect,ServerAliveInterval=5,ServerAliveCountMax=5 \
        192.168.4.23:/ /home/"${USER}"/mnt/dbelab04 &>/dev/null ;
    else fusermount -u -z ~/mnt/dbelab04 ;
    fi
}
function dbelab06mount(){
    if timeout 2 ping -c 1 -W 2 192.168.4.14 &> /dev/null; then timeout 2 sshfs -o reconnect,ServerAliveInterval=5,ServerAliveCountMax=5 \
        192.168.4.14:/ /home/"${USER}"/mnt/dbelab06 &>/dev/null ;
    else fusermount -u -z ~/mnt/dbelab06 ;
    fi
}
function cmc2mount(){
    if timeout 2 ping -c 1 -W 2 10.103.254.3 &> /dev/null; then timeout 2 sshfs -o reconnect,ServerAliveInterval=15,ServerAliveCountMax=3 \
        "${USER}"@10.103.254.3:/ /home/"${USER}"/mnt/cmc2 &>/dev/null
    else fusermount -u -z ~/mnt/cmc2 ;
    fi
}
function cmc3mount(){
    if timeout 2 ping -c 1 -W 2 10.103.254.6 &> /dev/null; then timeout 2 sshfs -o reconnect,ServerAliveInterval=15,ServerAliveCountMax=3 \
        "${USER}"@10.103.254.6:/ /home/"${USER}"/mnt/cmc3 &>/dev/null
    else fusermount -u -z ~/mnt/cmc3 ;
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode