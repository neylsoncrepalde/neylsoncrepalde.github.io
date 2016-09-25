---
layout: post
title: INTRODUÇÃO AO R -- Aula 1
author: Neylson Crepalde
date: 2016, September 25
tags: [R programming, rstats]
---

Olá! Esta é a primeira aula do curso **Introdução ao R** do MQuinho 2016. Este material foi amplamente inspirado no curso homônimo do [Rogério Barbosa](https://www.facebook.com/rogerio.barbosa.7528) e no curso *Introduction to Statistical Learning with Applications in R* de Trevor Hastie e Robert Tibshirani (livro disponibilizado pelos autores [aqui](http://www-bcf.usc.edu/~gareth/ISL/)). Vamos começar!

## Comandos básicos

Podemos usar o R como calculadora:

``` r
5 + 5
```

    ## [1] 10

``` r
16-32
```

    ## [1] -16

``` r
7*5
```

    ## [1] 35

``` r
9/3
```

    ## [1] 3

Resto da divisão

``` r
10%%3
```

    ## [1] 1

### Raiz e potência

A maioria dos comandos no R tem esse formato: `função()`. Para tirar a raiz usamos o comando `sqrt()`

``` r
sqrt(49)
```

    ## [1] 7

``` r
4^2
```

    ## [1] 16

``` r
5^5
```

    ## [1] 3125

Raiz cúbica

``` r
27^(1/3)
```

    ## [1] 3

Exponencial e log natural(base e)

``` r
exp(4.5)
```

    ## [1] 90.01713

``` r
log(90)
```

    ## [1] 4.49981

Linguagem de programação orientada a objetos
--------------------------------------------

O **R** é uma linguagem de programação orientada a objetos. Em linguagem muito coloquial, isso quer dizer que podemos atribuir números, textos e resultados a um objeto criado. Vamos construir um objeto chamado `x` com o valor 5 e um objeto `y` com o valor 7:

``` r
x <- 5
y = 7
x
```

    ## [1] 5

``` r
y
```

    ## [1] 7

Podemos fazer também operações com objetos

``` r
x+y
```

    ## [1] 12

``` r
x*y
```

    ## [1] 35

``` r
z = 2*x + y^2
z
```

    ## [1] 59

### Trabalhando com vetores

O comando `c()` significa *concatenar*, ou seja, juntar os valores. Vamos construir um objeto `x` e atribuir a ele um vetor de números:

``` r
x <- c(1,3,2,5)
x
```

    ## [1] 1 3 2 5

Se eu atribuir outro vetor ao objeto `x`, ele será sobrescrito.

``` r
x = c(1,6,2)
x
```

    ## [1] 1 6 2

``` r
y = c(1,4,3)
y
```

    ## [1] 1 4 3

Podemos verificar o tamanho do vetor com o comando `length()`:

``` r
length(x)
```

    ## [1] 3

``` r
length(y)
```

    ## [1] 3

### Operações com vetores

``` r
x+y
```

    ## [1]  2 10  5

``` r
x-y
```

    ## [1]  0  2 -1

``` r
x*y
```

    ## [1]  1 24  6

``` r
x/y
```

    ## [1] 1.0000000 1.5000000 0.6666667

Outros comandos úteis:

``` r
ls() #este comando lista os objetos no ambiente
```

    ## [1] "x" "y" "z"

``` r
rm(x,y) #este comando remove objetos
ls()
```

    ## [1] "z"

``` r
rm(list=ls()) # para limpar tudo no ambiente
ls()
```

    ## character(0)

Classes de vetores
------------------

Os vetores podem ser numéricos, inteiros, lógicos, ou *character*, isto é, vetores de texto.

Para descobrir a classe de um vetor, usamos o comando `class()`

``` r
numeros <- c(13,15,24,17,12)
nomes <- c("Neylson","Ítalo","Silvio","Rogério","Marcelo")

class(numeros) # vetor numérico
```

    ## [1] "numeric"

``` r
class(nomes) # vetor de texto ou string
```

    ## [1] "character"

``` r
inteiros <- c(5L, 7L, 45L, 9L, 2L)
class(inteiros) # vetor de números inteiros
```

    ## [1] "integer"

``` r
#podemos converter um vetor numérico para um vetor de inteiros
peso <- c(60.45, 78.9, 45.7, 98.654, 69.324)
class(peso)
```

    ## [1] "numeric"

``` r
as.integer(peso) 
```

    ## [1] 60 78 45 98 69

Trabalhando com vetores character (INTRO)
-----------------------------------------

Uma função bastante útil para vetores numéricos é a `paste()`. Esta função junta os diversos componentes de um vetor separando-os com um espaço. Se quero que seja separada por outro elemento qualquer, uso a opção `sep`. A função `paste0()` não usa separador.

``` r
paste("Eu","gosto","de","pão","de","queijo!")
```

    ## [1] "Eu gosto de pão de queijo!"

``` r
paste("Eu","gosto","de","pão","de","queijo!", sep = "|")
```

    ## [1] "Eu|gosto|de|pão|de|queijo!"

``` r
paste0("Ês", "pens","cu","ons","é","dês") #Só os mineiros entenderão!
```

    ## [1] "Êspenscuonsédês"

Vetores Lógicos
---------------

Vetores lógicos assumem apenas dois valores: `TRUE` e `FALSE`. Eles são importantes para testes no R.

``` r
logicos <- c(T,F,F,T,T)
class(logicos)
```

    ## [1] "logical"

``` r
5 > 3   # 5 é maior que 3?
```

    ## [1] TRUE

``` r
5 > 6   # 5 é maior que 6?
```

    ## [1] FALSE

``` r
is.numeric(logicos) # o vetor "logicos" é numeric?
```

    ## [1] FALSE

### Sequências

Sequências são bastante úteis em programação. Podemos criá-las de algumas maneiras no R:

``` r
1:10
```

    ##  [1]  1  2  3  4  5  6  7  8  9 10

``` r
4:40
```

    ##  [1]  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26
    ## [24] 27 28 29 30 31 32 33 34 35 36 37 38 39 40

``` r
2.5:9
```

    ## [1] 2.5 3.5 4.5 5.5 6.5 7.5 8.5

``` r
7:-7
```

    ##  [1]  7  6  5  4  3  2  1  0 -1 -2 -3 -4 -5 -6 -7

A função `seq()` permite algumas opções mais elaboradas:

``` r
seq(from=15, to=30)
```

    ##  [1] 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30

``` r
seq(from=15, to=30, by=3)
```

    ## [1] 15 18 21 24 27 30

``` r
seq(from=15, to=30, by= 0.5)
```

    ##  [1] 15.0 15.5 16.0 16.5 17.0 17.5 18.0 18.5 19.0 19.5 20.0 20.5 21.0 21.5
    ## [15] 22.0 22.5 23.0 23.5 24.0 24.5 25.0 25.5 26.0 26.5 27.0 27.5 28.0 28.5
    ## [29] 29.0 29.5 30.0

``` r
seq(from=15, to=30, length=10) #tamanho da sequência definida
```

    ##  [1] 15.00000 16.66667 18.33333 20.00000 21.66667 23.33333 25.00000
    ##  [8] 26.66667 28.33333 30.00000

``` r
seq(from=15, to=5, by=-.5)
```

    ##  [1] 15.0 14.5 14.0 13.5 13.0 12.5 12.0 11.5 11.0 10.5 10.0  9.5  9.0  8.5
    ## [15]  8.0  7.5  7.0  6.5  6.0  5.5  5.0

Podemos ainda criar repetições com a função `rep()`:

``` r
rep(1:3, times=10)
```

    ##  [1] 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3 1 2 3

``` r
rep(1:3, each=10)
```

    ##  [1] 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 3 3 3 3 3 3 3 3 3 3

### Missing data

No `R` o valor *missing*, ou **não resposta** é representado por `NA`.

``` r
x = c(1,3,NA,6,10)
x
```

    ## [1]  1  3 NA  6 10

``` r
class(x)
```

    ## [1] "numeric"

``` r
is.na(x) #No vetor x há um NA?
```

    ## [1] FALSE FALSE  TRUE FALSE FALSE

``` r
is.nan(x) #No vetor x há um NaN (not a number)?
```

    ## [1] FALSE FALSE FALSE FALSE FALSE

Veja bem que NaN (*not a number*) é um caso especial de *missing values*.

``` r
0/0
```

    ## [1] NaN

``` r
is.na(0/0)
```

    ## [1] TRUE

``` r
is.nan(0/0)
```

    ## [1] TRUE

### Matrizes

Matrizes são objetos que assumem duas dimensões, ou seja, vetores organizados em linhas e colunas. As matrizes podem armazenar dados de apenas um tipo. Esta é uma característica importante para lembrarmos.

Podemos criar uma matriz no `R` com o comando `matrix()` e definindo o número de linhas e colunas:

``` r
matrix(1:40, nrow = 10, ncol = 4)
```

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

``` r
matrix(1:40, nrow = 10, ncol = 4, byrow = T)
```

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

Podemos também fazer operações com matrizes:

``` r
X <- matrix(21:40, 5, 4)
X
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]   21   26   31   36
    ## [2,]   22   27   32   37
    ## [3,]   23   28   33   38
    ## [4,]   24   29   34   39
    ## [5,]   25   30   35   40

``` r
class(X)
```

    ## [1] "matrix"

``` r
sqrt(X)
```

    ##          [,1]     [,2]     [,3]     [,4]
    ## [1,] 4.582576 5.099020 5.567764 6.000000
    ## [2,] 4.690416 5.196152 5.656854 6.082763
    ## [3,] 4.795832 5.291503 5.744563 6.164414
    ## [4,] 4.898979 5.385165 5.830952 6.244998
    ## [5,] 5.000000 5.477226 5.916080 6.324555

``` r
X^2
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]  441  676  961 1296
    ## [2,]  484  729 1024 1369
    ## [3,]  529  784 1089 1444
    ## [4,]  576  841 1156 1521
    ## [5,]  625  900 1225 1600

