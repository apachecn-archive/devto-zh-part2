# Ubuntu 的电力线

> 原文：<https://dev.to/jeancarlosn/powerline-for-ubuntu-3o14>

[![](img/c7e3e23d4191b518c0331aa5f1beaf46.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2B9fc_AG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d1bwwtixojkmk2eui2t2.png)

第一步->在你的控制台中写:`sudo apt install powerline`。

第二步->写入。bashrc 在你家目录本:
`if [ -f /usr/share/powerline/bindings/bash/powerline.sh ]; then
source /usr/share/powerline/bindings/bash/powerline.sh
fi`。

3-最后一步->保存文件，并打开新的控制台 o 终端，将看到

4-如果存在问题，如行中的堰字符，命令他们安装字体:

 `clone
git clone https://github.com/powerline/fonts.git --depth=1
install
cd fonts
./install.sh
clean-up a bit
cd ..
rm -rf fonts`

这里这个链接所在的库字体是:[https://github.com/powerline/fonts](https://github.com/powerline/fonts)