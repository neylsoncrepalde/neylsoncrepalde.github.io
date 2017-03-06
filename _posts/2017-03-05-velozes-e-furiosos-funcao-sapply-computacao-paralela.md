---
layout: post
title: Velozes e Furiosos com a função sapply e computação paralela
author: Neylson Crepalde
date: 2017, March 05
tags: [R, Apply, Sapply, Parallel, Parallel Computing]
---

Quando iniciamos nossa caminhada na programação em R, é muito comum uma expressão que mistura surpresa e indignação ao descobrir que um trabalho de coleta de dados efetuado em 3 meses pode ser executado de forma automatizada pelo R em poucos minutos! Essa proeza é executada normalmente usando um loop `for` que permite executar a mesma tarefa repetidas vezes.

Depois de algum tempo de uso e convivência com a comunidade, percebemos que o loop `for` do R não possui um desempenho muito bom. Está mais para um fusquinha. Uma Ferrari seria a família de funções `apply`, aliás uma das principais vantagens da linguagem R sobre outras linguagens, em minha opinião. A maioria das funções da família `apply` foi escrita em C e tem um desempenho muito superior ao loop `for`. Vamos efetuar uma rotina básica de cálculos usando um loop for e uma função da família apply para compará-los.

Digamos que você tem um banco de dados com um milhão de linhas (razoavelmente grande, heim...) e quer executar algumas transformações em uma das variáveis: multiplicar por 10 e tirar a raiz quadrada. Usando o comando `system.time()` vamos verificar quanto tempo (em segundos) a máquina gasta para executar esses cálculos. Vale lembrar que estamos usando um notebook simples com processador **core i3** e **4 Gb de RAM**.

```{r}
numeros <- 1:1000000

system.time({
  teste1 = c()
  for (x in numeros){
    y = x*10
    z = sqrt(y)
    teste1 = c(teste1, z)
  }
})
```

    ##       user   system   elapsed 
    ##   5427.453  418.447  5846.278


Hum... O tempo de processamento foi de aproximadamente 1h e 37 min (5846 segundos). Vamos ver agora o desempenho da máquina com a função sapply (sapply é uma função da família apply que retorna vetores simples ao invés de listas).

```{r}
system.time({
  teste2 = sapply(numeros, function(x){
    y = x*10
    z = sqrt(y)
    return(z)}
    )
})
```

    ##    user  system elapsed 
    ##   3.113   0.026   3.139
    

Wow!!! Conseguimos reduzir 1 hora e meia para 3 segundos!!! Estamos super velozes!!!

Quando as instruções a serem executadas são mais complexas, esse tempo de processamento pode ser ainda grande. Há outra otimização desenvolvida pelos cientistas da computação muito útil: a computação paralela.

Por default, qualquer operação executada em R é feita de modo serial, ou seja, os cálculos acontecem um de cada vez. Se esse processo será repetido um milhão de vezes, para otimizá-lo podemos dividir os cálculos em 4 partes e enviar uma delas para cada núcleo do processador para serem executados simultaneamente. Isso pode reduzir drasticamente o tempo de processamento.

Não vamos apresentar aqui um exemplo complexo mas simplesmente a implementação do mesmo processo executado acima agora com computação paralela (talvez num próximo post...). Para isso, carregaremos o pacote `parallel` e usaremos uma função muito parecida com a função sapply. Vamos ver como fica o tempo de processamento aqui.

```{r}
library(parallel) # carrega o pacote

no_cores = detectCores()  # define o número de núcleos a serem usados
cl = makeCluster(no_cores)  # faz os clusters 

# exporta para os cluster a variável a ser trabalhada
# sem esse comando os clusters não reconhecem a variável direto do global env.
clusterExport(cl, "numeros")

# Ação!
system.time({
  teste3 = parSapply(cl, numeros, function(x){
    y = x*10
    z = sqrt(y)
    return(z)}
  )
})

stopCluster(cl) # interrompe os clusters
```

    ##    user  system elapsed 
    ##   1.456   0.050   2.735


WOW!!! Os cálculos ficaram ainda mais rápidos!!! Agora estamos velozes e furiosos!!!

Ao executar tarefas complexas, o paralelismo pode ser uma grande ferramenta. Para um maior aprofundamento no tema, consulte [este post](https://www.r-bloggers.com/how-to-go-parallel-in-r-basics-tips/). Comentários são sempre bem vindos! Grande abraço!
