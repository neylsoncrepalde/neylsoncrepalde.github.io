---
layout: post
title: Ocupação na UFMG e intervenção policial - Twitter
author: Neylson Crepalde
date: 2016, November 18
tags: [rstats, Social Network Analysis, Igraph, PEC 55, MP Ensino Médio, PMMG]
---

É do conhecimento de todos que hoje, 18/11/2016, a PM de Minas Gerais reprimiu com violência (balas de borracha e gás lacrimogênio) manifestantes que bloqueavam a Avenida Antônio Carlos na frente da UFMG. O fato está sendo sendo bastante comentado nas mídias sociais embora não receba atenção da grande mídia. Utilizando ferramentas de *Big Data*, investiguei como o fato está circulando no Twitter. Hoje às 20:50 a palavra **UFMG** constava como *trending topic*. Fizemos uma busca por 3 mil tweets com a palavra UFMG.

``` r
library(twitteR)
library(wordcloud)
library(tm)
library(plyr)

consumer_key <- "XXXXXXXXXXXXXXXXXXXXXX"
consumer_secret <- "XXXXXXXXXXXXXXXXXXXXXX"
access_token <- "XXXXXXXXXXXXXXXXXXXXXX"
access_secret <- "XXXXXXXXXXXXXXXXXXXXXX"

setup_twitter_oauth(consumer_key,
                    consumer_secret,
                    access_token,
                    access_secret)

tweets <- searchTwitter("UFMG", n=3000)
bd <- ldply(tweets, function(t) t$toDataFrame() )
```

Vamos realizar uma investigação preliminar nos tweets através de uma nuvem de palavras.

``` r
text <- sapply(tweets, function(x) x$getText())
```

``` r
corpus <- Corpus(VectorSource(text))
f <- content_transformer(function(x) iconv(x, to='latin1', sub='byte'))
corpus <- tm_map(corpus, f)
corpus <- tm_map(corpus, content_transformer(tolower))
corpus <- tm_map(corpus, removePunctuation)
corpus <- tm_map(corpus, function(x)removeWords(x,stopwords("pt")))
pal2 <- brewer.pal(8,"Dark2")
wordcloud(corpus, min.freq=2,max.words=100, random.order=F, colors=pal2)
title(xlab = "Twitter, 18/11/2016, 20:58")
```

![](/img/twitter_UFMG_files/figure-markdown_github/unnamed-chunk-6-1.png)

A maioria das palavras mais usadas parece se posicionar contra o fato. Palavras como **invadir, atirando, gás, bombas** aparecem.

Vamos investigar a emergência de alguns assuntos através da clusterização hierárquica.

``` r
tdm <- TermDocumentMatrix(corpus)
tdm <- removeSparseTerms(tdm, sparse = 0.94)
df <- as.data.frame(inspect(tdm))
dim(df)
```

``` r
df.scale <- scale(df)
d <- dist(df.scale, method = "euclidean")
fit.ward2 <- hclust(d, method = "ward.D2")
plot(fit.ward2)

rect.hclust(fit.ward2, k=8)
```

![](/img/twitter_UFMG_files/figure-markdown_github/unnamed-chunk-9-1.png)

Vamos tentar aprofundar nossas investigações gerando uma rede semântica com essas palavras. Para facilitar a análise vamos retirar a palavra UFMG.

``` r
matriz <- as.matrix(df)

library(igraph)
g = graph_from_incidence_matrix(matriz)
p = bipartite_projection(g, which = "FALSE")
V(p)$shape = "none"
deg = degree(p)
p = delete_vertices(p, 'ufmg')

plot(p, vertex.label.cex=deg/20, edge.width=(E(p)$weight)/100, 
     edge.color=adjustcolor("grey60", .5),
     vertex.label.color=adjustcolor("#005d26", .7),
     main = "Twitter - UFMG", xlab = "18/11/2016, 20:58")
```

![](/img/twitter_UFMG_files/figure-markdown_github/unnamed-chunk-10-1.png)

Observando o dendograma é possível identicar três assuntos principais: o primeiro, ocupando a posição mais central do grafo, relaciona-se ao confronto da polícia com os estudantes. Palavras como **balas, borracha, professores, manifestantes** e **midianinja** aparecem conectadas aqui. A parte de cima com palavras com ligações mais fortes parecem ser de tweets que fazem denúncia no momento em que o evento estava acontecendo. Palavras como **urgente, jogando, invadindo, entraram** aparecem aqui. Na parte inferior do grafo, parece emergir um terceiro assunto relacionado à nota de repúdio divulgada pela UFMG à ação policial.
