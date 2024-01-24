---
title: 如何配置多个 Github SSH Key
date: 2024-01-24 11:29:11
categories: Setups
tags:
	- git
	- github
  - configuration
---

git 和 github 目前是我工作生涯中用的最多的工具。不管是我的编程学习亦或者是我的工作，都需要本地使用 git 来 commit 代码的修改，并把 github 作为 remote 仓库, 将代码的修存储下来。我将会记录下我在使用他们的过程中做过的配置帮我解决了哪些问题。

<!--more-->

# WHY

我是一个喜欢公私分明的人，不喜欢使用我的私人账号提交代码到公司的 github, 包括不喜欢泄漏我的私人邮箱（关于如何针对不同的文件夹配置不同的 gitconfig, 可以自行查看[使用有条件的 .gitconfig](./conditional-git-config.md)).

对于这种情况，我希望有两个或者多个 github 账号，分别用来提交代码到不同的 github 仓库。那么问题来了，作为长期在 github 开发的重度患者， 为了使用方便快捷（不希望经常输入用户名密码），非常依赖 **SSH（Secure Shell Protocol）key** 作为提交代码到 github.com 的重要凭证。

# WHAT

到目前为止，对于 github 小白用户来说，对于 SSH 有很多问题，我将相关的文档列在这里，供大家参考。

- [什么是 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/about-ssh)
- [如何查看已有的 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)
- [如何生成新的 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [怎么将 SSH key 添加到 github 账户?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

# HOW

言归正传，假如我有两个 github 账户，并且我都想配置 SSH key 来推送代码，那这个时候就需要根据不同的 key, 推送本地仓库代码到不同的仓库甚至是不同的 organization.

## 配置 SSH config

1. 在生成 SSH key 的时候，给一个特定的名字给保存 SSH 密钥的文件，例如：`id_xxxx_1`，一般这个文件会存储在 `/Users/<YOU>/.ssh` 文件夹。
2. 经过第一步，如果你要针对两个 github 账户设置 SSH, 现在 `.ssh` 文件夹里应该会有**两对**密钥文件(分别是公钥和私钥)：

   ```
   id_xxxx_1.pub
   id_xxxx_1
   ```

   ```
   id_xxxx_2.pub
   id_xxxx_2
   ```

   > 如何将对应的 ssh key 添加到对应的 github 账号，可以查看[官方文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account), 这里就不再复述。

3. 之后就需要在你的本地做 ssh config 配置，输入命令

   ```
   cat ~/.ssh/config
   ```

   找到`~/.ssh/config`文件，并查看内容。如果没有的话，创建一个新文件，放置在 user 目录下的 `.ssh` 文件夹下面，根据下面的例子进行配置

   ```
   Host github-1
   	HostName github.com
   	Port 22
   	AddKeysToAgent yes
   	IdentityFile ~/.ssh/id_xxxx_1

   Host github-2
   	HostName github.com
   	Port 22
   	AddKeysToAgent yes
   	IdentityFile ~/.ssh/id_xxxx_2
   ```

4. 设置完成之后，可以使用以下命令测试一下配置是否生效

   ```
   > ssh -T github-1
   ```

   如果测试链接通过，会收到下面这条消息

   ```
   > Hi USERNAME! You've successfully authenticated, but GitHub does not
   > provide shell access.
   ```

   如果出现错误的情况，可以查阅 [SSH 故障排除文档](https://docs.github.com/zh/authentication/troubleshooting-ssh/error-permission-denied-publickey).

## SSH Config 常用参数配置

### Host

用于我们执行 SSH 命令的时候如何匹配到该配置。

- \*，匹配所有主机名。
- \*.example.com，匹配以 .example.com 结尾。
- !_.dialup.example.com,_.example.com，以 ! 开头是排除的意思。
- 192.168.0.?，匹配 192.168.0.[0-9] 的 IP。

### HostName

真实的主机名，默认值为命令行输入的值（允许 IP）。你也可以使用 %h，它将自动替换，只要替换后的地址是完整的就 ok。

- github 默认主机名是 `github.com`, 使用端口为 `22`。
- github ssh 主机名是 `ssh.github.com`, 使用端口为 `433`.

### AddKeysToAgent

是否自动将 key 加入到 ssh-agent，值可以为 no(default)/confirm/ask/yes。

如果是 yes，key 和密码都将读取文件并以加入到 agent ，就像 ssh-add。其他分别是询问、确认、不加入的意思。添加到 ssh-agent 意味着将私钥和密码交给它管理，让它来进行身份认证。

### IdentityFile

指定读取的认证文件路径，允许 DSA，ECDSA，Ed25519 或 RSA。值可以直接指定也可以用一下参数代替：

- %d，本地用户目录 ~
- %u，本地用户
- %l，本地主机名
- %h，远程主机名
- %r，远程用户名

### Port

指定连接远程主机的哪个端口，22(default)

其他参数，可以参考[Reference](#reference)里的其他文档.

# USAGE

我们设置完成了。但是使用时会出现以下的**问题**

- 怎么改造让旧仓库使用新的 SSH key 推送代码？
- clone 仓库的时候发现，ssh 的 link 都是 `git@github.com` 开头, 那怎么应用到不同的 SSH Host 上呢？

为了解决上面的问题，我们需要将仓库的 remote url 修改一下，例如从 github 复制的 url, 如下:

```
git@github.com:xxxx_1/repo.git
```

需要修改成：

```
git@github-1:xxxx_1/repo.git
```

`github-1` 是配置在 SSH config 里的 Host。如此这般，就不会出现 SSH key 推送混淆的问题了。

# Reference

- [github 官方文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/about-ssh)
- [一台电脑存放多个 git 账户的多个 rsa 秘钥](https://www.cnblogs.com/qingguo/p/5686247.html)
- [SSH Config 那些你所知道和不知道的事](https://deepzz.com/post/how-to-setup-ssh-config.html)
