---
layout: post
title: INTRODUÇÃO AO R - Exercício 2
author: Neylson Crepalde
date: 2016, September 27
tags: [R programming, rstats]
---

**Data de entrega: 28 de setembro**

### PNAD

Abra o banco de dados da PNAD 2012. Você precisará usar um comando de um pacote específico já que o arquivo é do SPSS (.sav). Depois disso:

1.  Usando um comando, tire as estatísticas descritivas de todas as variáveis do banco de dados.

2.  Tire um histograma da variável renda mensal do trabalho principal (use o dicionário disponibilizado para descobrir o código da variável). Não se esqueça de codificar 999999999999 como NA antes. Use o comando abaixo:

``` r
pnad$V4718[pnad$V4718 >= 999999999999] = NA
```

3.  Tire um boxplot da variável idade por categorias de sexo. O que você pode comentar de relevante sobre o gráfico?

4.  Agora, com um comando, execute um teste estatístico para verificar se as médias da renda mensal do trabalho principal entre homens e mulheres são estatisticamente diferentes. Interprete o resultado.

### Enade

Agora, abra o banco de dados do Enade 2014.

5.  Tire um histograma da nota geral dos alunos no Enade (nt\_ger).

6.  Agora tire um histograma da nota geral dos alunos apenas para os alunos do curso de licenciatura em ciências sociais (dica: faça um *subsetting* das linhas onde a variável co\_grupo seja igual a 5402).

7.  Agora, use um comando para gerar uma tabela de frequência e um gráfico para verificar as categorias de renda dos estudantes (qe\_i8). Antes disso, transforme a variável numa classe `factor` e coloque os labels corretos (use o comando `levels()` e o questionário do aluno para ajudar).

8.  Vamos investigar se os alunos que entraram nas faculdades via ações afirmativas tem desempenho no Enade diferente dos que não entraram por esse mecanismo. Para isso precisamos recodificar a variável **Entrou por ação afirmativa** (qe\_i15) para uma variável binária. Use o seguinte comando:

``` r
# Criando a variavel dummy acao.afirmativa. 0 = Não, 1 = Sim
enade$acao.afirmativa <- ifelse(enade$qe_i15=="a", 0, 1)
```

Agora você já sabe qual teste estatístico usar. Comente o resultado.

9.  Vamos montar um modelo de regressão linear. O seu objetivo é modelizar o desempenho do aluno (medido pela sua nota geral) como função da cor, do sexo e da escolarização da mãe (qe\_i5). Não se esqueça de recodificar a variável sexo renomeando a categoria **N** para NA. Você já sabe como fazer isso. Após estimar o modelo, apresente os resultados e interprete.

## Divirta-se!
