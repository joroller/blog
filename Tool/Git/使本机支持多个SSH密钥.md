# 使本机支持多个SSH密钥

## 生成多个密钥对

使用命令：

```shell
$ ssh-keygen -t rsa -C "your_email@youremail.com"
```

运行命令后，跟着向导执行操作以下步骤：

1 Generating public/private rsa key pair.

2 Enter file in which to save the key (xxx/id_rsa):

3 Created directory 'xxx/.ssh'.

4 Enter passphrase (empty for no passphrase):

5 Enter same passphrase again:

**在第2步需要注意，给出不同的名称**。其他步骤如果不需要配置，则可以直接按下Enter建。

查看秘钥：找到上面步骤2所提示存位置，用编辑器打开.pub后缀的文件。

## 配置 config

**创建config文件**：

在以下目录中：

linux: ~/.ssh/
windows: C:\Users\{用户名}\.ssh\

创建config文件（无后缀名），并且配置ssh key的路由策略，策略如下：

这里举例为gitlab.com与github.com分别配置，并且已经为它们生成了对应的key了，两个网站的用户名均为admin。

```
Host gitlab.com
    HostName gitlab.com
    User admin
    PreferredAuthentications publickey
    IdentityFile C:\\Users\\\admin\\.ssh\\id_rsa_gitlab
# github
Host github.com
    HostName github.com
    User admin
    PreferredAuthentications publickey
    IdentityFile C:\\Users\\\admin\\.ssh\\id_rsa_github
```

**配置项说明**：

**Host Host**: 可以看作是一个你要识别的模式，对识别的模式，进行配置对应的的主机名和ssh文件

**HostName**:  要登录主机的主机名

**User**:  登录名

**PreferredAuthentications**:  可选值有publickey, password, keyboard-interactive

**IdentityFile**:  指明上面User对应的key文件路径

## 设置远程仓库上的公钥

将复制出的公钥，设置到具体参考所使用的仓库产品。

Github：https://github.com/settings/keys

## 测试SSH连接

使用命令：

```shell
$ ssh -T git@github.com
```

如果得到如下输出，则表示成功连接：
```
Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.
```