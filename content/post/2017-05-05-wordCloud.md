---
title:  "word cloud figure for Karen "
date: 2017-05-05
lastmod: 2018-02-02
draft: false
tags: ["R", "wordCloud", "visualization"]
categories: ["dry lab"]
toc = true
---

This post, I wanted to create a word cloud (also called tag cloud) figure for Karen's paper. I chose papers with Karen's name since 2008, copied their title and abstract and saved in a plain text file called **abstract_Karen2008.txt**.

<!--more-->

For the creation of figure, I used **R** and two packages **[tm][1]** (text mining) and **[wordcloud2][2]**. There is another package **wordcloud**, but it has been updated since 2013, so I did not use that one.

The basic idea is:

1. read text.
2. remove some unnecessary words and do some modifications.
3. generate a dataframe with *word* and their *frequency*.
4. draw word cloud figure.

First is to load packages and import text.

```r
require(tm);require(wordcloud2)
# load text
text <- readLines("abstract_Karen2008.txt")
# Load the data as a corpus
docs <- Corpus(VectorSource(text))
# if need to see data, use inspect(docs)
# if need to manual change some words to space, use following function.
#toSpace <- content_transformer(function (x , pattern) gsub(pattern, " ", x))
#docs <- tm_map(docs, toSpace, ",")
#docs <- tm_map(docs, toSpace, "~")
#docs <- tm_map(docs, toSpace, "\\(")
#docs <- tm_map(docs, toSpace, "\\)")
#docs <- tm_map(docs, toSpace, "\\.")
```

Second, clean unnecessary words.

```r
# Convert the text to lower case
docs <- tm_map(docs, content_transformer(tolower))
# Remove numbers
docs <- tm_map(docs, removeNumbers)
# Remove english common stopwords
docs <- tm_map(docs, removeWords, stopwords("english"))
# Remove your own stop word
# specify your stopwords as a character vector
rm_words <- c("bp","diatoms","also","via","many","near","genes",
              "plants","can","results","using","hkme","within","likely",
              "reveals","may")
docs <- tm_map(docs, removeWords, rm_words)
# Remove punctuations
docs <- tm_map(docs, removePunctuation)
# Eliminate extra white spaces
docs <- tm_map(docs, stripWhitespace)
```

Third, generate a word frequency dataframe.

```r
# generate the word frequency dataframe
dtm <- TermDocumentMatrix(docs)
m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
d$word <- as.character(d$word)
d["dna",][1] <- "DNA"
d["rna",][1] <- "RNA"
d["mop",][1] <- "MOP"
d$word <- as.factor(d$word)
head(d, 5)
```
![Imgur](http://i.imgur.com/Pm12tju.png)

Fourth, plotting.

```r
# draw wordcloud with all names horizontal.
wordcloud2(data = d,size = 0.5,minRotation = 0, maxRotation = 0, minSize = 10,
           rotateRatio = 1)
```
![Imgur](http://i.imgur.com/PUrcAwi.png)


[1]:https://cran.r-project.org/web/packages/tm/
[2]:https://cran.r-project.org/web/packages/wordcloud2/
