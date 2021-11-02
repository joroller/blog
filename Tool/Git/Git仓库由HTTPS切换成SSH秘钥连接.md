# Git仓库由HTTPS切换成SSH秘钥连接

## 生成密钥对

使用命令：

```shell
$ ssh-keygen -t rsa -C "your_email@youremail.com"
```

运行命令后，跟着向导执行操作以下步骤：

1 Generating public/private rsa key pair.

2 Enter file in which to save the key (/home/you/.ssh/id_rsa):

3 Created directory '/c/Users/Administrator/.ssh'.

4 Enter passphrase (empty for no passphrase):

5 Enter same passphrase again:

如果不需要配置以上信息，则可以直接按下Enter建。

查看秘钥：找到上面步骤2所提示存位置，用编辑器打开id_rsa.pub，复制出公钥。

## 设置远程仓库上的公钥

将复制出的公钥，设置到具体参考所使用的仓库产品。

Github：https://github.com/settings/keys


## 本地将Git的 remote url 修改为SSH链接

查看当前使用的协议：

```shell
$ git remote -v
```

如果使用的是HTTPS协议，那么显示结果如下：

```shell
origin https://github.com/someaccount/someproject.git (fetch)
origin https://github.com/someaccount/someproject.git (push)
```

设置以远程url命令：

```shell
$ git remote set-url origin git@github.com:someaccount/someproject.git
```

复制链接，然后使用命令`git remote set-url`来调整url。