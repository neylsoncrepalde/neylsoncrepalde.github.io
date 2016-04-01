---
layout: post
title: Rede de Parlamentares usando o pacote bRasilLegis
subtitle: Neylson Crepalde (GIARS)
date: 2016, April 01
---

Olá. Neste tutorial vou utilizar o pacote [**bRasilLegis**](https://github.com/leobarone/bRasilLegis) desenvolvido pelo [Leonardo Barone](https://www.facebook.com/leonardo.s.barone) e pela [Alexia Aslan](https://www.facebook.com/alexiaaslan) para montar uma rede de parlamentares. O "plano de vôo" é obter uma rede de afiliações de parlamentares em comissões e, a partir delas, transformá-la numa rede 1-mode de parlamentares. À medida que formos desenvolvendo o código, tentarei dar algumas (mui breves) explicações teóricas a respeito do processo. Qualquer dúvida, deixe um comentário, me mande um e-mail ou uma mensagem no [Facebook](https://www.facebook.com/neylson.crepalde).

Vamos começar instalando o pacote. Use o comando

``` r
install.packages("devtools"); library(devtools); install_github("leobarone/bRasilLegis")
```

É necessário que você instale também os pacotes **XML** e **httr**. Eles são necessários para os comandos que serão desenvolvidos aqui. Começamos carregando os pacotes necessários.

``` r
library(XML)
library(httr)
library(bRasilLegis)
```

Primeiro, precisamos obter todas as comissões existentes. Para isso, usamos o comando

``` r
orgaos <- obterOrgaos()
orgao.id <- orgaos$id
orgao.id <- as.numeric(orgao.id)
```

Aqui, já deixamos os números de registro das comissões em formato numérico pois queremos capturar a participação em várias comissões de uma vez através de um loop. Sabemos que a captura de dados online pode ser *tricky* e erros normalmente fazem o código parar. Para que isso aconteça, inseri um pequeno condicional para que a captura seja tentada. Caso fracasse (porque não há registros da comissão ou qualquer outro motivo que seja) pulamos para a próxima.

Para realizar a captura e organizar tudo direto num *data frame*, use os seguintes comandos:

``` r
dados <- data.frame()

for (i in orgao.id){
  print(i)
  
  erro <- try(memb <- obterMembrosOrgao(i), silent = T)
  if ('try-error' %in% class(erro)){  
    print("erro")
  }
  
  else{
  
  memb <- obterMembrosOrgao(i)
  dados <- rbind(dados, memb)
  }
}
```

Como nosso intuito é obter uma rede a partir desses dados, precisamos de um formato que seja de fácil leitura para o pacote **igraph**. O formato **edge.list** (uma coluna com o nome de parlamentar e outra com o nome da comissão a qual ele pertence) é universalmente aceito pela maioria dos softwares que trabalham com ARS. Se você é usuário do sistema Windows e está trabalhando com o **UCINET**, por exemplo, esse formato pode ser facilmente aberto no programa. Com o **Pajek**, outro software excelente para ARS, funciona da mesma forma usando um programinha suplementar chamado **createpajek**.

Para organizar os dados em uma edge.list, use o comando

``` r
edge.list <- as.matrix(cbind(dados$nome.1, dados$nome))
```

Vamos também aproveitar algumas informações como atributos dos indivíduos. Para isso, usamos

``` r
atributos <- as.matrix(cbind(dados$nome.1, dados$partido, dados$uf))
atributos <- unique(atributos)
```

Se você quiser exportar o edge.list e a matriz de atributos para usar com o UCINET, por exemplo, podemos gravá-la em um arquivo .csv da seguinte maneira:

``` r
write.table(edge.list, "C:/.../Documents/edge_list.csv",
            sep = ";")
write.table(atributos, "C:/.../Documents/atributos.csv",
            sep = ";")
```

Agora, vamos começar montando a rede. Após carregar o pacote **igraph**, vamos importar o edge.list para um objeto reconhecido pelo pacote. Lembre-se de as redes 2-mode (bipartidas) são sempre **não** direcionadas. Para que o pacote reconheça uma rede 2-mode, é necessário um atributo para vértice chamado type contendo um vetor lógico TRUE-FALSE. A vantagem do **igraph** é que não precisamos dizer a ele essa informação. O pacote reconhece os dois modos da rede através do comando **bipartite.mapping()**. Depois de guardar o vetor lógico em um objeto que chamamos de **bip**, criamos o atribute type e extraindo o vetor lógico do objeto **bip**. Podemos *plotá-lo* logo em seguida sem nenhuma especificação só para termos certeza de que o processo ocorreu normalmente.

``` r
library(igraph)
g <- graph_from_edgelist(edge.list, directed = F)
bip <- bipartite.mapping(g)
V(g)$type <- bip[[2]]
```

Para conferir se o pacote reconheceu a rede como 2-mode, usamos o comando **is.bipartite()**. Podemos *plotá-la* logo em seguida com apenas uma especificação de cor por modo só para termos certeza de que o processo ocorreu normalmente. Como a rede é grande, vamos colocar o tamanho dos vértices um pouco menor, por exemplo, 4 e tirar os labels para não sujar.

``` r
is.bipartite(g)
```

    ## [1] TRUE

``` r
plot(g, vertex.size=4, vertex.color=as.numeric(V(g)$type)+4, vertex.label=NA)
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis09-1.png)

Pronto. A rede está aí. Podemos plotá-la também usando os algoritmos Kamada-Kawai ou Fruchterman-Reingold:

``` r
plot(g, layout=layout_with_kk(g), vertex.size=4, vertex.color=as.numeric(V(g)$type)+4, vertex.label=NA)
title(main = "Rede de afiliação de\nparlamentares em comissões", xlab = "Kamada-Kawai")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis10-1.png)