Para selecionar um elemento qualquer de uma matriz, usamos `[]`:

``` r
X[10]
```

    ## [1] 30

``` r
class(X[10])
```

    ## [1] "integer"

Para selecionar por linhas e colunas use a fórmula `[linha , coluna]`.

``` r
X[1,2]
```

    ## [1] 26

``` r
X[2,1]
```

    ## [1] 22

``` r
X[3,] # Linhas 3 inteira
```

    ## [1] 23 28 33 38

``` r
X[,4] # Coluna 4 inteira
```

    ## [1] 36 37 38 39 40

Para investigar as dimensões de uma matriz, usamos o comando `dim()`. `nrow()` e `ncol()` retornam o número de linhas e o número de colunas.

``` r
dim(X)
```

    ## [1] 5 4

``` r
nrow(X)
```

    ## [1] 5

``` r
ncol(X)
```

    ## [1] 4

### Data Frames

Os *data frames* são os objetos do tipo **banco de dados** do R. Eles também são organizados em linhas e colunas mas, diferente das matrizes, os *data frames* podem conter colunas de diferentes classes. Uma coluna pode ser *numeric*, outra *character* e outra *integer*, por exemplo. Os *data frames* são, portanto, estruturas semelhantes aos bancos de dados convencionais usados em outros pacotes estatísticos como SPSS ou STATA onde as colunas são as variáveis e as linhas, os casos.

