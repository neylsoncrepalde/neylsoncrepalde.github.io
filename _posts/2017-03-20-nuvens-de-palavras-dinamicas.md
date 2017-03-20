---
layout: post
title: Nuvens de palavras dinâmicas com wordcloud2 e corrigindo encoding
author: Neylson Crepalde
date: 2017, March 20
tags: [R, Wordloud2, Encoding]
---

Olá. Hoje vamos utilizar o pacote wordloud2 para plotar nuvens de palavras dinâmicas em html. Esse recurso é bastante útil numa análise preliminar/descritiva de *corpora* textuais. Para isso, vamos utilizar uma pergunta aberta de um questionário de pesquisa.

A empresários de um determinado local de Belo Horizonte, foi perguntado quais são as principais dificuldades encontradas na contratação de jovens. Ao trabalhar com análise de textos, é muito comum nos depararmos com problemas de encoding. Palavras com acentos, cedilha (ç) ficam desconfiguradas comprometendo a apresentação dos resultados. Eu e meu parceiro Marcus encontramos uma "funçãozinha mágica" no R que nos ajuda a resolver esse problema -&gt; `enc2native`.

A função `enc2native()` preserva o encoding original dos dados quando há mudanças de encoding internas nos códigos (é o caso da função Corpus do pacote tm). Costumo sempre trabalhar com o encoding "UTF-8", um encoding universal que facilita o uso de caracteres especiais. Vamos carregar primeiro os dados

``` r
library(data.table)
library(bit64)
library(magrittr)
library(tm)
library(wordcloud)
```

``` r
setwd("C:/Users/x6905399/Documents/RASTREIA/JUVENTUDES")
dados <- fread('emprego_empreendedorismo.csv', encoding="UTF-8") %>% 
  as.data.frame(., stringsAsFactors=F) %>% .[-2,]

pal2 = brewer.pal(8,'Dark2')

dificuldades = dados[,48] %>% tolower %>% 
  removePunctuation %>% removeWords(., stopwords('pt'))
```

Vejamos como ficam as nuvens de palavras sem a utilização da função.

``` r
wordcloud(dificuldades, min.freq=3,max.words=100, random.order=F, colors=pal2)
```

![](/img/post_wordcloud2_files/figure-markdown_github/wordcloud%20sem-1.png)

Imagine apresentar para seu chefe ou colegas de trabalho uma nuvem de palavras cheia de erros de encoding como essa!!!

A função `enc2native()` resolve o problema!

``` r
wordcloud(enc2native(dificuldades), min.freq=3,max.words=100, random.order=F, colors=pal2)
```

![](/img/post_wordcloud2_files/figure-markdown_github/unnamed-chunk-2-1.png)

Agora vamos deixar essa nuvem realmente atrativa usando o pacote wordcloud2 para gerar um html interativo. Para isso, é preciso algum trabalho transformando o vetor de texto num data.frame com duas colunas: palavras e contagem

``` r
library(wordcloud2)

corpus = Corpus(VectorSource(enc2native(dificuldades)))
#preparando o df para wordcloud2
tdm.word = TermDocumentMatrix(corpus) %>% as.matrix
tdm.df = data.frame(words = rownames(tdm.word),
                    freq = apply(tdm.word,1,sum))
```

<iframe src="http://neylsoncrepalde.github.io/word2.html" width="850" height="425" seamless scrolling="no" frameBorder = "0"></iframe>

Muito elegante, hum? É possível ainda plotar wordclouds em letras específicas ou dentro de uma figura. Para mais informações, veja este link: <https://cran.r-project.org/web/packages/wordcloud2/vignettes/wordcloud.html>

Até a próxima!
