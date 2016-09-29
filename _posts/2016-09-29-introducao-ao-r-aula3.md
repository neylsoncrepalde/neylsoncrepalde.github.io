---
layout: post
title: INTRODUÇÃO AO R - Aula 3
author: Neylson Crepalde
date: 2016, September 29
tags: [R programming, rstats]
---

Adaptado dos materiais de aula do [Rogério Barbosa](https://www.facebook.com/rogerio.barbosa.7528).

## Loops e Estruturas de controle

Loops são funções de iteração que nos permitem realizar tarefas repetidas de forma automatizada. Os loops estão presentes em qualquer linguagem de programação.

As três principais funções de loop são `for`, `while` e `repeat`.

### Loop For

A estrutura do loop for é esta:

``` r
for (contador  in  conjunto_de_valores){
  funcao_a_executar1
  funcao_a_executar2
  funcao_a_executar3
}
```

Vamos começar. Podemos ler o comando abaixo da seguinte forma: para cada número no conjunto de números de um a 10, "imprima" o número.

``` r
for (i in 1:10){
  print(i)
}
```

    ## [1] 1
    ## [1] 2
    ## [1] 3
    ## [1] 4
    ## [1] 5
    ## [1] 6
    ## [1] 7
    ## [1] 8
    ## [1] 9
    ## [1] 10

No comando acima, o "contador" e a expressao i e o "conjunto de valores" e o vetor numerico c(1,2,3,4,5,6,7,8,9,10). O numero de elementos do conjunto de valores determina quantas vezes as tarefas serao repetidas Nesse caso, esse vetor tem 10 elementos. Entao, o loop sera executado 10 vezes.

Na primeira iteracao, i assume o valor do primeiro elemento do conjunto. No caso, "1". E entao executamos uma funcao: print(i). Com isso, o valor de i sera apresentado na tela. Na segunda iteracao, i assume o valor do segundo elemento; no caso, 2... e assim por diante.

Podemos fazer o mesmo modificando a saída para `i^2`.

``` r
for (numero in 1:10){
  print(numero^2)
}
```

    ## [1] 1
    ## [1] 4
    ## [1] 9
    ## [1] 16
    ## [1] 25
    ## [1] 36
    ## [1] 49
    ## [1] 64
    ## [1] 81
    ## [1] 100

podemos executar várias coisas dentro de um loop. Vamos pedir uma média para todas as variáveis numéricas do banco **iris**.

``` r
data(iris)
for (var in 1:4){
  print(
    mean(iris[,var])
  )
}
```

    ## [1] 5.843333
    ## [1] 3.057333
    ## [1] 3.758
    ## [1] 1.199333

Podemos ainda fazer loops dentro de loops. Vamos preencher, com uma estrutura de loops aninhados, uma matriz. Primeiro criamos uma matriz com 10 colunas e 20 linhas. Todas as entradas sao zero:

``` r
A = matrix(0,ncol=10,nrow=20)
A
```

    ##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10]
    ##  [1,]    0    0    0    0    0    0    0    0    0     0
    ##  [2,]    0    0    0    0    0    0    0    0    0     0
    ##  [3,]    0    0    0    0    0    0    0    0    0     0
    ##  [4,]    0    0    0    0    0    0    0    0    0     0
    ##  [5,]    0    0    0    0    0    0    0    0    0     0
    ##  [6,]    0    0    0    0    0    0    0    0    0     0
    ##  [7,]    0    0    0    0    0    0    0    0    0     0
    ##  [8,]    0    0    0    0    0    0    0    0    0     0
    ##  [9,]    0    0    0    0    0    0    0    0    0     0
    ## [10,]    0    0    0    0    0    0    0    0    0     0
    ## [11,]    0    0    0    0    0    0    0    0    0     0
    ## [12,]    0    0    0    0    0    0    0    0    0     0
    ## [13,]    0    0    0    0    0    0    0    0    0     0
    ## [14,]    0    0    0    0    0    0    0    0    0     0
    ## [15,]    0    0    0    0    0    0    0    0    0     0
    ## [16,]    0    0    0    0    0    0    0    0    0     0
    ## [17,]    0    0    0    0    0    0    0    0    0     0
    ## [18,]    0    0    0    0    0    0    0    0    0     0
    ## [19,]    0    0    0    0    0    0    0    0    0     0
    ## [20,]    0    0    0    0    0    0    0    0    0     0

Agora vamos substituir os valores de cada celula usando loops:

``` r
for(i in 1:nrow(A)){
    for(j in 1:ncol(A)){
        A[i,j]=i*j
    } 
}
A
```

    ##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10]
    ##  [1,]    1    2    3    4    5    6    7    8    9    10
    ##  [2,]    2    4    6    8   10   12   14   16   18    20
    ##  [3,]    3    6    9   12   15   18   21   24   27    30
    ##  [4,]    4    8   12   16   20   24   28   32   36    40
    ##  [5,]    5   10   15   20   25   30   35   40   45    50
    ##  [6,]    6   12   18   24   30   36   42   48   54    60
    ##  [7,]    7   14   21   28   35   42   49   56   63    70
    ##  [8,]    8   16   24   32   40   48   56   64   72    80
    ##  [9,]    9   18   27   36   45   54   63   72   81    90
    ## [10,]   10   20   30   40   50   60   70   80   90   100
    ## [11,]   11   22   33   44   55   66   77   88   99   110
    ## [12,]   12   24   36   48   60   72   84   96  108   120
    ## [13,]   13   26   39   52   65   78   91  104  117   130
    ## [14,]   14   28   42   56   70   84   98  112  126   140
    ## [15,]   15   30   45   60   75   90  105  120  135   150
    ## [16,]   16   32   48   64   80   96  112  128  144   160
    ## [17,]   17   34   51   68   85  102  119  136  153   170
    ## [18,]   18   36   54   72   90  108  126  144  162   180
    ## [19,]   19   38   57   76   95  114  133  152  171   190
    ## [20,]   20   40   60   80  100  120  140  160  180   200

### Usando condicionais

O uso de condicionais é essencial em computação. Condicionais indicam que uma ação só será realizada se uma determinada condição for cumprida.

``` r
x = 10
y = 15
if(x == 10){x = x^2} 

if(y == 20){y = y^2}
x
```

    ## [1] 100

``` r
y
```

    ## [1] 15

Podemos ler os comandos acima da seguinte maneira: uma vez que atribuímos o valor 10 ao x e 15 ao y, colocamos o condicional se x for igual a 10, x deverá receber um novo valor, x ao quadrado. Se y for igual a 20, y deverá receber um novo valor, y ao quadrado. Como apenas a primeira condicional é satisfeita, apenas o valor de x é modificado.

Podemos usar uma estrutura de loop for com condicional para classificar, por exemplo, adultos num banco de dados.

``` r
banco = data.frame(sexo=c("feminino","masculino","feminino","masculino","masculino","feminino","masculino","feminino","masculino","feminino","feminino","masculino"),
                   idade=c(20,21,19,19,22,23,18,25,19,21,22,26),
                   stringsAsFactors=FALSE)

banco$adulto = NA
for (i in 1:nrow(banco)){
  if (banco$idade[i] >=21){
    banco$adulto[i] = "Adulto"
  }
  else {
    banco$adulto[i] = "Jovem"
  }
}
banco
```

    ##         sexo idade adulto
    ## 1   feminino    20  Jovem
    ## 2  masculino    21 Adulto
    ## 3   feminino    19  Jovem
    ## 4  masculino    19  Jovem
    ## 5  masculino    22 Adulto
    ## 6   feminino    23 Adulto
    ## 7  masculino    18  Jovem
    ## 8   feminino    25 Adulto
    ## 9  masculino    19  Jovem
    ## 10  feminino    21 Adulto
    ## 11  feminino    22 Adulto
    ## 12 masculino    26 Adulto

Aqui, criamos uma nova variável **adulto** e a recodificamos segundo criterio de idade ser maior ou igual a 21 anos.

Outro exemplo de recodificação com o banco de dados do Enade (que trabalhamos na aula 2). Sabemos, consultando o questionário do aluno, que as variáveis 101 até 142 (organização didático-pedagógica) tem as opções 7 e 8 codificadas respectivamente como "não sei responder" e "não se aplica". Precisamos recodificá-las como NA's para que nossa análise fique correta. Para fazer isso de modo bem fácil, podemos utilizar a função `recode` do pacote `car`. Podemos fazer:

``` r
library(readr)
enade <- read_csv2("/home/neylson/enade.csv", col_names = T)

library(descr)
freq(enade[,101], plot=F) # verificando as categorias da variável 101
```

    ## enade[, 101] 
    ##       Frequência Percentual % Válido
    ## 1             56       0.56   0.6444
    ## 2            110       1.10   1.2658
    ## 3            292       2.92   3.3602
    ## 4           1065      10.65  12.2555
    ## 5           2028      20.28  23.3372
    ## 6           4983      49.83  57.3418
    ## 7             97       0.97   1.1162
    ## 8             59       0.59   0.6789
    ## NA's        1310      13.10         
    ## Total      10000     100.00 100.0000

``` r
library(car)

for (i in 101:142){
  print(i)
  enade[,i] <- recode(enade[,i], "c(7,8)=NA")
}
```

    ## [1] 101
    ## [1] 102
    ## [1] 103
    ## [1] 104
    ## [1] 105
    ## [1] 106
    ## [1] 107
    ## [1] 108
    ## [1] 109
    ## [1] 110
    ## [1] 111
    ## [1] 112
    ## [1] 113
    ## [1] 114
    ## [1] 115
    ## [1] 116
    ## [1] 117
    ## [1] 118
    ## [1] 119
    ## [1] 120
    ## [1] 121
    ## [1] 122
    ## [1] 123
    ## [1] 124
    ## [1] 125
    ## [1] 126
    ## [1] 127
    ## [1] 128
    ## [1] 129
    ## [1] 130
    ## [1] 131
    ## [1] 132
    ## [1] 133
    ## [1] 134
    ## [1] 135
    ## [1] 136
    ## [1] 137
    ## [1] 138
    ## [1] 139
    ## [1] 140
    ## [1] 141
    ## [1] 142

``` r
freq(enade[,101], plot=F) # após a recodificação
```

    ## enade[, 101] 
    ##       Frequência Percentual % Válido
    ## 1             56       0.56   0.6562
    ## 2            110       1.10   1.2890
    ## 3            292       2.92   3.4216
    ## 4           1065      10.65  12.4795
    ## 5           2028      20.28  23.7638
    ## 6           4983      49.83  58.3900
    ## NA's        1466      14.66         
    ## Total      10000     100.00 100.0000

### Loop While

O loop while é executado até que uma condição seja satisfeita.

``` r
x <- 10
numero_da_iteracao <- 0

while(x > -2){
      print(paste("Esta é a iteração", numero_da_iteracao))
        x <- rnorm(1)
        numero_da_iteracao = numero_da_iteracao + 1
}
```

    ## [1] "Esta é a iteração 0"
    ## [1] "Esta é a iteração 1"
    ## [1] "Esta é a iteração 2"
    ## [1] "Esta é a iteração 3"
    ## [1] "Esta é a iteração 4"
    ## [1] "Esta é a iteração 5"
    ## [1] "Esta é a iteração 6"
    ## [1] "Esta é a iteração 7"
    ## [1] "Esta é a iteração 8"
    ## [1] "Esta é a iteração 9"
    ## [1] "Esta é a iteração 10"
    ## [1] "Esta é a iteração 11"
    ## [1] "Esta é a iteração 12"
    ## [1] "Esta é a iteração 13"
    ## [1] "Esta é a iteração 14"
    ## [1] "Esta é a iteração 15"
    ## [1] "Esta é a iteração 16"
    ## [1] "Esta é a iteração 17"
    ## [1] "Esta é a iteração 18"
    ## [1] "Esta é a iteração 19"
    ## [1] "Esta é a iteração 20"
    ## [1] "Esta é a iteração 21"
    ## [1] "Esta é a iteração 22"
    ## [1] "Esta é a iteração 23"
    ## [1] "Esta é a iteração 24"
    ## [1] "Esta é a iteração 25"
    ## [1] "Esta é a iteração 26"
    ## [1] "Esta é a iteração 27"
    ## [1] "Esta é a iteração 28"
    ## [1] "Esta é a iteração 29"
    ## [1] "Esta é a iteração 30"
    ## [1] "Esta é a iteração 31"
    ## [1] "Esta é a iteração 32"
    ## [1] "Esta é a iteração 33"
    ## [1] "Esta é a iteração 34"
    ## [1] "Esta é a iteração 35"
    ## [1] "Esta é a iteração 36"
    ## [1] "Esta é a iteração 37"
    ## [1] "Esta é a iteração 38"
    ## [1] "Esta é a iteração 39"
    ## [1] "Esta é a iteração 40"
    ## [1] "Esta é a iteração 41"
    ## [1] "Esta é a iteração 42"
    ## [1] "Esta é a iteração 43"
    ## [1] "Esta é a iteração 44"
    ## [1] "Esta é a iteração 45"
    ## [1] "Esta é a iteração 46"
    ## [1] "Esta é a iteração 47"
    ## [1] "Esta é a iteração 48"
    ## [1] "Esta é a iteração 49"
    ## [1] "Esta é a iteração 50"
    ## [1] "Esta é a iteração 51"
    ## [1] "Esta é a iteração 52"
    ## [1] "Esta é a iteração 53"
    ## [1] "Esta é a iteração 54"
    ## [1] "Esta é a iteração 55"
    ## [1] "Esta é a iteração 56"
    ## [1] "Esta é a iteração 57"
    ## [1] "Esta é a iteração 58"
    ## [1] "Esta é a iteração 59"
    ## [1] "Esta é a iteração 60"
    ## [1] "Esta é a iteração 61"
    ## [1] "Esta é a iteração 62"
    ## [1] "Esta é a iteração 63"
    ## [1] "Esta é a iteração 64"
    ## [1] "Esta é a iteração 65"
    ## [1] "Esta é a iteração 66"
    ## [1] "Esta é a iteração 67"
    ## [1] "Esta é a iteração 68"
    ## [1] "Esta é a iteração 69"
    ## [1] "Esta é a iteração 70"
    ## [1] "Esta é a iteração 71"
    ## [1] "Esta é a iteração 72"
    ## [1] "Esta é a iteração 73"
    ## [1] "Esta é a iteração 74"
    ## [1] "Esta é a iteração 75"
    ## [1] "Esta é a iteração 76"
    ## [1] "Esta é a iteração 77"
    ## [1] "Esta é a iteração 78"

### Loop Repeat

O loop repeat é executado infinitamente. É bom que coloquemos uma condição para que ele pare usando a função `break`.

``` r
i=0
repeat{
    print(i)
    i=i+1
        if(i > 1000) {
        break
    }
}
```

## A família APPLY (INTRO)

O R possui uma família de funções chamada `apply()` para realizar loops de forma intuitiva e com muita eficiência computacional.

A família possui a função genérica `apply()` e suas variações `sapply()`, `lapply()`, `tapply()`, `mapply()`, dentre outras. Hoje vamos trabalhar com lapply e sapply.

Podemos executar um comando para várias variáveis de uma só vez, por exemplo, tirar a média. Lapply recebe uma lista como argumento e retorna uma nova lista.

``` r
x <- list(Componente1=1:50, Componente2=seq(2,10,by=.2))
x
```

    ## $Componente1
    ##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
    ## [24] 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46
    ## [47] 47 48 49 50
    ## 
    ## $Componente2
    ##  [1]  2.0  2.2  2.4  2.6  2.8  3.0  3.2  3.4  3.6  3.8  4.0  4.2  4.4  4.6
    ## [15]  4.8  5.0  5.2  5.4  5.6  5.8  6.0  6.2  6.4  6.6  6.8  7.0  7.2  7.4
    ## [29]  7.6  7.8  8.0  8.2  8.4  8.6  8.8  9.0  9.2  9.4  9.6  9.8 10.0

``` r
lapply(x,mean)
```

    ## $Componente1
    ## [1] 25.5
    ## 
    ## $Componente2
    ## [1] 6

Sapply retorna os valores simplificados.

``` r
sapply(iris[,1:4], mean)
```

    ## Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
    ##     5.843333     3.057333     3.758000     1.199333

Tapply executa uma função por grupos.

``` r
# Sem tapply
mean(iris$Petal.Width[iris$Species=="virginica"])
```

    ## [1] 2.026

``` r
mean(iris$Petal.Width[iris$Species=="setosa"])
```

    ## [1] 0.246

``` r
mean(iris$Petal.Width[iris$Species=="versicolor"])
```

    ## [1] 1.326

``` r
# Com tapply...
tapply(iris$Petal.Width, iris$Species, mean)
```

    ##     setosa versicolor  virginica 
    ##      0.246      1.326      2.026
