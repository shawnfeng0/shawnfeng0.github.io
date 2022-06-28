---
title: "git使用ssh clone时提示Permission denied (publickey)失败"
categories: git
tags: git ssh
---

使用git clone ssh链接时，如果提示Permission denied (publickey)失败，先确认是否添加了ssh-key，如果没有添加，可以使用如下命令添加：

```shell
ssh-keygen -t ed25519
```

如果已经添加ssh-key，可能是因为本地的ssh版本太新，默认不支持rsa-sha1算法了（ssh-keygen不带参数就会默认使用的rsa-sha1算法），也请参考上面的命令重新生成新的key。不是ed25519也行，只要不是rsa-sha1，参考文章：

<https://confluence.atlassian.com/bitbucketserverkb/ssh-rsa-key-rejected-with-message-no-mutual-signature-algorithm-1026057701.html>

<https://gerrit-documentation.storage.googleapis.com/Documentation/2.12.3/error-permission-denied.html>

可使用以下命令调试git clone时ssh失败的问题：

```shell
GIT_SSH_COMMAND="ssh -vvv" git clone xxxxx.xxx
```

根据输出信息自行排查。
