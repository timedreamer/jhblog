---
title: Login to remote server without typing password (on Linux)
author: Ji Huang
date: '2020-08-12'
slug: login-to-remote-server-without-typing-password-on-linux
categories:
  - dry lab
tags:
  - server
lastmod: '2020-08-12T16:49:14-04:00'
keywords: []
description: ''
comment: no
toc: true
autoCollapseToc: false
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---


The method is very similar with Windows machine, but much easier, just two steps. The key is `ssh-keygen`. This works with WSL also.

Update: 2021-02-03

<!--more-->

### 1. Generate local SSH key.

```shell
ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/td/.ssh/id_rsa): /home/td/.ssh/ID_NAME_HERE
```

### 2. Copy the key to the remote server.

```shell
ssh-copy-id -i ~/.ssh/ID_NAME_HERE.pub your@remote.server
```

### 3. login to the remote server from terminal

```shell
ssh -i ~/.ssh/ID_NAME_HERE.pub your@remote.server
```

