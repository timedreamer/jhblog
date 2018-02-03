---
title: move site to blogdown
author: Ji Huang
date: '2018-02-02'
lastmod: '2018-02-03'
slug: move-site-to-blogdown
categories:
  - web
tags:
  - web
  - blog
  - science-writing
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
draft: false
---

Alright. I haven't write any blog posts for several weeks now, last post was on Octerber 24, 2017. Since then I've been busy with searching for postdocs and preparing another manuscript. Luckily I found a great lab to start my postdoc caree (I will update after I defend). Also, the manuscript was submited on the last day of Janunary 2018, figure crossed!

<!--more-->

So after all that been done and before I "officially" start writing my dissertation. I want to change my mind to something totally different. One project that on my mind for sometime is to use [blogdown][1] to write my own blog. The more I used R, the more I liked it. It's easy to get started, but need a lot of effort to be good at (I think all things are). But anyway, they idea that use RStudio, I used it almost everyday, to write blog and Github to do version control is really cool.

Here I described what I did for two days with a lot of unexpectedtrouble shooting. I mostly followed [this awesome tutorial][2] by Alison Hill.

# Set up RStudio environment

First, I need to setup my RStudio environment for the blog. You don't have to use RStdio, but since it's [highly recommended][3] by Yihui and I really like RStudio, so I did this first.

I first tried on my Windows 7 desktop. It looks very easy from blogdown book, however it took me a whole day and still can't fix. I think the HUGO was not installed corrected. Besides there are some issue with LiveReload. Sometimes only plain text in the `View` panel, sometimes nothing shows. Anyway, I then switched to RStudio server on pauper. I'm going to push the code on Gihub, so it doesn't matter what environment I use for now. I can clone the repo and re-start.


On RStudio server, the only problem I had is the LiveReload just show plain text instead of the webpage. The solution is to add `relativeurls = true` in **config.toml** file. I found it on [Github][4], solved by Yihui. After that, everything works smoothly so far!

## Choose a theme

In [blogdown book][5], they recommended several themes, but they all have something that doesn't fit my taste. Not because they are bad, just I know very limited CSS and JavaScript, so don't want to spend a lot of time on it. I searched the HUGO theme and found [Even][6] looks really good. 

These are two features I like:

1. mininal design. I don't need a lot of fancy features, just keep it simple.
2. category and tags. Since I used **tags** to organize Evernote, I found tag is very helpful at organizing. I know I won't have many posts, but tags is still very handy. 

Here is a screenshot.

![Imgur](https://i.imgur.com/UC3toN8.jpg)

I installed by `new-site` function.

```R
blogdown::new_site(theme = "olOwOlo/hugo-theme-even", theme_example = TRUE)
```

After this command, you can preview the website by `blogdown::server_site()`.

# Workflow

The workflow I used is suggest by blogdown. RStudio --> Git --> Github --> Netlify. I found it's easier and more intuitive than Github page.

## Set up Github credentials

One thing that can make your life much easier is to set up the [Cache credentials for HTTPS][7] (please see the [Happy Git and GitHub for the useR][8] for more details, I follow their instructions). In this way, you don't need to type your username and password everytime you push or pull to the Github (To be honest, I don't exactly how it works, but easier than SSH keys).

1. Make sure the directory is connected to the remote (Github).

```shell
git remote -v
``` 

Output is like this in my directory:
```
origin  https://github.com/timedreamer/jhblog.git (fetch)
origin  https://github.com/timedreamer/jhblog.git (push)
```

2. Make sure the Git version is above 1.7.10

```shell
git --version
# on pauper
# git version 2.9.4
```

3. To turn on the credential helper.

I did this in pauper, so used Linux command.

```shell
git config --global credential.helper 'cache --timeout=10000000'
```

In the [orignial tutorial][7], it says: 
> to store your password for ten million seconds or around 16 weeks, enough for a semester.

4. Push and entering username/password.

Now you can push it to Github. It will ask your username and password for once. After than you can just `git push`.
```shell
git push -u origin master # just enter username/password once.
git push # for next 16 weeks, just need git push.
```

## Connect to Netlify.

This step is pretty straightforward, just follow Alison's [tutorial][2]. The only thing I did different is the Even theme needs HUGO 0.35+, so I have to set up this.

You need to add a `New Variable` as `HUGO` and set to `0.35`. The Netlify will install that version for your. Really neat!

![Imgur](https://i.imgur.com/jmQHbNT.png)

You should rename the name, I renamed with `jhuang`. So whenever you update the Github, for example upload a new post, the Netlify will deploy the website again. It came very fast for me, less than a minute.


# Modify YAML meta-data. 

To transfer posts from Jekyll to HUGO (Thank God I wrote in Markdown!), I copied all posts from `timedreamer.github.io\_posts` to `jhblog/content/post`. Then manually edited the YAML meta-data for each posts. I'm sure there are ways to automatic this, but I just have ~10 posts, so I can do it very quick.


This is the new meta-data looks like:

```yaml
---
title:  "Site moved to Jekyll"
date: 2017-10-01
lastmod: 2018-02-02
draft: false
tags: ["jekyll", "web"]
categories: ["web"]
---

```

# Summary

That's it! I'm very happy that I moved to HUGO and blogdown. Alison in the posts also recommedn `rbind.io` domain, but I don't know how it works so far. I will keep the Netlify for sometime. Netlify provides this awesome free service, I think we should give some credits to it by using the Netlify domain.




[1]:https://bookdown.org/yihui/blogdown/
[2]:https://alison.rbind.io/post/up-and-running-with-blogdown/
[3]:https://bookdown.org/yihui/blogdown/rstudio-ide.html
[4]:https://github.com/rstudio/blogdown/issues/124
[5]:https://bookdown.org/yihui/blogdown/other-themes.html
[6]:https://github.com/olOwOlo/hugo-theme-even
[7]:http://happygitwithr.com/credential-caching.html
[8]:http://happygitwithr.com/





