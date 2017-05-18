---
layout: post
title: O Pronunciamento de Michel Temer no Twitter
author: Neylson Crepalde
date: 2017, May 18
tags: [R, Wordloud, Twitter, Data Mining]
---

Hoje foi um dia bastante importante no Brasil, o dia em que o atual presidente Michel Temer quaaaaaaaase renunciou após o vazamento de vídeos em que ele combina propina para Eduardo Cunha, ex-presidente da Câmara dos Deputados que está preso, para que fique calado. O assunto é *trending topic* mundial no twitter. Uma hora após seu pronunciamento, vamos investigar rapidamente o que está sendo falado sobre Temer nessa mídia social. Para isso, coletamos 10000 tweets com as palavras-chave "michel temer".

Em [outro post](http://neylsoncrepalde.github.io/2016-03-18-analise-de-conteudo-twitter/) mostramos como fazer a coleta dos dados na API do twitter. Vou apenas postar um exemplo de como fica o código.

```r
library(twitteR)
library(wordcloud)
library(tm)
library(plyr)
library(stringr)
library(magrittr)

consumer_key <- "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
consumer_secret <- "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
access_token <- "XXXXXXXXXXXXXXXXXXXXXXXXXXXX-XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
access_secret <- "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"

setup_twitter_oauth(consumer_key,
                    consumer_secret,
                    access_token,
                    access_secret)

tweets <- searchTwitter("Michel Temer", n=10000)
bd <- ldply(tweets, function(t) t$toDataFrame() )  # Salvando os tweets num banco de dados
View(bd)                                           # Ver o banco

text <- sapply(tweets, function(x) x$getText())
```

Vamos às análises. Quais são as palavras mais faladas nos tweets contendo "Michel Temer"? Depois de ter os dados no ambiente do R, vamos tranformá-lo num *corpus* textual para análise, retirar os *emojis* com um regex, transformar todas as palavras em minúsculas, retirar pontuação, retirar palavras que não são interessantes para a análise (preposições, conectivos, etc.) e, obviamente, retirar as palavras "michel" e "temer".

``` r
corpus <- Corpus(VectorSource(enc2native(text)))
corpus <- tm_map(corpus, function(x) gsub("[^[:alpha:][:space:]]*", "", x))
corpus <- tm_map(corpus, content_transformer(tolower))
corpus <- tm_map(corpus, removePunctuation)
corpus <- tm_map(corpus, function(x)removeWords(x,stopwords("pt")))
corpus <- tm_map(corpus, function(x)removeWords(x, c("michel","temer")))
```

Agora, vamos plotar uma nuvem de palavras:

``` r
library(RColorBrewer)
pal2 <- brewer.pal(9,"YlGnBu")
pal2 <- pal2[5:9]
wordcloud(corpus, min.freq=2,max.words=100, random.order=F, colors=pal2)
```

![](/img/post_twitter_temer_files/figure-markdown_github/unnamed-chunk-2-1.png)

Agora vamos investigar quem são as pessoas que mais aparecem nesses tweets, ou seja, quais são as contas mais retweetadas:

``` r
pessoas <- str_extract_all(text, "@\\w+") %>% unlist
wordcloud(pessoas, min.freq=2,max.words=100, random.order=F, colors=pal2)
```

![](/img/post_twitter_temer_files/figure-markdown_github/unnamed-chunk-3-1.png)

Agora, vamos investigar quais são as hashtags mais usadas:

``` r
hastags <- str_extract_all(text, "#\\w+") %>% unlist
wordcloud(hastags, min.freq=2,max.words=100, random.order=F, colors=pal2)
```

![](/img/post_twitter_temer_files/figure-markdown_github/unnamed-chunk-4-1.png)

Por hoje é só. Aguardem cenas dos próximos capítulos de \#BRASILIAOFCARDS!!!
