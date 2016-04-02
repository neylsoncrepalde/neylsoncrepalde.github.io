---
layout: post
title: O Cenário Político
subtitle: Algumas análises rápidas
tags: [Content Analysis, Twitter, Politics]
author: Neylson Crepalde
date: 2016, March 17
---

O cenário político brasileiro parece estar dentro de um liquidificador com as últimas notícias publicadas ontem (16 de março de 2016)
sobre a nomeação de Lula como Ministro da Casa Civil e da sua conversa com a presidente Dilma divulgada em rede nacional.

Usando os pacotes **twitteR** e **tm**, fiz uma busca no Twitter pela palavra-chave *Lula* a fim de monitorar a percepção das pessoas
nessa mídia social bem no "calor do momento". Pretendo, em breve, publicar um post comentando o código utilizado para a análise com um
breve tutorial.

A busca retornou 2000 tweets. Gerei 2 *wordclouds*, o primeiro com dados coletados às 22:57 de ontem e o segundo com tweets coletados às 00:19 de hoje.

![lula1](/img/wordcloud_lula.jpeg) ![lula2](/img/wordcloud_lula2.jpeg)

Gerei também outro *wordcloud* fazendo uma busca por **#ocupabrasilia**. Este termo está em segundo lugar nos *trends* do Twitter neste momento.

![#ocupabrasilia](/img/wordcloud_ocupabrasilia.jpeg)

Depois disso, fiz uma clusterização hierárquica dos termos mais presentes agrupando-os em 7 grandes grupos.

![hclust](/img/dendogram_lula.jpeg)

Após exportar a matriz de termos para um arquivo .csv, usei o software **Ucinet** para gerar redes das palavras. A primeira rede foi gerada a partir dos *overlaps* dos termos. O tamanho dos vértices representa a centralidade de grau de cada palavra.

![rede_overlaps](/img/rede_lula_palavras_uci.jpg)

Para gerar a segunda rede, usei as correlações entre os termos. O tamanho continua sendo a centralidade de grau.

![rede_cor](/img/rede_lula_palavras_cor_uci.jpg)

Tentei, com essas análises rápidas e incipientes, captar a opinião geral dos usuários do Twitter sobre esse momento político histórico que vive o Brasil. Espero que os grafos sejam úteis.

Abraços a todos.
