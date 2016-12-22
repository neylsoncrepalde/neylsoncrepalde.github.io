---
layout: post
title: The Walking DEAD - um modelo de difusão social para o apocalipse zumbi!
author: Neylson Crepalde
date: 2016, December 22
tags: [Python, rstats, Social Network Analysis, Network Diffusion Models, Infection Dynamics]
---


Por uma série de motivos (inclua-se aqui meu problema de pesquisa de tese, curiosidade sobre ABM's, o vício da minha cunhada em Netflix) cheguei até o excelente artigo de Riebling e Schmitz (2016) intitulado **ZombieApocalypse: Modeling the social dynamics of infection and rejection.** O artigo é aberto e pode ser visualizado [aqui](http://journals.sagepub.com/doi/full/10.1177/2059799115622767). Sua ideia é bastante engenhosa: eles usam um modelo de difusão em redes (*network diffusion model*) para modelizar o alastrar de uma infecção zumbi generalizada. Neste post, vamos replicar uma versão simples do modelo tentando entender a implementação de um modelo de simulação computacional de agentes em redes. O post foi amplamente inspirado num Jupyter Notebook publicado pelos autores e que pode ser conferido [aqui](https://github.com/jrriebling/ZombieApocalypseSim).

![](http://weknowyourdreams.com/images/zombies/zombies-04.jpg)

Os modelos de difusão são amplamente utilizados para estudar padrões de infecção e contágio na área da saúde utilizando técnicas da Análise de Redes Sociais (ARS). São um tipo de *Agent Based Modelin* ou Modelagem Baseada em Agentes, modelos onde agentes e suas ações são simulados computacionalmente. O [Rogério Barbosa](https://www.facebook.com/rogerio.barbosa.7528) tem uma série de posts muito bons sobre o método que podem ser vistos [aqui](https://sociaisemetodos.wordpress.com/2016/04/20/cachorros-artificiais-agentes-de-agent-based-modeling-usando-r-parte-1/). Na implementação desse tipo de modelos, o pesquisador programa as características dos agentes e do mundo social mais fundamentais de que precisa para tentar entender como os fenômenos no nível macro emergem das interações.

Para simular o *The Walking Dead*, vamos usar a distribuição Anaconda do Python 3.5. A distribuição pode ser baixada [aqui](https://www.continuum.io/downloads). Se é a primeira vez que você instala o Python, é importante instalar também alguns pacotes que não vem como *default* na distro Anaconda.

Se você é usuário de Windows, abra o prompt de comando clicando no botão do Windows e pesquisando por `cmd`. Se você é usuário de Ubuntu, abra o terminal com `Ctrl + Alt + t`. Para instalar os pacotes, use os comandos:

```linux
pip install simpy
pip install networkx
pip install nxsim
```

Vamos aos códigos.

## Carregando as bibliotecas


```python
import matplotlib.pyplot as plt
import networkx as nx
import pandas as pd
import numpy as np
import seaborn as sns
import random
import os
from nxsim import NetworkSimulation, BaseNetworkAgent, BaseLoggingAgent

sns.set_context('notebook') #colocando um aspecto mais atrativo nos plots com seaborn
```

## Algumas funções úteis


```python
def census_to_df(log, num_trials=1, state_id=1, dir_path='sim_01'):
    """Reads nxsim log files and returns the sum of agents with a given state_id at 
    every time interval of the simulation for every run of the simulation as a pandas
    DataFrame object."""
    D = {}
    for i in range(num_trials):
        name = 'Trial ' + str(i)
        trial = log.open_trial_state_history(dir_path=dir_path, 
                                             trial_id=i)
        census = [sum([1 for node_id, state in g.items() 
                       if node_id != 'topology' and state['id'] == state_id]) 
                  for t, g in trial.items()]
        D[name] = census      
    return pd.DataFrame(D)

def friends_to_the_end(log, num_trials=1, dir_path='sim_01'):
    """Reads nxsim log files and returns the number of human friends connected
    to every human at every time interval of the simulation for every run of
    the simulation as a pandas DataFrame object."""
    D = {}
    for i in range(num_trials):
        name = 'Trial ' + str(i)
        trial = log.open_trial_state_history(dir_path=dir_path, 
                                             trial_id=i)
        friends = [np.mean([state['friends'] for node_id, state in g.items() 
                       if node_id != 'topology' and state['id'] == 0]) 
                  for t, g in trial.items()]
        D[name] = friends    
    return pd.DataFrame(D)

def edge_count_to_df(log, num_trials=1, state_id=1, dir_path='sim_01'):
    """Reads nxsim log files and returns the edge count for the topology at every
    time interval of the simulation for every run of the simulation as a pandas
    DataFrame object."""
    D = {}
    for i in range(num_trials):
        name = 'Trial ' + str(i)
        trial = log.open_trial_state_history(dir_path=dir_path, 
                                             trial_id=i)
        edge_count = [len(trial[key]['topology']) for key in trial]
        D[name] = edge_count
    return pd.DataFrame(D)
```

## Topologia

Vamos declarar a topologia da rede sobre a qual as simulações irão acontecer. A principal diferença entre os modelos de difusão social e os ABM's comuns reside no pressuposto da independência das observações. Esse pressuposto não se sustenta em estruturas relacionais já que a mudança de apenas um laço muda toda a configuração da rede. O pacote *nxsim* nos permite rodar um modelo de simulações apenas dentro da topologia de uma rede dada. Vamos simular uma rede livre de escala com 100 nós, ou 100 pessoas.


```python
number_of_nodes = 100
G = nx.scale_free_graph(number_of_nodes).to_undirected()
```

## Classe de agentes

### Cenário 1 - Infestação Zumbi

Vamos agora definir a classe de agentes que nos interessa num cenário básico e as ações dos agentes. Nesse modelo preliminar, o que acontece nas rodadas é o seguinte: O estado de *humano* é definido como `self.state['id']==0` e o estado *Zumbi* como `self.state['id']==1`. A cada rodada, se o agente é humano ele verifica se tem pessoas próximas, "vizinhos", que estão infectados. Joga-se dados e se o valor obtido for menor do que o limite da infecção, ele se infecta. Depois, conta-se os amigos que ainda são humanos.


```python
class ZombieMassiveOutbreak(BaseNetworkAgent):
    def __init__(self, environment=None, agent_id=0, state=()):
        super().__init__(environment=environment, agent_id=agent_id, state=state)
        
        self.inf_prob = 0.2

    def run(self):
        while True:
            if self.state['id'] == 0:
                self.check_for_infection()
                self.count_friends()
                yield self.env.timeout(1)
            else:
                yield self.env.event()

    def check_for_infection(self):
        zombie_neighbors = self.get_neighboring_agents(state_id=1)
        for neighbor in zombie_neighbors:
            if random.random() < self.inf_prob:
                self.state['id'] = 1
                print(self.env.now, self.id, '<--', neighbor.id, sep='\t')
                break
                                
    def count_friends(self):
        human_neighbors = self.get_neighboring_agents(state_id=0)
        self.state['friends'] = len(human_neighbors)
```

Para iniciar a simulação, primeiro estabelecemos todos como humanos e escolhemos um "paciente zero", o primeiro zumbi! Nessa simulação, serão feitas até 28 rodadas. O processo todo será repetido 100 vezes.


```python
# Starting out with a human population
init_states = [{'id': 0, } for _ in range(number_of_nodes)]

# Randomly seeding patient zero
patient_zero = random.randint(0, number_of_nodes)
init_states[patient_zero] = {'id': 1}

# Setting up the simulation
sim = NetworkSimulation(topology=G, states=init_states, agent_type=ZombieMassiveOutbreak, 
                        max_time=28, num_trials=100, logging_interval=1.0, dir_path='sim_01')

# Running the simulation
sim.run_simulation()
```

    Starting simulations...
    ---Trial 0---
    Setting up agents...
    1	0	<--	49
    1	17	<--	0
    1	19	<--	0
    1	25	<--	0
    1	37	<--	0
    1	38	<--	0
    .............

    11	9	<--	1
    11	74	<--	1
    12	87	<--	6
    13	29	<--	55
    18	98	<--	64
    Written 28 items to pickled binary file: sim_01/log.99.state.pickled
    Simulation completed.


Do mesmo modo como a modelagem baseada em agentes, o modelo de difusão social está preocupado com a análise dos resultados macro das interações. Vamos olhar para como as infecções acontecem no tempo.


```python
zombies = census_to_df(BaseLoggingAgent, 100, 1, dir_path='sim_01').T
humans = census_to_df(BaseLoggingAgent, 100, 0, dir_path='sim_01').T

plt.plot(zombies.mean(), color='r')
plt.fill_between(zombies.columns, zombies.max(), zombies.min(), color='r', alpha=.33)

plt.plot(humans.mean(), color='g')
plt.fill_between(humans.columns, humans.max(), humans.min(), color='g', alpha=.33)

plt.title('Simple ZombieApokalypse, $P_{inf}=0.2$')
plt.legend(['Zombies', 'Humans'], loc=7, frameon=True)
plt.xlim(xmax=27)
plt.xticks(np.arange(0, 28., 3), tuple(range(1, 29, 3)))
plt.ylabel('Population')
plt.xlabel('Simulation time')
plt.show()
```


![svg](/img/2016-12-22-the-walking-dead/output_13_0.svg)


### Cenário 2 - Escapar!!

Vamos programar um segundo cenário para nossa infestação zumbi. Se eu algum dia encontrasse com um zumbi, sem pestenejar, sairia correndo desenfreadamente sem olhar pra trás e gritando como uma garotinha(!!!). Nossos agentes virtuais também devem ter esse direito. Agora eles podem tentar escapar.

Na nova simulação, além de tudo o que já acontecia, antes de qualquer coisa, nosso agente pode perceber aqueles próximos que se tornaram zumbis e tentar escapar. Se os dados forem menores do que a "marca" de correr (`self.run_prob = 0.05`), o laço entre esses agentes é desfeito e o nosso agente pode sair gritando como uma garotinha livre do seu amigo zumbi (pelo menos desse).


```python
class ZombieEscape(BaseNetworkAgent):
    def __init__(self, environment=None, agent_id=0, state=()):
        super().__init__(environment=environment, agent_id=agent_id, state=state)
        
        self.inf_prob = 0.3
        self.run_prob = 0.05

    def run(self):
        while True:
            if self.state['id'] == 0:
                self.run_you_fools()
                self.check_for_infection()
                self.count_friends()
                yield self.env.timeout(1)
            else:
                yield self.env.event()

    def check_for_infection(self):
        zombie_neighbors = self.get_neighboring_agents(state_id=1)
        for neighbor in zombie_neighbors:
            if random.random() < self.inf_prob:
                self.state['id'] = 1 # zombie
                print('Infection:', self.env.now, self.id, '<--', neighbor.id, sep='\t')
                break
                
    def run_you_fools(self):
        zombie_neighbors = self.get_neighboring_agents(state_id=1)
        for neighbor in zombie_neighbors:
            if random.random() < self.run_prob:
                self.global_topology.remove_edge(self.id, neighbor.id)
                print('Rejection:', self.env.now, 'Edge:', self.id, neighbor.id, sep='\t')
                
    def count_friends(self):
        human_neighbors = self.get_neighboring_agents(state_id=0)
        self.state['friends'] = len(human_neighbors)
```

Rodando a simulação:


```python
# Starting out with a human population
init_states = [{'id': 0, } for _ in range(number_of_nodes)]

# Randomly seeding patient zero
patient_zero = random.randint(0, number_of_nodes)
init_states[patient_zero] = {'id': 1}

# Setting up the simulation
sim = NetworkSimulation(topology=G, states=init_states, agent_type=ZombieEscape, 
                        max_time=28, num_trials=100, logging_interval=1.0, dir_path='sim_02')

# Running the simulation
sim.run_simulation()
```

    Starting simulations...
    ---Trial 0---
    Setting up agents...
    Infection:	0	23	<--	77
    Infection:	2	2	<--	23
    Infection:	2	3	<--	2
    Infection:	2	8	<--	2
    Infection:	2	10	<--	2
    Infection:	2	13	<--	8
    Infection:	2	14	<--	2
    Infection:	2	17	<--	2
    Infection:	2	19	<--	17
    Infection:	2	35	<--	3
    Infection:	2	45	<--	2
    Rejection:	2	Edge:	48	2
    Rejection:	3	Edge:	0	19
    Infection:	3	0	<--	3
    
    .............................

    Infection:	8	64	<--	20
    Infection:	8	96	<--	1
    Infection:	9	98	<--	64
    Infection:	11	39	<--	12
    Infection:	15	69	<--	1
    Infection:	18	46	<--	11
    Written 28 items to pickled binary file: sim_02/log.99.state.pickled
    Simulation completed.


E agora, vamos ver como o número de zumbis e humanos se comporta no tempo:


```python
zombies = census_to_df(BaseLoggingAgent, 100, 1, dir_path='sim_02').T
humans = census_to_df(BaseLoggingAgent, 100, 0, dir_path='sim_02').T

## For later comparisons:
mean_escape_zombies = zombies.mean()
mean_escape_humans = humans.mean()

plt.plot(zombies.mean(), color='r')
plt.fill_between(zombies.columns, zombies.max(), zombies.min(), color='r', alpha=.2)

plt.plot(humans.mean(), color='g')
plt.fill_between(humans.columns, humans.max(), humans.min(), color='g', alpha=.2)

plt.title('Escape ZombieApokalypse, $P_{inf} = 0.3$, $P_{run} = 0.05$')
plt.legend(['Zombies', 'Humans'], loc=7, frameon=True)
plt.xlim(xmax=27)
plt.xticks(np.arange(0, 28., 3), tuple(range(1, 29, 3)))
plt.ylabel('Population')
plt.xlabel('Simulation time')
plt.show()
```


![svg](/img/2016-12-22-the-walking-dead/output_19_0.svg)


Apenas programando a possibilidade de escapar de um zumbi, podemos observar a esperança de sobrevivência da raça humana com cerca de 20 humanos remanescentes. Se essa possibilidade não existisse, o domínio zumbi total seria inevitável, como vimos na primeira simulaço. Agora, vamos comparar os dois cenários para verificar o remanescente humano em cada um.


```python
first_friends = friends_to_the_end(BaseLoggingAgent, 100, dir_path='sim_01').T
escape_friends = friends_to_the_end(BaseLoggingAgent, 100, dir_path='sim_02').T

plt.plot(first_friends.mean(), color='y')
plt.plot(escape_friends.mean(), color='b')

plt.title('Remaining Friends (Humans)')
plt.legend(['Zombie Outbreak','Escape'], loc='best', frameon=True)
plt.xlim(xmax=27)
plt.xticks(np.arange(0, 28., 3), tuple(range(1, 29, 3)))
plt.ylabel('Average number of friends')
plt.xlabel('Simulation time')
plt.show()
```

    /home/neylson/anaconda3/lib/python3.5/site-packages/numpy/core/_methods.py:59: RuntimeWarning: Mean of empty slice.
      warnings.warn("Mean of empty slice.", RuntimeWarning)



![svg](/img/2016-12-22-the-walking-dead/output_21_1.svg)


Podemos visualizar o fenômeno do contágio na rede. Para isso, vamos escolher uma das simulações (`trial_id`) e plotar num grafo os nós humanos e os nós infectados.


```python
pos=nx.fruchterman_reingold_layout(G)
for i in range(28): 
    
    cor = []
    for j in range(number_of_nodes):
        cor.append(BaseLoggingAgent.open_trial_state_history(dir_path="sim_01", trial_id=0)[i][j]['id'])
    
    cores = []
    for j in cor:
        if j == 1:
            cores.append('red')
r        else:
            cores.append('lightgreen')
    
    #plotando a rede
    plt.figure(i, figsize=(12, 8))
    plt.axis('off')
    nx.draw_networkx_nodes(G,pos,node_size=60,node_color=cores)
    nx.draw_networkx_edges(G,pos,alpha=.4)
    plt.title('Infection - Time '+str(i), size=16)
        
    if i < 9:
        plt.savefig('image00'+str(i+1)+'.png')
    elif i >= 9:
        plt.savefig('image0'+str(i+1)+'.png')


```

Podemos transformar todos os plots em um vídeo para facilitar a visualização com **ffmpeg**. Aqui está o código para fazer um slide show.

```linux
ffmpeg -framerate 1 -i image%03d.png output.mp4
```

Clique no grafo abaixo e veja o vídeo!

[![svg](/img/2016-12-22-the-walking-dead/output_23_0.svg)](https://raw.githubusercontent.com/neylsoncrepalde/diffusion_models/master/output.mp4)

Por hoje  só! Ano que vem tem mais! Abraços
