# 如何向 windows 控制台添加自定义提示

> 原文：<https://dev.to/c33s/how-to-add-a-custom-prompt-to-cmder-gb2>

[Cmder](http://cmder.net/) 是一个很棒的软件包，它取代了 windows 上默认的控制台`cmd.exe`或`powershell`。

如果您在 git repo 中，您会在提示中看到当前的分支，如果它是干净的，则显示为白色，如果是脏的，则显示为红色。纱线、npm，...

[![Cmder Screenshot](img/26458ffeacdb38656d61d780e325517e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6m2wyLyF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq1toqolv48jbcjea13q.png)

有时你也想把**的输出添加到提示符中。可以这样做:**

 **在我的例子中，我想看看我在哪个[地形工作区](https://www.terraform.io/docs/state/workspaces.html)上。使用 terraform，您可以通过在 terraform 目录中运行`terraform workspace show`来实现这一点。这将输出类似于
的内容

```
λ terraform workspace show
default 
```

为此你必须创建一个 **[碰杯扩展](https://github.com/vladimir-kotikov/clink-completions)** 。假设你在`C:\Program Files\Cmder`中安装了 cmder，你在`C:\Program Files\Cmder\vendor\clink-completions`中创建了一个名为`terraform.lua`的文件

`terraform.lua`

```
local color = require("color")

local function is_terraform()
    for dir in io.popen([[dir ".\" /b /ad]]):lines() do 
        if dir == ".terraform" then
            return true
        end 
    end

    return false
end

local function terraform_prompt_filter()
    if is_terraform() then
        f = assert (io.popen ("terraform workspace show"))
        for line in f:lines() do
            terraform_context = '(workspace: ' .. line .. ')'
            if line == "production" then
                prompt_color = color.RED
            elseif line == "development" then
                prompt_color = color.GREEN
            else
                prompt_color = color.YELLOW
            end
            break
        end -- for loop
        f:close()
        terraform_context = color.color_text(terraform_context, prompt_color)
        clink.prompt.value = clink.prompt.value:gsub("\n", terraform_context .. "\n")
        return false
    end 
end

clink.prompt.register_filter(terraform_prompt_filter, 1) 
```

这段代码通过查看目录`.terraform`是否存在来检查它当前是否在 terraform 目录中。如果您在`development`工作区，它将工作区输出显示为绿色，如果您在`production`工作区，它将显示为红色，而在所有其他工作区，它将显示为黄色。根据你的需要修改代码。

我确信 lua 代码可以优化，我对评论和改进持开放态度。

链接:

*   Willi Heidelbach 的封面图片[https://pix abay . com/en/puzzle-match-missing-hole-blank-693870/](https://pixabay.com/en/puzzle-match-missing-hole-blank-693870/)**