---
layout: post
title: Análise de Redes com R
author: Neylson Crepalde - GIARS
date: 2016, March 15
tags: [Análise de Redes Sociais, visualização]
---
Este tutorial visa esclarecer como realizar alguns passos da análise de redes sociais usando o ambiente R.

Primeiro, carregamos os pacotes **sand** que contém os bancos de dados que usaremos e **igraph** usado para análises. O pacote **sand** carrega automaticamente o pacote **igraph** e, por isso, não precisamos chamá-lo novamente. Se houver um erro indicando que o pacote não está instalado, digite o código

``` r
install.packages("sand")
```

``` r
library(sand)
```

Vamos começar a trabalhar com algumas visualizações da rede de associações de advogados em New England (LAZEGA, 2001). Para conhecer melhor os dados digite o código

``` r
help(lazega)
```

Primeiro, vamos atualizar os dados para que a nova versão do **igraph** não fique colocando *warnings* desnecessários. Depois, vamos gerar 3 visualizações, a primeira com um layout *default*, a segunda com um layout *Kamada Kawai* e a terceira com um layout *Fruchterman-Reingold*. Usaremos também o comando **vertex.label=NA** para especificar que não queremos imprimir labels nos vértices da rede e **vertex.size** para especificar o tamanho dos vértices.

``` r
lazega <- upgrade_graph(lazega)
plot(lazega, vertex.label=NA)
```

![](/img/unnamed-chunk-4-1.png)

``` r
plot(lazega, layout=layout_with_kk(lazega), vertex.label=NA, vertex.size=10)
```

![](/img/unnamed-chunk-4-2.png)

``` r
plot(lazega, layout=layout_with_fr(lazega), vertex.label=NA, vertex.size=8)
```

![](/img/unnamed-chunk-4-3.png)

Às vezes é necessário investigar os arcos existentes no grafo. Para isso use o comando

``` r
E(lazega)
```

    ## + 115/115 edges (vertex names):
    ##  [1] V1 --V17 V2 --V7  V2 --V16 V2 --V17 V2 --V22 V2 --V26 V2 --V29
    ##  [8] V3 --V18 V3 --V25 V3 --V28 V4 --V12 V4 --V17 V4 --V19 V4 --V20
    ## [15] V4 --V22 V4 --V26 V4 --V28 V4 --V29 V4 --V31 V5 --V18 V5 --V24
    ## [22] V5 --V28 V5 --V31 V5 --V32 V5 --V33 V6 --V24 V6 --V28 V6 --V30
    ## [29] V6 --V31 V6 --V32 V7 --V18 V9 --V12 V9 --V16 V9 --V29 V10--V24
    ## [36] V10--V26 V10--V29 V10--V31 V10--V34 V11--V17 V12--V15 V12--V16
    ## [43] V12--V17 V12--V19 V12--V26 V12--V29 V12--V34 V13--V31 V13--V33
    ## [50] V14--V16 V14--V17 V14--V25 V14--V28 V14--V30 V14--V32 V15--V16
    ## [57] V15--V19 V15--V20 V15--V22 V15--V24 V15--V26 V15--V29 V15--V32
    ## [64] V15--V35 V15--V36 V16--V17 V16--V22 V16--V26 V16--V27 V16--V29
    ## + ... omitted several edges

Para obter a matriz de adjacências, use