Podemos criar um banco de dados com o comando `data.frame()`. É necessário dizer ao R o nome das variáveis:

``` r
dados <- data.frame(Nomes=c("Antonio","Gilberto","Mauricio","Isabela"),
                   Profissoes=c("Video Maker","Comerciante","Bancario","Estudante"),
                   Cidades=c("Belo Horizonte","Sao Paulo","Belo Horizonte","Sao Paulo"),
                   Idades=c(65,50,67,19),
                   stringsAsFactors=F)
dados
```

    ##      Nomes  Profissoes        Cidades Idades
    ## 1  Antonio Video Maker Belo Horizonte     65
    ## 2 Gilberto Comerciante      Sao Paulo     50
    ## 3 Mauricio    Bancario Belo Horizonte     67
    ## 4  Isabela   Estudante      Sao Paulo     19

Podemos selecionar itens específicos de cada *data frame* de várias maneiras.

``` r
dados[4]
```

    ##   Idades
    ## 1     65
    ## 2     50
    ## 3     67
    ## 4     19

``` r
class(dados[4]) # retorna um data.frame
```

    ## [1] "data.frame"

``` r
class(dados[[4]]) # [[]] retorna um numeric
```

    ## [1] "numeric"

``` r
class(dados["Idades"]) # retorna um data.frame
```

    ## [1] "data.frame"

