---
title: Login to remote server without typing password (on Windows)
author: Ji Huang
date: '2019-11-19'
slug: login-to-remote-server-without-typing-password-on-windows
categories:
  - dry lab
tags:
  - server
  - linux
  - bioinformatics
  - tools
lastmod: '2019-11-19T11:23:54-05:00'
keywords: []
description: ''
comment: no
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

This post is to show you how to login to remote server without typing password every time. This is powered by [SSH-Key](https://www.ssh.com/ssh/key/). On windows, you can use [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) to generate SSH Keys.

<!--more-->

# 1. Download and install the Putty Suit

Putty is still my favorite SH client, stable and lightweight. It has all the functions I need from a SSH client. You can simply download from [Putty website](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Be sure to install the *Package* since you will need the *PuTTYgen* for generating the SSH-key.


# 2. Generate SSH Key

To generate SSH Key, simply opne *PuTTYgen* and click *Generate*. 


![](https://i.imgur.com/iunwTs2.png)

After 5s, they key is generated like one below. Simple click *Save private key* and save it in a secure location in your computer. You can assign *Key passphrase* which is basically a password to increase security. However, if you set *key passphrase*, you will need to type it everytime when login to the server. 

![](https://i.imgur.com/x0jrRQo.png)


# 3. Set up the server

To set up the server, copy the SSH key from the red box and paste it to the `.ssh/authorized_keys` file. On sever, you can edit the file by `vim`: `vim ~/.ssh/authorized_keys`.


# 4. Set up local PuTTY

Load you server from PuTTY and Click *SSH--Auth* on the left panel. Load the saved SSH Key file from Step 2.

![](https://i.imgur.com/bUtnvhp.png)


Go back to your *Session* and save the setting.


**Now you are good to go!** 


Bonus: If you use SFTP/FTP add-in in Sublime Text, you can simple set up the sever by pointing to the same SSH Key file.