``` r
get.adjacency(lazega)
```

    ## 36 x 36 sparse Matrix of class "dgCMatrix"

    ##    [[ suppressing 36 column names 'V1', 'V2', 'V3' ... ]]

    ##                                                                          
    ## V1  . . . . . . . . . . . . . . . . 1 . . . . . . . . . . . . . . . . . . .
    ## V2  . . . . . . 1 . . . . . . . . 1 1 . . . . 1 . . . 1 . . 1 . . . . . . .
    ## V3  . . . . . . . . . . . . . . . . . 1 . . . . . . 1 . . 1 . . . . . . . .
    ## V4  . . . . . . . . . . . 1 . . . . 1 . 1 1 . 1 . . . 1 . 1 1 . 1 . . . . .
    ## V5  . . . . . . . . . . . . . . . . . 1 . . . . . 1 . . . 1 . . 1 1 1 . . .
    ## V6  . . . . . . . . . . . . . . . . . . . . . . . 1 . . . 1 . 1 1 1 . . . .
    ## V7  . 1 . . . . . . . . . . . . . . . 1 . . . . . . . . . . . . . . . . . .
    ## V8  . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
    ## V9  . . . . . . . . . . . 1 . . . 1 . . . . . . . . . . . . 1 . . . . . . .
    ## V10 . . . . . . . . . . . . . . . . . . . . . . . 1 . 1 . . 1 . 1 . . 1 . .
    ## V11 . . . . . . . . . . . . . . . . 1 . . . . . . . . . . . . . . . . . . .
    ## V12 . . . 1 . . . . 1 . . . . . 1 1 1 . 1 . . . . . . 1 . . 1 . . . . 1 . .
    ## V13 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 . 1 . . .
    ## V14 . . . . . . . . . . . . . . . 1 1 . . . . . . . 1 . . 1 . 1 . 1 . . . .
    ## V15 . . . . . . . . . . . 1 . . . 1 . . 1 1 . 1 . 1 . 1 . . 1 . . 1 . . 1 1
    ## V16 . 1 . . . . . . 1 . . 1 . 1 1 . 1 . . . . 1 . . . 1 1 . 1 . . 1 . 1 . 1
    ## V17 1 1 . 1 . . . . . . 1 1 . 1 . 1 . . 1 . . 1 . 1 1 1 . 1 1 . . . . 1 . .
    ## V18 . . 1 . 1 . 1 . . . . . . . . . . . . . . . . . . . . 1 . . 1 1 1 . 1 .
    ## V19 . . . 1 . . . . . . . 1 . . 1 . 1 . . . . 1 . 1 . 1 . 1 . . . . . 1 1 .
    ## V20 . . . 1 . . . . . . . . . . 1 . . . . . . 1 . . . 1 . . . . . . . . . .
    ## V21 . . . . . . . . . . . . . . . . . . . . . . . . . . 1 . . . . . . . . .
    ## V22 . 1 . 1 . . . . . . . . . . 1 1 1 . 1 1 . . . . . . . . . . 1 1 . . . .
    ## V23 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
    ## V24 . . . . 1 1 . . . 1 . . . . 1 . 1 . 1 . . . . . . 1 . . . . 1 . . . . 1
    ## V25 . . 1 . . . . . . . . . . 1 . . 1 . . . . . . . . . . 1 . . . . . . 1 .
    ## V26 . 1 . 1 . . . . . 1 . 1 . . 1 1 1 . 1 1 . . . 1 . . 1 . . . . 1 . . . .
    ## V27 . . . . . . . . . . . . . . . 1 . . . . 1 . . . . 1 . . . . . . . . . .
    ## V28 . . 1 1 1 1 . . . . . . . 1 . . 1 1 1 . . . . . 1 . . . . 1 1 1 . . 1 .
    ## V29 . 1 . 1 . . . . 1 1 . 1 . . 1 1 1 . . . . . . . . . . . . . . . . 1 . .
    ## V30 . . . . . 1 . . . . . . . 1 . . . . . . . . . . . . . 1 . . 1 . . . . .
    ## V31 . . . 1 1 1 . . . 1 . . 1 . . . . 1 . . . 1 . 1 . . . 1 . 1 . 1 1 . 1 .
    ## V32 . . . . 1 1 . . . . . . . 1 1 1 . 1 . . . 1 . . . 1 . 1 . . 1 . 1 . 1 .
    ## V33 . . . . 1 . . . . . . . 1 . . . . 1 . . . . . . . . . . . . 1 1 . . . .
    ## V34 . . . . . . . . . 1 . 1 . . . 1 1 . 1 . . . . . . . . . 1 . . . . . . .
    ## V35 . . . . . . . . . . . . . . 1 . . 1 1 . . . . . 1 . . 1 . . 1 1 . . . .
    ## V36 . . . . . . . . . . . . . . 1 1 . . . . . . . 1 . . . . . . . . . . . .
    

Medidas Descritivas
-------------------

Agora, vamos obter algumas medidas descritivas da rede. Para obtermos a distribuição de grau faça

``` r
grau <- degree(lazega)
hist(grau)
```

![](/img/unnamed-chunk-7-1.png)

``` r
grau
```

    ##  V1  V2  V3  V4  V5  V6  V7  V8  V9 V10 V11 V12 V13 V14 V15 V16 V17 V18 
    ##   1   6   3   9   6   5   2   0   3   5   1   9   2   6  11  13  15   8 
    ## V19 V20 V21 V22 V23 V24 V25 V26 V27 V28 V29 V30 V31 V32 V33 V34 V35 V36 
    ##  10   4   1   9   0   9   5  12   3  13   9   4  13  12   5   6   7   3

``` r
summary(grau)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   0.000   3.000   6.000   6.389   9.000  15.000

Para investigar a distribuição de grau, podemos usar

``` r
dist.grau <- degree.distribution(lazega)
d <- 1:max(grau)-1
ind <- grau !=0
plot(d[ind], dist.grau[ind], log="xy", pch=19,
     xlab=c("Log-Degree"), ylab=c("Log-Intensity"), main="Log-Log Degree Distribution")
```

    ## Warning in xy.coords(x, y, xlabel, ylabel, log): 1 x value <= 0 omitted
    ## from logarithmic plot

    ## Warning in xy.coords(x, y, xlabel, ylabel, log): 1 y value <= 0 omitted
    ## from logarithmic plot

``` r
legend("bottomleft", "Distribuição de grau na escala log-log")
```

![](/img/unnamed-chunk-8-1.png)

Referências
-----------

KOLACZYK, Eric D.; CSARDI, Gabor. **Statistical analysis of network data with R**. New York, NY: Springer, 2014.

LAZEGA, Emmanuel. **The Collegial Phenomenon**: The Social Mechanisms of Cooperation Among Peers in a Corporate Law Partnership. Oxford: Oxford University Press, 2001.