``` r
class(dados[["Idades"]]) # retorna um numeric
```

    ## [1] "numeric"

``` r
class(dados$Idades) # Usando o operador $ retorna sempre um numeric
```

    ## [1] "numeric"

Também podemos selecionar itens específicos de um *data frame*:

``` r
dados[2,4] # linha 2, coluna 4
```

    ## [1] 50

``` r
dados[3,1] # linha 3, coluna 1
```

    ## [1] "Mauricio"

``` r
dados[,2] # toda a coluna 2
```

    ## [1] "Video Maker" "Comerciante" "Bancario"    "Estudante"

``` r
dados[3,] # toda a linha 3
```

    ##      Nomes Profissoes        Cidades Idades
    ## 3 Mauricio   Bancario Belo Horizonte     67

Podemos combinar essas formas de selecao:

``` r
dados[["Idades"]][1]  #Primeiro elemento da variavel idade
```

    ## [1] 65

``` r
dados[["Idades"]][2:4]  # Elementos 2,3 e 4 da variavel idade
```

    ## [1] 50 67 19

Podemos ainda especificar critérios de seleção. Neste exemplo, vamos extrair todos os casos em que a pessoa tenha mais de 30 anos:

``` r
# dados fazendo subsetting pelas linhas em que a variável Idades seja maior que 30
dados[dados$Idades>30,]
```

    ##      Nomes  Profissoes        Cidades Idades
    ## 1  Antonio Video Maker Belo Horizonte     65
    ## 2 Gilberto Comerciante      Sao Paulo     50
    ## 3 Mauricio    Bancario Belo Horizonte     67

Para acrescentar novas informações aos bancos de dados, usamos os comandos `rbind()` para acrescentar linhas e `cbind()` para acrescentar colunas.

``` r
rbind(dados, data.frame(Nomes="Fernando",Profissoes="Bancario", Cidades="Belo Horizonte",Idades=28))
```

    ##      Nomes  Profissoes        Cidades Idades
    ## 1  Antonio Video Maker Belo Horizonte     65
    ## 2 Gilberto Comerciante      Sao Paulo     50
    ## 3 Mauricio    Bancario Belo Horizonte     67
    ## 4  Isabela   Estudante      Sao Paulo     19
    ## 5 Fernando    Bancario Belo Horizonte     28

``` r
cbind(dados, Time=c("Cruzeiro","Galo Doido","Cruzeiro","Galo Doido"))
```

    ##      Nomes  Profissoes        Cidades Idades       Time
    ## 1  Antonio Video Maker Belo Horizonte     65   Cruzeiro
    ## 2 Gilberto Comerciante      Sao Paulo     50 Galo Doido
    ## 3 Mauricio    Bancario Belo Horizonte     67   Cruzeiro
    ## 4  Isabela   Estudante      Sao Paulo     19 Galo Doido

Podemos ainda criar uma matriz ou um *data frame* com os comandos `rbind()` e `cbind()` a partir de vetores. Por exemplo:

``` r
pessoas = c("Neylson","Ítalo","Vicky","Mel","Leonardo")
idades = c(29, 23, 21, 49, 27)
grupos_pesquisa = c("GIARS","GIARS","GIARS","CPEQS","CPEQS")
colegas <- cbind(pessoas, idades, grupos_pesquisa)
colegas
```

    ##      pessoas    idades grupos_pesquisa
    ## [1,] "Neylson"  "29"   "GIARS"        
    ## [2,] "Ítalo"    "23"   "GIARS"        
    ## [3,] "Vicky"    "21"   "GIARS"        
    ## [4,] "Mel"      "49"   "CPEQS"        
    ## [5,] "Leonardo" "27"   "CPEQS"

