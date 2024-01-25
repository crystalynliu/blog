---
title: 如何配置多个 Github SSH Key
date: 2024-01-24 11:29:11
categories: Setups
tags:
	- git
	- github
  - configuration
---

我是一个喜欢公私分明的人，不喜欢使用我的私人账号提交代码到公司的 github, 包括不喜欢泄漏我的私人邮箱（关于如何针对不同的文件夹配置不同的 gitconfig, 可以自行查看[使用有条件的 .gitconfig](./conditional-git-config.md)).

对于这种情况，我希望有两个或者多个 github 账号，分别用来提交代码到不同的 github 仓库。那么问题来了，作为长期在 github 开发的重度患者，怎么样才能轻松在不同的项目之间无缝切换账户进行身份验证呢？

<!--more-->

# WHY

先来了解一下，在什么情况下，我们需要配置多个 **SSH（Secure Shell Protocol）key**:

1. **多个 GitHub 账户：**
   如果你有多个 GitHub 账户，例如一个用于个人项目，另一个用于工作项目，那么每个账户可能需要不同的 SSH 密钥。

2. **访问多个 Git 仓库提供商：**
   如果你同时使用 GitHub、GitLab 或 Bitbucket 等不同的 Git 仓库提供商，每个提供商可能需要使用不同的 SSH 密钥。

通过配置多个 SSH 密钥，你可以轻松地在不同的上下文中切换，确保每个项目或账户都能正确地进行身份验证，而不会产生冲突。

# WHAT

到目前为止，对于 github 小白用户来说，对于 SSH 有很多问题，我将相关的文档列在这里，供大家参考。

- [什么是 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/about-ssh)
- [如何查看已有的 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)
- [如何生成新的 SSH?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [怎么将 SSH key 添加到 github 账户?](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

# HOW

言归正传，假如我有两个 github 账户，并且我都想配置 SSH key 来推送代码，那这个时候就需要根据不同的 key, 推送本地仓库代码到不同的仓库甚至是不同的 organization.

## 配置 SSH config

1. 在生成 SSH key 的时候，指定一个 SSH 密钥文件名，例如：`id_xxxx_1`

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_xxxx_1
   ```

   完成命令的 step 之后会生成两个文件：私钥 `id_xxxx_1` 和 公钥`id_xxxx_1.pub`

2. 将私钥添加到 SSH 代理:

   ```bash
   ssh-add ~/.ssh/id_xxxx_1
   ```

3. 将新生成的 SSH 密钥添加到 GitHub 账户：

   将公钥 `~/.ssh/id_xxxx_1.pub` 内容添加到 GitHub 账户的 SSH 密钥设置中。

   > 如何将对应的 ssh key 添加到对应的 github 账号，可以查看[官方文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account), 这里就不再复述。

4. 创建或编辑 SSH 配置文件：

   ```bash
   nano ~/.ssh/config
   ```

   在配置文件中添加以下内容，为每个 GitHub 主机指定使用的密钥：

   ```
   # 默认密钥
   Host github.com
		HostName github.com
		Port 22
		AddKeysToAgent yes
		IdentityFile ~/.ssh/id_rsa

   # 新密钥
   Host github-xxxx-1
		HostName github.com
		Port 22
		AddKeysToAgent yes
		IdentityFile ~/.ssh/id_xxxx1
   ```

	请确保 `Host` 后面的名称与 `git remote` 中的主机名相匹配。

5. 测试 SSH 连接是否正常

   ```
   > ssh -T github-xxxx-1
   ```

   如果测试链接通过，会收到下面这条消息

   ```
   > Hi USERNAME! You've successfully authenticated, but GitHub does not
   > provide shell access.
   ```

   > 如果出现错误的情况，可以查阅 [SSH 故障排除文档](https://docs.github.com/zh/authentication/troubleshooting-ssh/error-permission-denied-publickey).

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

为了解决上面的问题，我们需要通过修改remote url指定不同的主机名来使用不同的密钥。

```bash
git clone git@github.com:yourusername/yourrepo.git   # 使用默认密钥
git clone git@github-xxxx-1:yourusername/yourrepo.git  # 使用新密钥
```

`github-xxxx-1` 是配置在 SSH config 里的 Host。如此这般，就不会出现 SSH key 推送混淆的问题了。

# Reference

- [github 官方文档](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/about-ssh)
- [一台电脑存放多个 git 账户的多个 rsa 秘钥](https://www.cnblogs.com/qingguo/p/5686247.html)
- [SSH Config 那些你所知道和不知道的事](https://deepzz.com/post/how-to-setup-ssh-config.html)
- [ChatGPT](https://chat.openai.com/)