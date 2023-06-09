# 版本标签已更新。支持 remove 命令和- force 选项

> 原文：<https://dev.to/franiglesias/versiontag-updated-supports-remove-command-and---force-option-pmh>

几天前，我发布了一个简单的工具来管理 git 项目中的语义版本。

[![franiglesias image](img/a31bca5821248859e051bcc546af70a1.png)](/franiglesias) [## git 中的自动化语义版本控制

### 弗兰·伊格莱西亚斯 7 月 22 日 181 分钟阅读

#tools #git #bash #semanticversioning](/franiglesias/automate-semantic-versioning-in-git-ah9)

今天，我刚刚更新了它，以管理一个`--force`选项来避免确认问题，并一次性完成标记。

此外，我还添加了一个`remove`命令来帮助删除最后一个标签，如果您在没有正确完成的情况下标记开发，这个命令会很有用。

项目在这里

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[version tag](https://github.com/franiglesias/versiontag)

### Bash 命令来自动化标记语义版本控制

<article class="markdown-body entry-content container-lg" itemprop="text">

# 版本标签

Bash 命令在 git 存储库中自动标记语义版本。

当前版本:v2.1.1

## 特征

允许在 git 项目中轻松维护语义版本。

自 1.4.2 支持。semver 文件写入。

因为 2.0.0 标签在默认情况下没有注释。您必须使用选项`-m`或`--message`来注释标签。

## 命令

`help` -显示帮助
`current` -显示找到的最后一个版本标签。如果没有，默认为`v0.0.0`
`patch` -增加补丁版本:`v1.0.0 -> v1.0.1`
`minor` -增加次要版本(并重置补丁):`v1.0.5 -> v1.1.0`
`major` -增加主要版本(并重置次要版本和补丁):`v1.3.4 -> v2.0.0`
`remove` -删除存储库中的最后一个标签。该命令不支持`-f|--force`选项。

## 选择

`-h|--help` -显示帮助。
`-f|--force` -脚本不会要求确认。
`-d|--dry` -执行而不执行任何改变。
`-m|--message` -用消息注释标签。
`-s|--semver` …

</article>

[View on GitHub](https://github.com/franiglesias/versiontag)

这是实用程序的当前代码。请随意提出任何改进意见。我正在学习 bash 脚本，所以如果有任何帮助来更好地组织代码并以任何方式改进它，那将是非常棒的。

```
#!/bin/bash

function execute {
    cmd=("$@")
    echo
    echo "Executing ${cmd[*]} ..."
    echo
    # execute the command:
    "${cmd[@]}"
}

function confirm() {
    read -p "$1 ([y]es or [N]o): "
    case $(echo $REPLY | tr '[A-Z]' '[a-z]') in y|yes) echo "yes" ;;
        *)     echo "no" ;;
    esac
}

function getMessage() {
    if [[ -z "$@" ]]
    then echo false;
    fi echo $@
}

function showHelp() {
    echo
    echo "versiontag"
    echo "=========="
    echo
    echo "Automates version tag creation."
    echo 
    echo "Commands: patch|minor|major|current 'Tag message'"
    echo "          remove"
    echo "          current"
    echo
    echo "Examples:"
    echo "---------"
    echo 
    echo "Show current version: ./versiontag current"
    echo "Patch version:        ./versiontag patch 'Fix broken view'"
    echo "Minor version:        ./versiontag minor 'Add Customer filter by email'"
    echo "Major version:        ./versiontag major 'Blog module'"
    echo "Remove last version:  ./versiontag remove"
    echo
    exit 0
}

function showSuccess() {
    echo
    echo "Tag v$major.$minor.$patch was created in local repository."
    echo
    echo "Push it:"
    echo
    echo "    git push origin v$major.$minor.$patch"
    echo
    echo "Delete tag:"
    echo
    echo "    git tag -d v$major.$minor.$patch"
    echo
}

lasttag=`git describe --tags --long` || true if [[ -z "$lasttag" ]]; then lasttag='v0.0.0'
fi cleantag=${lasttag%%-*}

parts=(${cleantag//./ })

major=${parts[0]:1}
minor=${parts[1]}
patch=${parts[2]}

major=${major:0}
minor=${minor:0}
patch=${patch:0}

last="v$major.$minor.$patch"

mode=''
force=false while [ -n "$1" ]
do
    case "$1" 
    in
        -f|--force)
            force=true;;
        -h|--help)
            showHelp
            exit 0;;
        patch) 
            patch=$((patch + 1)) 
            msg=$(getMessage "$2")
            shift 1
            mode='patch';;
        minor) 
            minor=$((minor + 1))
            patch=0 
            msg=$(getMessage "$2")
            shift 1
            mode='minor';;
        major) 
            major=$((major + 1)); minor=0
            patch=0 
            msg=$(getMessage "$2")
            shift 1
            mode='major';;
        current)
            echo "Current version: $last"
            exit 0;;
        remove)
            if [[ "yes" == $(confirm "Do you want to remove $last?") ]]; then execute git tag -d "$last"
                execute git push origin ":$last"
            fi exit 0;;
        help)
            showHelp;;
        *)
            echo "$1 option is not valid"
            exit 1;;
    esac

    shift 1;
done

if [[ false == "$msg" ]]; then echo
    echo "No message."
    exit 1;
fi

if [[ -z "$mode" ]]; then showHelp;
    exit 0;
fi echo "Current version : $last"
echo "New tag         : v$major.$minor.$patch"
echo
echo "Message         : $msg"

if [[ "$force" = true ]]; then execute git tag -a "v$major.$minor.$patch" -m "$msg ($mode version)"
    execute git push origin "v$major.$minor.$patch"
    echo 
    echo "Version tag created and pushed"
    exit 0;
fi

if [[ "no" == $(confirm "Do you agree?") ]]; then echo
    echo "No tag was created"
    exit 0;
fi execute git tag -a "v$major.$minor.$patch" -m "$msg ($mode version)"

showSuccess;

if [[ "yes" == $(confirm "Do you want to push this tag right now?") ]]; then execute git push origin "v$major.$minor.$patch"
fi exit 0 
```

Enter fullscreen mode Exit fullscreen mode