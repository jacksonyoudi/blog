---
title: (效率工具)程序员必备终端及美化
date: 2021-07-31 17:21:07
img: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Wbh9jA.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/1zZOJy.jpg
toc: true
mathjax: false
summary: mac下终端工具iterm2 + oh my zsh + powerlevel10k
categories: 
- 工具
keywords:
-  mac
-  zsh
- powerlevel10k
- dracula

tags:
-  mac
-  zsh
- powerlevel10k
- dracula
    
---


![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/QicdYL.jpg)

作为一个合格的程序员，CLI是必备的技能。 工欲善其事，必先利其器。一个顺手并且提高效率的终端是必须的。 平时很多人会私信或评论中问我关于我的终端相关的内容，今天我整理出来。分享给大家。

#### 需要安装的软件
> 1. iterm2
> 2. dracula
> 3. zsh
> 3. Oh My ZSH
> 4. powerlevel10k


上面👆给出了需要安装的软件包， 下面就按安装的顺序一个一个介绍

### 一、iterm2
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Wbh9jA.jpg)

这个就不多介绍， 我相信只要是使用mac的程序员，这个都是知道的， 当然，也有一个其他类似的产品。 后面安装的和iterm2没有强依赖。 这只是一个终端， 没有最好，适合你的就是最好的。 顺便提一下， 我自己的💻使用touch bar的， 是支持touch bar，一些很炫的操作可以在touch bar上操作。但是，我一直使用公司的笔记本，使用快捷键进行操作， 效率更高。

[官网](https://www.iterm2.com/)

#### 1. 安装
1). 方式一
[下载地址](https://www.iterm2.com/downloads.html)， 下载后，点击安装就可以了

2). 方式二
```bash
brew cask install iterm2   
```
brew使用，这里就不介绍了


#### 2. 使用
[文档](https://www.iterm2.com/documentation.html)
这里顺便介绍一下，主要是快捷键, 自己对着文档，学习一下， 因为重点不在这。
`CMD + 单击` 可以实现跳转

### 二、 安装配置dracula主题
这个也有两种方式
1. 使用git
   `git clone https://github.com/dracula/iterm.git`
2. 直接下载，解压
   下载地址 [GitHub .zip download](https://github.com/dracula/iterm/archive/master.zip)

*使用主题*
配置过程如下
>1. 打开 iTerm2 > Preferences > Profiles > Colors Tab， 选择color
>2. 点击下面的`Color Presets`,展开
>3. 选择`import`, 将上面👆下载的文件导入
>4. 选择`Dracula.itermcolors`文件导入
>5. 导入以后，重新选择 `Color Presets`，选择 `dracula`就可以了
    具体如下：
    ![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/FLOTC9.jpg)




### 三、Oh My ZSH
[Oh My ZSH]([https://ohmyz.sh/](https://ohmyz.sh/)
)
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/1zZOJy.jpg)

1. 先安装 zsh
   下面只给命令
 ```bash

# 安装
brew install zsh zsh-completions

# 切换shell
chsh -s /bin/zsh
```

2. 安装Oh My ZSH
   下面选一种
```bash
 sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
 # or
 sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
如果感兴趣，可以看下脚本内容，定义了安装目录什么的，和常见的安装shell没有区别。

3. 关闭iterm2, 然后再打开，就生效了
4. 配置 一个插件
   配置在 `.zshrc`文件中，配置很简单
   可以使用的插件以及描述 [github](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins)


### 四、powerlevel10k
[github地址](https://github.com/romkatv/powerlevel10k)

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/DUuyUe.jpg)


1. 安装主题
   也有很多种方式， 这里使用git
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
在 `.zshrc`中配置主题生效，退出， 就可以生效
```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```
如果你还没有安装字体，那么可以使用下面的命令来安装所需字体。
一些需要字体安装的文档 [install-a-powerline-font](https://github.com/bhilburn/powerlevel9k/wiki/Install-Instructions#step-2-install-a-powerline-font)
```bash
# clone
git clone https://github.com/powerline/fonts.git
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```

字体安装完毕之后，打开 Shell 你会发现字体依然没有生效，这是因为你没有选择对应的字体。在设置中选择你想要的支持字体。
如果有一些特殊的图标不能正常显示， 可能需要安装 [awesome-terminal-fonts](https://github.com/gabrielelana/awesome-terminal-fonts)字体


还有一些图标，比如github,gitlab, git,linux的一些图标在`/Users/youdi/.oh-my-zsh/custom/themes/powerlevel9k/functions/icons.zsh`中修改。

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Mob8Xk.jpg)

#### 其他
我使用 `colorls`,是ruby的的工具,
[安装]([https://github.com/athityakumar/colorls](https://github.com/athityakumar/colorls))
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/vjJGRU.jpg)

另外一个查看性能的工具 `gotop`, 你肯定用过 `top`,`htop`
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/8cEKLj.jpg)


[源码]([https://github.com/cjbassi/gotop](https://github.com/cjbassi/gotop)
), 类似的 top的工具很多，各个语言版本的都有， 我比较喜欢编译型语言的版本。
