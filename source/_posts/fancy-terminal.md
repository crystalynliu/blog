---

title: 搭建 Colorful Terminal (iTerm2 + oh-my-zsh)
date: 2019-07-23
categories: laptop.env.setting
tags: [macOS, terminal]

---

作为一名在 macOS 下工作的码农，有一句名言很实用

> 工欲善其事，必先利其器。

首先需要做的就是让编码的工具使用率变得更高，更快以及更自动化。
今天来谈谈怎么配置 Terminal。

<!--more-->


# [iTerm2](https://www.iterm2.com/)
MacOS 自带的 Terminal 是很正规的**白底黑字**， 对于我这种已经习惯**黑底白字**的码农来说是不友好的，并且没有更多颜色特性表现不同的特性。所以在第一时间知道 **iTerm2** 的时候就果断抛弃了 Terminal，从此被打入冷宫:p

并且 iTerm2 拥有一些非常实用的 [特性](https://www.iterm2.com/features.html), 例如 `split panes`, `autocomplete`等。至于这些特性如果使用，需要大家自行摸索。

## How to install

说起来安装一些软件，在 macOS 下不得不提的就是 [Homebrew](https://brew.sh/), 它可以让我们很方便的使用命令行安装一些管理工具（node, mysql等）以及 macOS 的应用（例如 chrome, evernote等）。Homebrew 的使用方式就算是课外作业吧。

只需要一行命令，不用上网搜索下载安装，就可以安装好想要的应用：

```bash
$ brew cask install iterm2
```

## Settings
讲两个我很常用的设置

* **透明度(transparency)**
    
    我们在使用 iterm2 的时候可能会需要按照搜索或者笔记的内容去写 command, 那如果有一定的透明度，我们就不用害怕屏幕不够大啦。
    
    `iTerm2` menu -> `Preferences` -> `Profiles` -> `Window` 标签页 找到 `Transparency` 就可以设置透明度了。

* **工作目录(working directory)**

    在 iTerm2 中打开新的 pane 和 tab 时， 默认情况下总是 `Home` 目录，之后还需要使用命令进入工作目录。所以我们可以设置 iTerm2 在每次打开新窗口的时候就自动进入你当前正在工作的目录。
    
    `iTerm2` menu -> `Preferences` -> `Profiles` -> `General` 标签页中的 `Working Directory` 选择 `Reuse previous session's directory`
    
现在，Terminal 就可以退出舞台，后面所有使用命令行的时候就由 iTerm2 来替代了。    

# [Oh My Zsh](https://ohmyz.sh/)

默认的 bash 命令是黑白色的。但是我们更期望在用不同的颜色表示不同的文件（unix 是文件系统哦）。并且它有很强大的插件和工具，可以美观 interface 以及简化我们的操作。

## How to install

* via curl
    
    ```bash
    $ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```

* via wget
    
    ```bash
    $ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
    
    ```

## Settings

### Themes
**oh-my-zsh** 现在已经拥有上百个主题样式，怎么样应用想要的主题呢？

1. 可以在 wiki 上找到所有主题的 [screenshots](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)，之后挑选你喜欢的主题，推荐一个 fancy 的选择是 `agnoster `。
2. 需要修改 `~/.zshrc` 文件，找到有一个关于主题的变量, default 值是`robbyrussell `, 可以修改这个变量为你喜欢的主题。

    ```
    ZSH_THEME="agnoster"
    ```
3. 重新启动 Terminal，你就会看到一个全新的 Terminal    

    ![](/images/agnoster-theme.png)


#### [Powerline Fonts](https://github.com/powerline/fonts)
    
如果发现酷酷的分支图标都变成了问号，那是因为大多数的主题都需要安装 Powerline Fonts。你可以使用下面的命令快速安装
    
```bash
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts

```

之后通过修改 font 参数就可以使用 Powerline fonts 了。

![](/images/font-setting.jpg)
    
#### 配色方案

如果发现窗口的颜色不是像我一样是深蓝色，记得将 Terminal 的配色方案设置为 `Solarized Dark`。

![](/images/color-presets.png)


### ls 目录的颜色
使用`ls`命令的时候，发现还是灰蒙蒙的一片，似乎无法区分哪些是文件夹，哪些是可执行文件。如何让 `ls` 命令的结果带上颜色，在 `~/.zshrc` 文件中添加👇的配置

```bash
# LSCOLORS
export LSCOLORS="exfxcxdxbxexexabagacad"
alias ls='ls -G'

```

### Vim 编辑器的颜色
找到 [Solarized for vim](https://github.com/altercation/vim-colors-solarized) 的配色方案。

按照步骤下载之后，修改 `~/.vimrc` 文件

```bash
syntax on
set nu
set mouse=a
colorscheme solarized
```

## Plugins
**oh-my-zsh** 有强大的插件工具，可以通过修改`~/.zshrc`文件中的 plugin 配置，来控制 plugin 是否激活，当然前提是你已经安装了相关的插件。

```bash
plugins=(git z zsh-completions zsh-syntax-highlighting)
```

### git
git 的插件是默认安装的，就是说当你安装了 **oh-my-zsh**，就已经有了 git 插件，通过配置就可以使用它。

这个 git 插件主要是用来干什么的呢？git 对于一个码农来说重要性不言而喻，几乎每个人都会使用一些方法（例如 alias）来提高效率，使用 `git st` 来替换 `git status`。但是这需要我们自己手动设置，并且根据每个人的习惯不同，设置也不完全一样。

**oh-my-zsh** 提供了一套系统别名（alias），来达到相同的功能。比如`gst`作为`git status`的别名。所以你用了 **oh-my-zsh** 就相当拥有了一套全球通用的 git 命令别名系统。是不是特别棒？

完整的别名列表请参考官方文档：[git Aliases](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/git/#aliases)

### z

在打开 Terminal 后， 你是怎么进入工作目录的？是 `cd xx/xx/xx...`, `^R` 还是通过使用alias？

**z** 的出现可以帮助你快速进入目录，例如在我的电脑上使用 `z cask` 命令就会进入`/usr/local/Caskroom` 目录。

**z** 是 **oh-my-zsh** 默认安装，只需要更改配置，重新加载 Terminal。就可以使用了。  

### zsh-syntax-highlighting

既然要做 colorful terminal, 那我们就要做到极致。**[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)** 是为 zsh 的 shell 命令提供高亮的功能，它可以在 zsh 提示符下键入 cammand 时高亮显示到 Terminal，并且会在命令执行前进行检查，将错误的 cammand 高亮为红色。

怎么将插件安装到 **oh-my-zsh**，然后配置它呢?

* 将插件的 repo 拷贝到 **oh-my-zsh** 的插件目录下

    ```bash
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    ```

* 在 `~/.zshrc` 激活 plugin

    ```bash
    plugins=( [plugins...] zsh-syntax-highlighting)
    ```

# End

写这篇文章的初衷，仅仅是因为我刚刚对我的 Terminal 进行了配置，害怕忘记。以为很快就可以的文章，也写了 4，5 个小时。在这个过程中会去找各种参考，会去配上各类官方链接，慢慢地它就变成了一篇给大家看的文章。

# Reference
- [Guide to setup Mac](https://github.com/macdao/ocds-guide-to-setting-up-mac)
- [zsh+on-my-zsh配置教程指南(程序员必备)](https://segmentfault.com/a/1190000013612471)