``` r
plot(g, layout=layout_with_fr(g), vertex.label=NA, vertex.size=4, vertex.color=as.numeric(V(g)$type)+4)
title(main = "Rede de afiliação de\nparlamentares em comissões", xlab = "Fruchterman-Reingold")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis10-2.png)

Agora vamos começar as transformações. Vamos extrair uma matriz quadrada (1-mode) apenas dos parlamentares. Sabemos que eles estão com o valor FALSE no atributo *type*.

``` r
parlamentares <- bipartite_projection(g, which = "false")
plot(parlamentares, layout=layout_with_kk(parlamentares), 
     vertex.size=4, vertex.label=NA)
title(main = "Rede de Parlamentares\nEdge = Afiliação em Comissão")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis11-1.png)

Agora, vamos criar 3 atributos para cada vértice: o partido, o estado e um atributo de contagem = 1 porque pretendemos visualizar quantos parlamentares estão em cada partido. Em seguida vamos plotar as redes usando a cor para diferenciar os partidos e os estados.

``` r
V(parlamentares)$partido <- atributos[,2]
V(parlamentares)$uf <- atributos[,3]
V(parlamentares)$count <- 1
plot(parlamentares, layout=layout_with_kk(parlamentares), 
     vertex.size=4, vertex.label=NA, vertex.color=as.integer(factor(V(parlamentares)$partido)))
title(main="Rede de Parlamentares", xlab = "Cor = Partido")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis12-1.png)

``` r
plot(parlamentares, layout=layout_with_kk(parlamentares), 
     vertex.size=4, vertex.label=NA, vertex.color=factor(V(parlamentares)$uf))
title(main="Rede de Parlamentares", xlab = "Cor = UF")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis12-2.png)

Podemos proceder com várias análises a partir desse grafo gerado: rodar escores de centralidade diversos, separar cluster e blocos (blockmodeling), mas deixaremos as questões de análise das redes propriamente dito para outro momento. Nesse ponto, quero extrair uma subrede apenas com os partidos. Para isso usamos o comando **contract.vertices**. O comando **simplify** limpa os loops (laços consigo mesmo) e laços múltiplos.

``` r
rede.partidos <- contract.vertices(parlamentares, as.integer(as.factor(V(parlamentares)$partido)), 
                                   vertex.attr.comb = list(partido=toString, "ignore"))
rede.partidos <- simplify(rede.partidos)
```

Infelizmente, o partido é lido pelo pacote como um vetor numérico. Precisamos adicionar os nomes através da lista de atributos que geramos mais cedo. Já conferi e vi que os números gerados no pacote são os mesmos da lista e portanto podemos nos direcionar por eles. Vamos inserir também a contagem.

``` r
name <- c("","DEM","PCdoB","PDT","PEN","PHS","PMB","PMDB","PP","PPS","PR","PRB",
          "PROS","PSB","PSC","PSD","PSDB","PSL","PSOL","PT","PTB","PTC","PTdoB","PTN",
          "PV","REDE","S.PART.","SD")

count <- cbind(summary(factor(atributos[,2])))

V(rede.partidos)$name <- name
V(rede.partidos)$count <- count[,1]
```

Agora é só plotar a rede de partidos usando os labels dos partidos que criamos e usando a contagem como tamanho.

``` r
plot(rede.partidos, layout=layout_with_kk(rede.partidos), vertex.label=V(rede.partidos)$name,
     vertex.size=V(rede.partidos)$count)
title(main="Brazilian Parties Network - Parliament\n2016-03-23", 
      xlab="Size = Number of Deputies\nEdge = Co-affiliation in Commissions")
```

![](/img/2016-04-01-brasillegis_files/figure-markdown_github/brasillegis15-1.png)

Por hoje é só. Se tiverem dúvidas, não hesitem em perguntar. Fiquem sempre de olho no site do [GIARS](http://www.giars.ufmg.br/), o Grupo Interdisciplinar de Pesquisa em Análise de Redes Sociais da UFMG do qual faço parte. A [página de Facebook do grupo](https://www.facebook.com/giarsufmg) também é atualizada constantemente.

Divirtam-se! Um grande abraço a todos!
