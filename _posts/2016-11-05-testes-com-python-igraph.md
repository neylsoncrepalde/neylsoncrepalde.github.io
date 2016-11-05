---
layout: post
title: Testes com Python e Igraph
author: Neylson Crepalde
date: 2016, November 05
tags: [Python 3.5, Social Network Analysis, Igraph]
---

Hoje vamos realizar algumas análises com Python e o famoso pacote *Igraph*, uma das biliotecas mais usadas para análise de redes sociais (SNA) em diversas linguagens. Eu mesmo comecei a trabalhar com análise de redes usando *Igraph* no **R**. Hoje, vamos testar algumas funções do *Igraph* no Python.

Lembrando que estamos usando a distribuição Anaconda do Python 3.5. Vamos aos códigos.

Primeiro, após importar as funções da biblioteca *Igraph*, vamos carregar a famosa rede *Zachary's Karate Club*.


```python
from igraph import *

karate = Nexus.get("karate")
karate.summary()
```




    "IGRAPH UNW- 34 78 -- Zachary's karate club network\n+ attr: Author (g), Citation (g), name (g), Faction (v), id (v), name (v), weight (e)"



Com o método `summary()` podemos observar que a rede possui 34 vértices e 78 laços. Ele possui alguns atributos da rede (Autor, Citação e nome), atributos dos vértices (Facção, id e nome) e um atributo de peso dos laços.

Vamos printar a citação correta deste banco de dados.


```python
karate['Citation']
```




    'Wayne W. Zachary. An Information Flow Model for Conflict and Fission in Small Groups. Journal of Anthropological Research Vol. 33, No. 4 452-473'



Agora, vamos investigar os nomes dos indivíduos.


```python
karate.vs['name']
```




    ['Mr Hi',
     'Actor 2',
     'Actor 3',
     'Actor 4',
     'Actor 5',
     'Actor 6',
     'Actor 7',
     'Actor 8',
     'Actor 9',
     'Actor 10',
     'Actor 11',
     'Actor 12',
     'Actor 13',
     'Actor 14',
     'Actor 15',
     'Actor 16',
     'Actor 17',
     'Actor 18',
     'Actor 19',
     'Actor 20',
     'Actor 21',
     'Actor 22',
     'Actor 23',
     'Actor 24',
     'Actor 25',
     'Actor 26',
     'Actor 27',
     'Actor 28',
     'Actor 29',
     'Actor 30',
     'Actor 31',
     'Actor 32',
     'Actor 33',
     'John A']



Vamos olhar agora para a "facção" de cada um:


```python
karate.vs['Faction']
```




    [1.0,
     1.0,
     1.0,
     1.0,
     1.0,
     1.0,
     1.0,
     1.0,
     2.0,
     2.0,
     1.0,
     1.0,
     1.0,
     1.0,
     2.0,
     2.0,
     1.0,
     1.0,
     2.0,
     1.0,
     2.0,
     1.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0,
     2.0]



Agora, vamos plotar a rede usando os nomes dos indivíduos como *labels*, atribuindo cores para cada facção, atribuindo o peso de cada laço à sua espessura na imagem e fazendo com que o tamanho varie pela centralidade de grau.


```python
lay = karate.layout("kk")
karate.vs["label"] = karate.vs["name"]

color = []
for faction in karate.vs['Faction']:
    if faction == 1:
        x='red'
        color.append(x)
    elif faction == 2:
        x='lightgreen'
        color.append(x)

karate.vs["color"] = color

plot(karate, bbox=(600,400), layout=lay, vertex_size=karate.degree(), 
     edge_color='grey', edge_width=karate.es['weight']).show()
```

![](/img/karate_python.png)

Após alguns testes, verifiquei que o pacote *Igraph* no R é um pouco mais eficiente pela liberdade que o R tem em plotagens e por já ter várias ferramentas implementadas. Eu, por exemplo, tenho trabalhado muito com os modelos P\* ou *ERGM*'s. Esses modelos já estão bastante bem implementados no R no pacote *statnet*. No Python precisaríamos programá-lo *from scratch* o que me dá um certo pânico só de pensar... Esse esforço já foi feito e o código pode ser acessado [neste post](http://davidmasad.com/blog/ergms-from-scratch/). 

Coisas simples, como adicionar um título a um grafo gerado no Python, podem ser um pouco trabalhosas (veja [esta discussão](http://stackoverflow.com/questions/18250684/add-title-and-legend-to-igraph-plots) no StackOverflow).

Como já disse em outro post, algumas análises funcionam melhor no R, outras melhor no Python. Com esses testes, seguirei realizando análises de redes no R. Já raspagem de dados em rede online... 