``` r
class(colegas)
```

    ## [1] "matrix"

``` r
sapply(colegas, class) # aplica a função class para cada elemento da matriz
```

    ##     Neylson       Ítalo       Vicky         Mel    Leonardo          29 
    ## "character" "character" "character" "character" "character" "character" 
    ##          23          21          49          27       GIARS       GIARS 
    ## "character" "character" "character" "character" "character" "character" 
    ##       GIARS       CPEQS       CPEQS 
    ## "character" "character" "character"

``` r
colegas <- cbind.data.frame(pessoas, idades, grupos_pesquisa)
colegas
```

    ##    pessoas idades grupos_pesquisa
    ## 1  Neylson     29           GIARS
    ## 2    Ítalo     23           GIARS
    ## 3    Vicky     21           GIARS
    ## 4      Mel     49           CPEQS
    ## 5 Leonardo     27           CPEQS

``` r
class(colegas)
```

    ## [1] "data.frame"

``` r
sapply(colegas, class) # aplica a função class para cada elemento do data.frame (colunas)
```

    ##         pessoas          idades grupos_pesquisa 
    ##        "factor"       "numeric"        "factor"

### Listas

Uma lista é uma classe de objetos mais complexa do que o *data frame* porque pode conter quaisquer outras classes em sua composição. Podemos montar uma lista com um vetor numérico, um vetor character, uma matriz e um data.frame:

``` r
lista <- list(numerico=c(4,6,78,9.5,3.465,1098), vetor_character=c("Márcio", "Cléber"),
              matriz=matrix(101:200, 20, 5), 
              bd = data.frame(nomes=c("César", "Daniel","Cecília"),
                         idades=c(19,22,24),
                         raca=c("Branco","Pardo","Preto"), stringsAsFactors = F))
lista
```

    ## $numerico
    ## [1]    4.000    6.000   78.000    9.500    3.465 1098.000
    ## 
    ## $vetor_character
    ## [1] "Márcio" "Cléber"
    ## 
    ## $matriz
    ##       [,1] [,2] [,3] [,4] [,5]
    ##  [1,]  101  121  141  161  181
    ##  [2,]  102  122  142  162  182
    ##  [3,]  103  123  143  163  183
    ##  [4,]  104  124  144  164  184
    ##  [5,]  105  125  145  165  185
    ##  [6,]  106  126  146  166  186
    ##  [7,]  107  127  147  167  187
    ##  [8,]  108  128  148  168  188
    ##  [9,]  109  129  149  169  189
    ## [10,]  110  130  150  170  190
    ## [11,]  111  131  151  171  191
    ## [12,]  112  132  152  172  192
    ## [13,]  113  133  153  173  193
    ## [14,]  114  134  154  174  194
    ## [15,]  115  135  155  175  195
    ## [16,]  116  136  156  176  196
    ## [17,]  117  137  157  177  197
    ## [18,]  118  138  158  178  198
    ## [19,]  119  139  159  179  199
    ## [20,]  120  140  160  180  200
    ## 
    ## $bd
    ##     nomes idades   raca
    ## 1   César     19 Branco
    ## 2  Daniel     22  Pardo
    ## 3 Cecília     24  Preto

Podemos fazer *subsetting* com listas do mesmo modo que fazemos com *data frames*:

``` r
# Com colchetes simples
class(lista[3])
```

    ## [1] "list"

``` r
class(lista["matriz"])
```

    ## [1] "list"

``` r
# Com colchetes duplas
class(lista[[3]])
```

    ## [1] "matrix"

``` r
class(lista[["matriz"]])
```

    ## [1] "matrix"

``` r
# Subsettings mais precisos
class(lista[[4]][2,1])
```

    ## [1] "character"
