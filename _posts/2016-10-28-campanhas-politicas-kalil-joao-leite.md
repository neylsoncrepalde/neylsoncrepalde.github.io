---
layout: post
title: Campanhas políticas de Kalil e João Leite no 2º turno em BH
author: Neylson Crepalde e Maria Alice Silveira
date: 2016, October 28
tags: [R programming, rstats, Ciência Política, Belo Horizonte, 2º turno, Kalil, João Leite]
---
#### Por Neylson Crepalde e Maria Alice Silveira

A disputa eleitoral pela prefeitura de Belo Horizonte tem se acirrado no 2º turno. Os candidatos Alexandre Kalil e João Leite trocam acusações e ferpas e também posições nas pesquisas. Resolvemos analisar, a partir das postagens realizadas nas páginas de Facebook dos candidatos, para onde e como é organizado o seu discurso político nesse momento. As páginas estão disponíveis nestes links: <https://www.facebook.com/AlexandreKalilOficial>, <https://www.facebook.com/joaoleiteBH>. Os dados foram raspados usando o aplicativo [Netvizz](https://apps.facebook.com/netvizz/).

Para os que quiserem replicar as análises, este post está disponível em sua versão **R Notebook** [aqui](http://neylsoncrepalde.github.io/kalil_jl.nb.html). Basta importá-lo no R. É necessário utilizar a versão preview do [RStudio (v1.0.44)](https://www.rstudio.com/products/rstudio/download/preview/).

Vamos às nuvens de palavras dos dois candidatos:

![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-2-1.png)


![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-4-1.png)

Na nuvem de palavras de Kalil é possível identificarmos que as principais palavras foram: “propostas”, “ideiaprabhfuncionar” e “conheça”. Em vários posts da página identificamos a hashtag \#ideiaprabhfuncionar, que apresentava uma proposta de governo do candidato. Vale lembrar que o candidato do PHS afirma em seu discurso que não é político. Apresentar propostas pode ser uma importante estratégia para mostrar que o candidato está compromissado com o governo, apesar de não ser político. O nome do candidato João Leite é citado na página, porém com menos intensidade.

Já na nuvem de palavras do candidato João leite podemos identificar que as palavras mais mencionadas durante esse período foram “João”, “Leite” e “Kalil”. O fato de citar o nome do outro candidato em sua página nos leva a entender que o candidato adotou um discurso de ataque ao seu opositor, provavelmente porque houve crescimento do candidato do PHS nas pesquisas de intenção de voto divulgadas 15 dias após o início do segundo turno. Isso pode ser confirmado também porque o candidato do PSDB tem adotado esse discurso nos nas propagandas eleitorais de rádio e TV e também na participação em debates.

Também podemos notar menções às palavras “pesquisa”, “margem”, “erro”. Nesta semana, o candidato João Leite divulgou uma pesquisa que mostra o seu crescimento e um cenário de empate técnico entre os candidatos. As outras palavras encontradas estão relacionadas a temas bastante comuns em campanhas e sempre abordados pelos candidatos, como saúde, educação, cultura entre outros.

Em nenhuma das duas páginas foram encontradas menções significativas à palavra “Galo”, ou Atlético Mineiro, clube de futebol que os dois candidatos estão ligados e são sempre vinculados pela opinião pública.

Vamos verificar agora como as palavras usadas pelos candidatos se agrupam hierarquicamente. Esse método nos permite ter uma ideia dos assuntos gerais tratados por eles. Além disso, vamos ver como essas palavras se relacionam em rede. Isso nos permite olhar, com maior profundidade, o surgimento de temáticas no discurso dos candidatos.


![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-6-1.png)


![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-7-1.png)


![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-9-1.png)


![](/img/kalil_jl_files/figure-markdown_github/unnamed-chunk-9-2.png)

Ao olharmos as palavras clusterizadas e as redes podemos identificar na página do Kalil dois grandes assuntos que emergem. O primeiro reúne palavras como “propostas”, “conheça”, “programa” entre outras. Isso nos leva a entender as palavras estavam nas postagens com a hashtag \#ideiaprabhfuncionar onde o candidato Kalil apresentava suas propostas e também convidada as pessoas para conhecer seu programa de governo. Já a menção a João Leite só foi identificada nas postagens que diziam respeito ao debate e também a respostas às acusações do candidato no PSDB.

Na página do João Leite identificamos só um grande componente conectado de palavras. No centro da rede encontramos as palavras “João”, “Leite”, “cidade”, “confiança”, “capitão”. Na parte superior da rede identificamos a relação das palavras “propostas”, “verdade”, “compromissos” e “lideranças”. Na parte inferior à esquerda da rede as palavras relacionadas são “eleitores”, “pesquisa”, “margem”, “erro”. Provavelmente se referem as postagens relacionadas ao crescimento do candidato na pesquisa recente.

[Neylson Crepalde](https://www.facebook.com/neylson.crepalde) é doutorando em sociologia pela UFMG. [Maria Alice](https://www.facebook.com/m.alicesilveira) é doutoranda em Ciência Política pela UFMG.
