---
layout: post
title: INTRODUÇÃO AO R - Aula 4
author: Neylson Crepalde
date: 2016, October 4
tags: [R programming, rstats]
---

Funções - Introdução
--------------------

A programação de funções marca a passagem do usuário para o desenvolvedor de R. As funções podem ser bastante úteis na automatização de tarefas e na implamentação de novas análises. Funções são escritas no R com a seguinte sintaxe:

``` r
nome_da_funcao <- function(argumentos){
  acoes_a_serem_executadas
  return(valor_final_retornado)
}
```

As funções possuem classe própria (class *function*) e podem ser executadas de forma aninhada. Vamos programar uma função que calcula a média e retorna uma frase legal na apresentação.

``` r
media <- function(x){
  cat("Função legal de média\n")
  med = sum(x)/length(x)
  return(med)
}
```

Vamos ver se funciona?

``` r
vetor <- c(1,5,4,7,6,4,22,4,33,678,8.665,3)
media(vetor)
```

    ## Função legal de média

    ## [1] 64.63875

``` r
mean(vetor)
```

    ## [1] 64.63875

Vamos agora programar uma função de desvio padrão.

``` r
desvio_pad <- function(vetor, na.rm = TRUE){
  cat("Função legal de Desvio Padrão\n")
  if (na.rm == TRUE){
    vetor = vetor[!is.na(vetor)]
  }
  n = length(vetor)
  med = sum(vetor)/n
  desv = vetor - med
  desv2 = desv^2
  desv.p= sqrt(sum(desv2/(n - 1)))
  return(desv.p)
}
```

Vamos testar:

``` r
x = rnorm(20)
x = c(x, NA)
desvio_pad(x)
```

    ## Função legal de Desvio Padrão

    ## [1] 1.076367

``` r
sd(x)
```

    ## [1] NA

``` r
sd(x, na.rm = T)
```

    ## [1] 1.076367

Como exercício, vamos programar a sequência de Fibonacci usando apenas um for loop e gerando um vetor de tamanho 10:

``` r
fib = c(1,1)
for (i in 3:10){
  fib[i] = fib[i-1] + fib[i-2]
}
fib
```

    ##  [1]  1  1  2  3  5  8 13 21 34 55

Agora vamos colocar a sequência numa função em que possamos dar como argumento o tamanho do vetor que desejamos que seja criado.

``` r
fibonacci <- function(x){
fib = c(1,1)
for (i in 3:x){
  fib[i] = fib[i-1] + fib[i-2]
}
return(fib)
}

fibonacci(10)
```

    ##  [1]  1  1  2  3  5  8 13 21 34 55

``` r
fibonacci(20)
```

    ##  [1]    1    1    2    3    5    8   13   21   34   55   89  144  233  377
    ## [15]  610  987 1597 2584 4181 6765

Análise de Redes Sociais
------------------------

Num curso ministrado por um membro do [GIARS](http://www.giars.ufmg.br) não poderia faltar uma seção introdutória sobre análise de redes!

Veja como o R executa bem funções de estudos em redes:

``` r
library(igraph)
library(sand)
lazega <- upgrade_graph(lazega)
plot(lazega, vertex.size=12, vertex.color="red", edge.arrow.size=.3,
     vertex.label.color="black", main="Lazega's lawyers")
```

![](/img/intro_r_aula4_files/figure-markdown_github/unnamed-chunk-8-1.png)

### Segue no post [Workshop R e Redes 01](http://neylsoncrepalde.github.io/2016-04-23-workshop-r-e-redes-01/).
