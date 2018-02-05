---
title: Edit remote server using Sublime Text
author: Ji Huang
date: '2018-02-05'
slug: edit-remote-server-using-sublime-text
categories:
  - dry lab
tags:
  - text-editor
  - misc
lastmod: '2018-02-05T16:03:53-05:00'
keywords: []
description: ''
comment: yes
toc: yes
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

Today I was saw a [post] in 2014 naming "[Sublime Text, Putty, and You][1]" that connectingn Putty with Sublime Text so that you can edit remote server files using Sublime text. This is a great method since I'm always struggling with Vim commands. I tried several times to adapt to Vim, but sorry just can't. 

# Previous solution

Before I used [SFTP package][2] to edit remote files. It works OK, but need extra effort to find the desired files from pop window like the one below.

<!--more-->

![Imgur](https://i.imgur.com/PYz5Qy5.jpg)

I also wrote a post before what package I used in Atom, [FTP and markdown packages in Atom][3]. The only problem I have with Atom is: it is VERY slow. "Slow" I mean from double click to actually typing. For that reason, I mostly used [Notepad++]. It's fine if you like to open Atom window all the time, but I personaly like to close un-used windows/tabs. Sublime Text has a comparable speed with Notepad++, though a little lag. But with much more features, I can take it.

# New solution Using rmate

I did the following to connect Sublime Text with Server.

## 1. Install rmate on server

Rmate can set up a tunnel between server and local computer. I used Rubygems to install, following [official instruction][5]. The difference is I have to install for user by `user-install`. You can find more detail from [Rubygem FAQ][6].

```shell
# add --user-install
gem install rmate --user-install
``` 

By default, it was installed in `~/.gem/ruby/1.9.1/bin`. Then need to add that directory into **PATH**.

```shell
# add the following code in ~/.bashrc. Restart session.
if which ruby >/dev/null && which gem >/dev/null; then
    PATH="$(ruby -rubygems -e 'puts Gem.user_dir')/bin:$PATH"
fi
```

Now `rmate` is a excutable command.


## 2. Install rsub package on Sublime Text

To install [rsub][7] on Sublime Text, the easier way is to install through [Package Control][8]. I skip this part, shoud be very easy. Just `Ctrl + Shift + P`, then `Install Package` and `rsub`.

You can see the package default setting from *Package Settings*. It uses port **52698** by default. See code below.

```
/*
    rsub default settings
*/
{
    /*
        rsub listen port
    */
    "port": 52698,

    /*
        rsub listen host

        WARNING: it's NOT recommended to change this option,
        use SSH tunneling instead.
    */
    "host": "localhost"
}

```


## 3. Connect rmate with Sublime Text in Putty.

So far we set up two parts: server and local, however we did not connect them. If you type `rmate test.txt`, you probably will get the following error `Error connecting to ‘localhost:52698’: Connection refused - connect(2)`.


To connech SHH tunnels, first load saved session for your server.


Next, Click `SSH` --> `Tunnels`. Put `52698` in `Source port` and `localhost:52698` in `Destination`. Don't forget to choose `remote`. 

![Imgur](https://i.imgur.com/JH4uU3R.jpg)

Then, click `Add`, you will see the information in the blank above. Now go back to your session and `Save`, then you don't need to config this everytime.

![Imgur](https://i.imgur.com/Dn7VOdH.jpg)

Here you go! You are all set!!

## 4. Test a file. 

Let's try it. Create a *test.txt* file.

```shell
jhuang@pauper:~$ cat test.txt
can you see me in Sublim text?
```

To edit it, make sure the Sublime Text is opened in your local machine. Then type `rmate test.txt`. You should be able to see the file open in your editor, took less than a second for me.

Added one a sentence, `Ctrl + S` and close the file.

```shell
jhuang@pauper:~$ cat test.txt
can you see me in Sublim text?

Yes I can see it! Awesome!!
```

Whoo!! Done!

I'm very happy about it. Hope you find it's helpful too. [This post][9] on setting instructions is also very helpful. Thanks to the author.




[1]: https://blog.cs.wmich.edu/sublime-text-putty-and-you/
[2]: https://wbond.net/sublime_packages/sftp
[3]: http://jhuang.netlify.com/post/2017-02-20-atompackage1/
[4]: https://notepad-plus-plus.org/
[5]: https://github.com/textmate/rmate
[6]: http://guides.rubygems.org/faqs/
[7]: https://packagecontrol.io/packages/rsub
[8]: https://packagecontrol.io/
[9]: http://www.martinrowan.co.uk/2015/07/live-editing-raspberry-pi-files-remotely-windows-pc-using-sublime-text-rsub-putty/
