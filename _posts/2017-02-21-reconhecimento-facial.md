---
layout: post
title: Reconhecimento facial com Python e OpenCV - Machine Learning
author: Neylson Crepalde
date: 2017, February 21
tags: [Python, Machine Learning, Facial Recognition, OpenCV]
---

Olá. Hoje vamos utilizar uma implementação de *machine learning* para reconhecimento facial usando o Python 2.7 e a biblioteca [OpenCV 3](http://opencv.org/). Vamos começar carregando algumas bibliotecas necessárias para nosso trabalho de hoje. O *numpy* é necessário para o OpenCV. Da biblioteca *scipy*, vamos importar a função de moda. Além disso, carregamos as bibliotecas *os* para mudar a pasta de trabalho no Python, *time* para gerar nomes de bancos de dados atualizados por data e hora e *csv* para exportar arquivos . Isso tem se mostrado bem importante na minha experiência. Eu mesmo já perdi alguns dados rodando scripts de coleta de dados sem atualizar o nome do arquivo de saída. Desse modo, um banco de quase 1Gb de dados foi sobrescrito por outro de 200 Mb :(

### Plano de vôo

Nosso objetivo hoje é usar o reconhecimento facial para contar os rostos em algumas imagens. Serão usados 4 modelos já treinados e implementados no OpenCV para isso. Os resultados de cada um dos modelos serão tabulados junto com a média das quatro medidas, a moda e a variância.

### Instalando o OpenCV

A instalação do OpenCV 3 não é algo trivial. Eu encontrei alguma dificuldade mas informações no stackoverflow e a boa vontade dos amigos do grupo de Facebook [Python Brasil](https://www.facebook.com/groups/python.brasil) foram imprescindíveis. Em breve, farei também uma postagem apenas focando na instalação do OpenCV. Vamos aos códigos.


```python
import numpy as np
from scipy.stats import mode
import cv2
import os
import csv
import time
```

Agora, vamos mudar a pasta de trabalho, para onde estão alocadas as imagens que vamos utilizar. Depois disso, vamos criar um arquivo .csv de saida que receberá em seu nome a data e hora do processo.


```python
path = 'C:/Users/Admin2/Documents/RASTREIA/faces'
os.chdir(path)

#Salva o arquivo com titulo atualizado por data e hora
titulo = time.strftime("%Y-%m-%d") + '_' + time.strftime("%H-%M-%S")
saida = open('face_recon_'+titulo+'.csv', 'w')
export = csv.writer(saida, quoting=csv.QUOTE_NONNUMERIC)
```

Agora, vamos listar todos os arquivos .jpg existentes na pasta de trabalho.


```python
file_list = []

for file in os.listdir(path):
    if file.endswith(".jpg"):
        file_list.append(file)
```

Agora, mãos na massa. Criaremos um loop *for* para realizar as mesmas tarefas para cada foto. O algoritmo está construído da seguinte maneira:

1\. Estabelecemos 4 objetos com os quatro classificadores;

2\. Lê a imagem;

3\. Transforma a imagem em tons de cinza (50? kkkkkk);

4\. Executamos as quatro classificações e armazenamos os resultados em objetos;

5\. Cria uma lista com os objetos de resultados;

6\. Para cada resultado, plotamos um retângulo onde foram encontradas faces pelos classificadores;

7\. Diz ao usuário quantas faces foram encontradas;

8\. Cria uma lista com os resultados, transforma num array e calcula média, moda e variância;

9\. Escreve no banco de dados .csv o nome do arquivo da imagem, o número de faces, média, moda e variância;

10\. Exibe uma mensagem reconfortante de missão cumprida com os dizeres "Cabô, manolo!" (by [Wesley Matheus](https://www.facebook.com/wesley.matheus.777701)).

Há ainda uma opção para exibir, uma por uma, as figuras com os retângulos. Se forem apenas algumas poucas imagens, é interessante verificar em tempo real como está o reconhecimento. Se forem umas 500 imagens pode ser que você gaste um tempinho fazendo isso... Melhor não... Para habilitar, é só "descomentar" as três linhas.

### O comando detectMultiScale

O comando chave do reconhecimento facial que aplica os modelos já implementados no OpenCV às imagens recebe os seguintes argumentos: a própria imagem, um fator escalar que pode ser corrigido conforme o tamanho da imagem para melhorar a qualidade da classificação, um número mínimo de vizinhos, e o tamanho mínimo da área a ser investigada pelo classificador em pixels. Áreas menores do que o estabelecido aqui serão ignoradas.

*detectMultiScale(image[, scaleFactor[, minNeighbors[, flags[, minSize[, maxSize]]]]]) -> objects*

Vamos aos códigos!


```python
for file in file_list:
    #Para cada arquivo na lista faça:
    #Estabelece os classificadores de face
    face_cascade = cv2.CascadeClassifier('C:/Users/Admin2/OpenCV3/opencv/sources/data/haarcascades/haarcascade_frontalface_default.xml')
    face_alt_cascade = cv2.CascadeClassifier('C:/Users/Admin2/OpenCV3/opencv/sources/data/haarcascades/haarcascade_frontalface_alt.xml')
    face_alt2_cascade = cv2.CascadeClassifier('C:/Users/Admin2/OpenCV3/opencv/sources/data/haarcascades/haarcascade_frontalface_alt2.xml')
    face_alt_tree_cascade = cv2.CascadeClassifier('C:/Users/Admin2/OpenCV3/opencv/sources/data/haarcascades/haarcascade_frontalface_alt_tree.xml')
    
    #Lê a imagem e converte para escala de cinza
    img = cv2.imread(file)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    
    #Faz as classificações
    faces = face_cascade.detectMultiScale(gray, 1.1, 5, minSize=(30,30))
    faces2 = face_alt_cascade.detectMultiScale(gray, 1.1, 5, minSize=(30,30))
    faces3 = face_alt2_cascade.detectMultiScale(gray, 1.1, 5, minSize=(30,30))
    faces4 = face_alt_tree_cascade.detectMultiScale(gray, 1.1, 5, minSize=(30,30))
    
    #Organiza numa as classificações numa lista para loop
    classifiers = [faces, faces2, faces3, faces4]
    
    # Coloca os quadrados nas faces
    for classifier in classifiers:
        for (x,y,w,h) in classifier:
            cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
            roi_gray = gray[y:y+h, x:x+w]
            roi_color = img[y:y+h, x:x+w]
        
        print("Para a imagem "+file+", foram encontradas {0} faces!".format(len(classifier)))
        
        #Exibe as imagens com retãngulos. Para exibir, descomente as três linhas abaixo
        #cv2.imshow('img',img)
        #cv2.waitKey(0)
        #cv2.destroyAllWindows()
    
    # Exibe a média, variância e moda de cada classificador
    encontrados = []
    
    for (classifier) in classifiers:
        x = format(len(classifier))
        encontrados.append(x)
    
    encontrados = np.asarray(encontrados, dtype = np.float16)
    media = np.mean(encontrados)
    variancia = np.var(encontrados)
    moda = float(mode(encontrados)[0])
    
    if file == file_list[0]:
        export.writerow(["imagem","media","variancia","moda"])
        export.writerow([file, media, variancia, moda])
    else:
        export.writerow([file, media, variancia, moda])

saida.close()

print 'Cabô, manolo!'
```

    Para a imagem galera2.jpg, foram encontradas 7 faces!
    Para a imagem galera2.jpg, foram encontradas 3 faces!
    Para a imagem galera2.jpg, foram encontradas 3 faces!
    Para a imagem galera2.jpg, foram encontradas 0 faces!
    Para a imagem image_preview (1).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (1).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (1).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (1).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (2).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (2).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (2).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (2).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (3).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (3).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (3).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (3).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (4).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (4).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (4).jpg, foram encontradas 0 faces!
    Para a imagem image_preview (4).jpg, foram encontradas 0 faces!
    Para a imagem image_preview.jpg, foram encontradas 0 faces!
    Para a imagem image_preview.jpg, foram encontradas 0 faces!
    Para a imagem image_preview.jpg, foram encontradas 0 faces!
    Para a imagem image_preview.jpg, foram encontradas 0 faces!
    Para a imagem Inauguracao-placa-turma.jpg, foram encontradas 2 faces!
    Para a imagem Inauguracao-placa-turma.jpg, foram encontradas 2 faces!
    Para a imagem Inauguracao-placa-turma.jpg, foram encontradas 3 faces!
    Para a imagem Inauguracao-placa-turma.jpg, foram encontradas 0 faces!
    Para a imagem letras 2010.jpg, foram encontradas 18 faces!
    Para a imagem letras 2010.jpg, foram encontradas 19 faces!
    Para a imagem letras 2010.jpg, foram encontradas 19 faces!
    Para a imagem letras 2010.jpg, foram encontradas 15 faces!
    Para a imagem turma-2010 (1).jpg, foram encontradas 0 faces!
    Para a imagem turma-2010 (1).jpg, foram encontradas 0 faces!
    Para a imagem turma-2010 (1).jpg, foram encontradas 0 faces!
    Para a imagem turma-2010 (1).jpg, foram encontradas 0 faces!
    Para a imagem turma-2010-2.jpg, foram encontradas 22 faces!
    Para a imagem turma-2010-2.jpg, foram encontradas 14 faces!
    Para a imagem turma-2010-2.jpg, foram encontradas 17 faces!
    Para a imagem turma-2010-2.jpg, foram encontradas 5 faces!
    Para a imagem Turma-2010.jpg, foram encontradas 47 faces!
    Para a imagem Turma-2010.jpg, foram encontradas 30 faces!
    Para a imagem Turma-2010.jpg, foram encontradas 33 faces!
    Para a imagem Turma-2010.jpg, foram encontradas 10 faces!
    Para a imagem Turma2010.jpg, foram encontradas 7 faces!
    Para a imagem Turma2010.jpg, foram encontradas 7 faces!
    Para a imagem Turma2010.jpg, foram encontradas 7 faces!
    Para a imagem Turma2010.jpg, foram encontradas 3 faces!
    Cabô, manolo!
    

Depois disso, podemos importar o banco de dados para o Python (ou R) e verificar como foi a classificação. Vejam também um exemplo de uma imagem após o reconhecimento:


```python
import pandas as pd
dados = pd.read_csv('face_recon_2017-02-21_11-38-28.csv')
print(dados)
```

                             imagem  media  variancia  moda
    0                   galera2.jpg   3.25     6.1875   3.0
    1         image_preview (1).jpg   0.00     0.0000   0.0
    2         image_preview (2).jpg   0.00     0.0000   0.0
    3         image_preview (3).jpg   0.00     0.0000   0.0
    4         image_preview (4).jpg   0.00     0.0000   0.0
    5             image_preview.jpg   0.00     0.0000   0.0
    6   Inauguracao-placa-turma.jpg   1.75     1.1875   2.0
    7               letras 2010.jpg  17.75     2.6875  19.0
    8            turma-2010 (1).jpg   0.00     0.0000   0.0
    9              turma-2010-2.jpg  14.50    38.2500   5.0
    10               Turma-2010.jpg  30.00   174.5000  10.0
    11                Turma2010.jpg   6.00     3.0000   7.0
    

![](/img/2017-02-21-reconhecimento-facial/face_rec_marcado.jpg)

Por hoje é só. Comentários são sempre bem vindos. Até a próxima! Abraços
