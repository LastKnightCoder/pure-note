---
date: 2022-04-19
tags: [Git/Github, SSH]
---

## 背景

今天我突然想开一个新的账户，专门用来写一些电子书，在通过电子邮箱创建了另一个账户之后，第一件事就是为这个账户添加一个 `ssh` 的公钥，这样就不用每次提交都要输入密码了，但是当我试图直接将我电脑上的公钥提交时，它提示我这个公钥已经存在，这是我另一个 Github 账户使用的公钥，这使我意识到不同的账户不能使用相同的公钥。

## 解决办法

首先需要为新账号生成新的公钥和私钥

```shell
ssh-keygen -t rsa -f ~/.ssh/id_rsa_bookonly -C "youremail"
```

然后就可以在 `.ssh` 文件夹中看到生成的 `id_rsa_bookonly` 和 `id_rsa_bookonly.pub` 文件，现在我们将 `id_rsa_bookonly.pub` 的文件内容添加到创建的 Github 账户中。

添加完之后需要编辑 `~/.ssh/config` 文件，添加以下配置：

```config
Host book.github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_book
```

配置文件还是很容易看懂的，它的 Host 设置为了 `book.github.com`，所以使用第二个账号下载和上传仓库的时候写法要进行改变

```shell
git clone git@github.com/User/Repo -> git clone git@book.github.com/User/Repo
git remote add origin git@github.com/User/Repo -> git remote add origin git@book.github.com/User/Repo
```
