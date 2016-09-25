---
layout: post
title: INTRODUÇÃO AO R - Exercício 1
author: Neylson Crepalde
date: 2016, September 26
tags: [R programming, rstats]
---

Esses exercícios correspondem à aula 1. Você deve enviar por e-mail um script com os códigos comentados que geram as respostas corretas das questões. O e-mail deve ter o seguinte assunto: **MQuinho - Introdução ao R - Exercício 1**. Atenção! Data de entrega: **27 de setembro!**

1)  Execute cada um desses comandos no `R` e explique o que cada um faz num comentário.

``` r
7 * 9
4 + 4
x <- 3 - 10
y = x + 8
20 %% 3
sqrt(256)
45^2
968^(1/3)
exp(6)/log(156)
```

2)  Crie dois vetores. 1 vetor chamado `nomes` com os nomes das pessoas que moram na sua casa e outro chamado `idades` com as idades de cada um deles.

3)  Use um comando para mostrar a classe desses vetores e um comando para verificar o tamanho dos vetores.

4)  Use um comando para juntar esses dois vetores como colunas e criar um `data.frame`. Verifique as dimensões do seu banco de dados.

5)  Com apenas um comando, crie cada um dos seguintes vetores:

<!-- -->

    ##  [1]  1  2  3  4  5  6  7  8  9 10

    ##  [1]  2  4  6  8 10 12 14 16 18 20

    ##  [1] 1.0 1.1 1.2 1.3 1.4 1.5 1.6 1.7 1.8 1.9 2.0 2.1 2.2 2.3 2.4 2.5 2.6
    ## [18] 2.7 2.8 2.9 3.0 3.1 3.2 3.3 3.4 3.5 3.6 3.7 3.8 3.9 4.0 4.1 4.2 4.3
    ## [35] 4.4 4.5 4.6 4.7 4.8 4.9 5.0

    ## [1] 1 2 3 1 2 3 1 2 3

    ##  [1] 1 1 1 1 1 2 2 2 2 2 3 3 3 3 3

6)  Com apenas um comando, crie cada uma das seguintes matrizes:

<!-- -->

    ##       [,1] [,2] [,3] [,4]
    ##  [1,]    1   11   21   31
    ##  [2,]    2   12   22   32
    ##  [3,]    3   13   23   33
    ##  [4,]    4   14   24   34
    ##  [5,]    5   15   25   35
    ##  [6,]    6   16   26   36
    ##  [7,]    7   17   27   37
    ##  [8,]    8   18   28   38
    ##  [9,]    9   19   29   39
    ## [10,]   10   20   30   40

    ##       [,1] [,2] [,3] [,4]
    ##  [1,]    1    2    3    4
    ##  [2,]    5    6    7    8
    ##  [3,]    9   10   11   12
    ##  [4,]   13   14   15   16
    ##  [5,]   17   18   19   20
    ##  [6,]   21   22   23   24
    ##  [7,]   25   26   27   28
    ##  [8,]   29   30   31   32
    ##  [9,]   33   34   35   36
    ## [10,]   37   38   39   40

    ##      [,1] [,2] [,3] [,4]
    ## [1,]   16   80  144  208
    ## [2,]   32   96  160  224
    ## [3,]   48  112  176  240
    ## [4,]   64  128  192  256

7)  Crie uma lista com o `data.frame` que você criou, a primeira matriz, um vetor numérico à sua escolha e um vetor de texto à sua escolha. Apresente a lista.

### Divirta-se!
