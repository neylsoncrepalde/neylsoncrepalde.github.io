---
layout: post
title: Transição Demográfica no Brasil
author: Neylson Crepalde
date: 2016, October 9
tags: [R programming, rstats, Transição Demográfica, Pirâmide etária]
---

Vamos usar o pacote `rChart` de Ramnath Vaidyanathan para montar um gráfico animado da pirâmide etária no Brasil desde 1970 até o ano 2050. É necessário ter os pacotes `devtools`, `reshape2`, `plyr` e `XML` instalados. Se não, execute esta linha de comando

``` r
install.packages(c('XML', 'reshape2', 'devtools', 'plyr'))
```

Depois disso, carregue o pacote `devtools` para que façamos a instalação do pacote.

``` r
library(devtools)
```

``` r
install_github('ramnathv/rCharts@dev')
```

Agora, vamos carregar algumas funções para raspagem e plotagem desses dados escritas por Kyle Walker. Se você estiver usando o RStudio (altamente recomendável), use este comando:

``` r
source('https://raw.githubusercontent.com/walkerke/teaching-with-datavis/master/pyramids/rcharts_pyramids.R')
```

Para um tutorial completo do uso do pacote, acesse [este post](http://walkerke.github.io/2014/06/rcharts-pyramids/). Por ora, vamos focar apenas em nosso objetivo: plotar a transição demográfica no Brasil. Vamos plotar as pirâmides etárias do Brasil do ano 1970 até 2050 com intervalos de 5 anos. Veja como a população brasileira se comporta no período.

Transição Demográfica no Brasil
===============================

``` r
dPyramid('BR', seq(1970, 2050, 5), colors = c('blue', 'red'))
```

<iframe src="http://neylsoncrepalde.github.io/img/brasil_trasicao_demografica.html" width="850" height="425" seamless scrolling="no" frameBorder = "0"></iframe>
