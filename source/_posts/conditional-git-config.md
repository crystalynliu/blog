---
title: 使用有条件的 .gitconfig
categories: Setups
tags:
  - Git
  - configuration
date: 2019-11-11 18:07:39
---

如果你是一个 Git 重度依赖患者，这篇文章就是让你避免提交错误的 email 给某个 repo 的 commit, 让我们彻底区分开工作与生活的 email。

> No more accidentally committing with the wrong email in a new repository

<!--more-->

# Scenario

在使用 Git 工具之前，我们会预先设置一个全局的 `git email` 和 `username`，这个可能是你的**私人邮箱**或者是**工作邮箱**。但是你只能选择一个来设置。

```
git config email --global <your.email>
git config username --global <your.username>
```

但如果你生活和工作使用同一台电脑工作，经常会有这样的事情，创建一个新的 repo, 这个 repo 可能是工作需要，但是你的 Git 原先的设置是私人邮箱，这个时候，会出现两种情况:

* 你忘记了这件事情，你会使用全局配置提交 commit, 之后就会发现 commit 的提交上会出现你的私人邮箱，这是不专业的。
* 你想起来了这件事情，所以你会使用 `git config email <your.work.email>` 去修改当前 repo config 设置。

那是不是会有更灵活的方式呢？

# One solution

在`Git 2.13` 以上的版本，`.gitconfig` 支持 [Conditional Includes](https://git-scm.com/docs/git-config#_conditional_includes) 的语法，可以使用 `includeIf.<condition>.path` 的语法在特定的条件下复写  global 的配置。

如果我们对自己的电脑整理妥当，那么我们经常会将工作放在一个特定的文件夹下面，例如 `~/works`。这样我们可以针对这个特定的文件夹，创建一个包含特定 email 的 `.gitconfig`。那到底要怎么配置呢？

1. **创建一个特定的 .gitConfig**

	首先，我们需要创建一个 `.gitconfig` 文件适用于 `~/works` 文件夹，这个文件放置的位置并不重要，你可以选择放置在 `HOME` path, 也可以选择放置在 `~/works` 文件夹下面。

	按照我的习惯，我将它放置在 `HOME` path, 起名为 `.gitConfig-work`， 并写入想要被复习的配置：

	```
	[user]
		name = <your.name>
		email = <your.work.email>
	```

2. **为`~/works`文件夹设置`conditional includes`**

	接下来，我们需要在 gloabl `.gitconfig` 文件里，追加一些配置来告诉 Git，当我们工作在 `~/works` 文件夹下面的时候，应该使用 `.gitconfig-work` 的配置项。

	```
	[includeIf "gitdir:~/works/"]
		path = ~/.gitconfig-work
	```


	这个配置表明当你工作的位置处于 `~/works` 文件夹下面的时候，`.gitconfig-work` 的配置项会复写 `.gitconfig` 中的配置，这时，当你在 `~/works` 文件夹下面创建新的 repo 并发生提交时，你可以看到 email 会默认变成工作邮箱。

以上的两步，就可以帮助你解决工作时提交错误邮箱的尴尬事情！

# End

当然，强大的 Git 还有更多的功能等待我们学习。`includeIf` 不仅仅只有一个参数 `gitdir`, 还包括 `gitdir/i` 关键字来进行匹配。

更多的配置，希望大家查看 [git config](https://git-scm.com/docs/git-config#_conditional_includes)。

# Reference

- [Conditional Git Configuration](https://blog.jiayu.co/2019/02/conditional-git-configuration/)
- [Configure user.name and user.email per wildcard domains in .gitconfig](https://stackoverflow.com/questions/13750953/is-it-possible-to-configure-user-name-and-user-email-per-wildcard-domains-in-gi)
