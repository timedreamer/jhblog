---
title:  "FTP and markdown packages in Atom"
date: 2017-02-25
tags: ["text-editor"]
categories: ["misc"]
---

My main working desktop is Windows 7 and I use Bitvise SHH client to connect to department server which running Ubuntu 14.04. I usually use [Notpad++](https://notepad-plus-plus.org/){:target="_blank"} ftp function to connect with server and do some text copy/editing. It works fine, I do not have any complains about it.

<!--more-->

However, I started using **[Atom](https://atom.io/){:target="_blank"}** last year and I really like it. With full support for git and Markdown, it definitely better than Notepad++ (I still use Notepad++ to open plain text file on Windows). So I was wondering whether I can connect to server using Atom, so I can stay in Atom for most of time.

By the way, somehow on my Windows7 desktop, the default Mardown Preview can't do live update. So I changed to [Markdown Preview Enhanced](https://atom.io/packages/markdown-preview-enhanced){:target="_blank"}. That works pretty well.

The problem for Markdown Preview Enhanced is, it does not support [kramdown](https://kramdown.gettalong.org/){:target="_blank"}. Actually, I did not find any packages can perfectly support it. The [language-markdown](https://atom.io/packages/language-markdown){:target="_blank"} has some support, but definitely not perfect.

For example it has this problem:
![Imgur](https://i.imgur.com/rWPzylo.png)


### Use [Atom Commander](https://atom.io/packages/atom-commander){:target="_blank"} for FTP server.
updated 3/17/2017

### Use [Ftp-Remote-Edit](https://atom.io/packages/ftp-remote-edit){:target="_blank"} for FTP server.
updated 9/27/2017

It has a nice view and showing on the left/right side of the dock. Better than Atom Commander that showing at the bottom of the window.
