---
title: Ubuntu 切换阿里云源
urlname: pkhv6m
date: 2020-05-03 14:37:08 +0800
tags: []
categories: []
---

备份 cp /etc/apt/sources.list /etc/apt/sources.backup.list

打开 vim /etc/apt/sources.list

vim 输入 ：%d 回车，删除原有内容

复制下面内容

```markdown
#阿里源
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

更换好源之后执行下方命令更新：
sudo apt-get update
sudo apt-get upgrade
