# Failed to connect to github.com port 443

## 问题描述

```
Failed to connect to github.com port 443:connection timed out
```

## 解决方法

使用以下命令取消代理：

```shell
$ git config --global --unset http.proxy

$ git config --global --unset https.proxy
```