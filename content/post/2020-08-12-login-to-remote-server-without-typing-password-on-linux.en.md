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

<!--more-->

### 1. Generate local SSH key.

```shell
ssh-keygen
```

### 2. Copy the key to the remote server.

```shell
ssh-copy-id -i ~/.ssh/id_rsa.pub your@remote.server
```

### 3. login to the remote server from terminal

```shell
ssh your@remote.server
```

