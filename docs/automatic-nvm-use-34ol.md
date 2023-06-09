# 自动` nvm 使用'

> 原文：<https://dev.to/d4nyll/automatic-nvm-use-34ol>

[![](img/c5eabcebe0b01b4ed3f1cb518fb71874.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sqQp7xmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9bf5o9sxr8smp8055vjm.gif)

在从事多个 JS 项目时厌倦了键入`nvm use`？那么试试这个 Bash 别名吧！

```
 find-up () {
    path=$(pwd)
    while [[ "$path" != "" && ! -e "$path/$1" ]]; do path=${path%/*}
    done echo "$path"
}

cdnvm(){
    cd $@;
    nvm_path=$(find-up .nvmrc | tr -d '[:space:]')

    # If there are no .nvmrc file, use the default nvm version
    if [[ ! $nvm_path = *[^[:space:]]* ]]; then declare default_version;
        default_version=$(nvm version default);

        # If there is no default version, set it to `node`
        # This will use the latest version on your machine
        if [[ $default_version == "N/A" ]]; then nvm alias default node;
            default_version=$(nvm version default);
        fi

        # If the current version is not the default version, set it to use the default version
        if [[ $(nvm current) != "$default_version" ]]; then nvm use default;
        fi

        elif [[ -s $nvm_path/.nvmrc && -r $nvm_path/.nvmrc ]]; then declare nvm_version
        nvm_version=$(<"$nvm_path"/.nvmrc)

        # Add the `v` suffix if it does not exists in the .nvmrc file
        if [[ $nvm_version != v* ]]; then nvm_version="v""$nvm_version"
        fi

        # If it is not already installed, install it
        if [[ $(nvm ls "$nvm_version" | tr -d '[:space:]') == "N/A" ]]; then nvm install "$nvm_version";
        fi

        if [[ $(nvm current) != "$nvm_version" ]]; then nvm use "$nvm_version";
        fi
    fi
}
alias cd='cdnvm' 
```

Enter fullscreen mode Exit fullscreen mode

这将从您的当前目录“向上”搜索，以尝试找到一个`.nvmrc`文件。如果它找到了，它将使用那个版本；如果没有，它将使用默认版本。

* * *

这段代码的前一次迭代只查看当前目录，这意味着如果您的项目目录有许多嵌套目录，内部目录将使用默认版本，而不是项目的版本。

先前的迭代记录如下:

```
alias cd='cdnvm(){ cd $1; if [[ -f .nvmrc && -s .nvmrc && -r .nvmrc ]]; then <.nvmrc nvm install; elif [[ $(nvm current) != $(nvm version default) ]]; then nvm use default; fi; };cdnvm' 
```

Enter fullscreen mode Exit fullscreen mode

这个别名会自动检测您正在进入的目录中的一个文件，并切换到那个版本。而且当你`cd`回来的时候，它会自动切换回`default`版本！

或者如果你喜欢一行程序:

```
echo 'alias cd='\''cdnvm(){ cd $1; if [[ -f .nvmrc && -s .nvmrc && -r .nvmrc ]]; then <.nvmrc nvm install; elif [[ $(nvm current) != $(nvm version default) ]]; then nvm use default; fi; };cdnvm'\''' >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你喜欢可读性更强的格式:

```
cdnvm(){
  cd $1
  if [[ -f .nvmrc && -s .nvmrc && -r .nvmrc ]]; then
    <.nvmrc nvm install;
  elif [[ $(nvm current) != $(nvm version default) ]]; then nvm use default;
  fi
}
alias cd='cdnvm' 
```

Enter fullscreen mode Exit fullscreen mode

在[https://asciinema.org/a/191898](https://asciinema.org/a/191898)观看比赛