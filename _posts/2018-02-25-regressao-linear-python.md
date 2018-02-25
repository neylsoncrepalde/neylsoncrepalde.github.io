---
layout: post
title: Regressão Linear com Python
author: Neylson Crepalde
date: 2018, February 25
tags: [Python, Linear Regression, Data Science]
---

<!-- Loading mathjax macro -->
<!-- Load mathjax -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS_HTML"></script>
    <!-- MathJax configuration -->
    <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        tex2jax: {
            inlineMath: [ ['$','$'], ["\\(","\\)"] ],
            displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
            processEscapes: true,
            processEnvironments: true
        },
        // Center justify equations in code and markdown cells. Elsewhere
        // we use CSS to left justify single line equations in code cells.
        displayAlign: 'center',
        "HTML-CSS": {
            styles: {'.MathJax_Display': {"margin": 0}},
            linebreaks: { automatic: true }
        }
    });
    </script>
    <!-- End of mathjax configuration -->

Olá. Hoje vamos revisar como estimar um modelo de regressão linear por MQO no Python. Para isso, vamos usar *pandas*, *scipy* e a biblioteca *statsmodels*. Há algumas outras bibliotecas para estimação de modelos estatísticos em Python mas considero *statsmodels* a melhor delas pela facilidade e praticidade de uso. Outras bibliotecas como *scikit-learn* exigem um pouco mais de linhas de programação e, consequentemente, uma base matemática mais consolidada. Aliás, este é um ótimo exercício para quem está estudando modelos lineares: tente programá-lo no Python sem a ajuda dos comandos do pacote, *from scratch*. Sua compreensão vai melhorar bastante.

Antes de avançarmos para o código, vamos fazer uma breve revisão sobre os modelos lineares por MQO.

## Regressão Linear por mínimos quadrados ordinários (MQO)

A regressão linear é um método de análise estatística que nos permite estimar o valor de uma determinada variável resposta (variável dependente) como função de outras variáveis preditoras (variáveis independentes). Essa relação pode ser expressa na seguinte equação:

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <mrow class="MJX-TeXAtom-ORD">
    <mover>
      <msub>
        <mi>y</mi>
        <mi>i</mi>
      </msub>
      <mo stretchy="false">&#x005E;<!-- ^ --></mo>
    </mover>
  </mrow>
  <mo>=</mo>
  <msub>
    <mi>&#x03B2;<!-- β --></mi>
    <mn>0</mn>
  </msub>
  <mo>+</mo>
  <msub>
    <mi>&#x03B2;<!-- β --></mi>
    <mn>1</mn>
  </msub>
  <msub>
    <mi>x</mi>
    <mrow class="MJX-TeXAtom-ORD">
      <mi>i</mi>
      <mn>1</mn>
    </mrow>
  </msub>
  <mo>+</mo>
  <msub>
    <mi>&#x03B2;<!-- β --></mi>
    <mn>2</mn>
  </msub>
  <msub>
    <mi>x</mi>
    <mrow class="MJX-TeXAtom-ORD">
      <mi>i</mi>
      <mn>2</mn>
    </mrow>
  </msub>
  <mo>+</mo>
  <mo>.</mo>
  <mo>.</mo>
  <mo>.</mo>
  <msub>
    <mi>&#x03B2;<!-- β --></mi>
    <mi>p</mi>
  </msub>
  <msub>
    <mi>x</mi>
    <mrow class="MJX-TeXAtom-ORD">
      <mi>i</mi>
      <mi>p</mi>
    </mrow>
  </msub>
  <mo>+</mo>
  <msub>
    <mi>&#x03F5;<!-- ϵ --></mi>
    <mi>i</mi>
  </msub>
</math>

onde $\hat{y_i}$ representa a variável dependente, $x_{i1}, x_{i2}, x_{ip}$ representam as variáveis independentes, os preditores da regressão, $\beta_0$ representa o *intercepto* da regressão, ou seja, o valor esperado de $y$ quando todos os preditores tem valor = 0, $\beta_1, \beta_2, \beta_p$ representam os coeficientes estimados da regressão e $\epsilon_i$ representa o termo do erro, ou seja, a diferença entre os valores esperados e os valores observados de $y_i$.

O método de estimação dos *mínimos quadrados ordinários* - MQO (do inglês *ordinary least squares* - OLS) é uma técnica de otimização que busca o melhor ajuste do modelo através da minimização dos quadrados do erro da regressão. A regressão linear é um método de análise bastante poderoso. Contudo, ela está ancorada em alguns pressupostos que precisam ser respeitados. Caso contrário, seus resultados tendem a ficar enviesados. São eles:

1. **A relação entre a variável resposta e os preditores é linear.**

2. **A variável resposta possui distribuição normal.**
Os testes estatísticos desse método são todos baseados no pressuposto da normalidade. Caso tentemos analisar uma variável que não possui distribuição normal com esse método, os resultados não serão bons.

3. **O termo do erro é não correlacionado com a variável resposta.**

4. **O termo do erro possui distribuição normal com média 0.**

5. **O termo do erro é homoscedástico**, 
ou seja, possui variância constante em toda a sua extensão.

6. **Os parâmetros populacionais são constantes,**
ou seja, valores fixos desconhecidos os quais serão estimados pela equação de regressão.

## Estimando MQO no Python

Vamos aos códigos. Primeiro, vamos gerar algumas variáveis aleatórias distribuídas normalmente, uma variável categórica binária e um erro normalmente distribuído para servir de banco de dados para nossa regressão. 


```python
%matplotlib inline
import scipy as sp
import pandas as pd
import matplotlib.pyplot as plt
import statsmodels.formula.api as sm

x1 = sp.random.normal(size=100)
x2 = sp.random.normal(size=100)
x3 = sp.random.normal(size=100)
x4 = sp.random.normal(size=100)
categoria = sp.random.binomial(n=1, p=.5, size=100)
erro = sp.random.normal(size=100)
```

Em seguida, vamos construir a variável resposta com parâmetros conhecidos para fins de comparação. A equação verdadeira pode ser expressa assim:

$$y = 3 + 2(x_1) + 0.65(x_2) + 3.14(x_3) - 1.75(x_4) + 1.5(dummy) + \epsilon$$

Vamos verificar, no final deste tutorial, o desempenho da análise de regressão em relação aos parâmetros verdadeiros. 


```python
y = 3 + 2*x1 + 0.65*x2 + 3.14*x3 - 1.75*x4 + 1.5*categoria + erro
```

Em seguida, vamos criar um DataFrame com essas variáveis.


```python
dic = {"y":y, "x1":x1, "x2":x2, "x3":x3, "x4":x4, "categoria":categoria}
dados = pd.DataFrame(data=dic)
dados   #Visualiza o DataFrame
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>categoria</th>
      <th>x1</th>
      <th>x2</th>
      <th>x3</th>
      <th>x4</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-1.027964</td>
      <td>0.005811</td>
      <td>0.735222</td>
      <td>0.613691</td>
      <td>2.028470</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1.193090</td>
      <td>0.258837</td>
      <td>-0.017317</td>
      <td>-0.821739</td>
      <td>8.377292</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1.225674</td>
      <td>-2.640500</td>
      <td>-0.889131</td>
      <td>-2.218702</td>
      <td>5.831579</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1.288136</td>
      <td>0.915106</td>
      <td>1.903477</td>
      <td>0.568446</td>
      <td>13.142979</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>-0.828994</td>
      <td>0.148843</td>
      <td>0.344978</td>
      <td>0.051499</td>
      <td>1.756007</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>-0.007645</td>
      <td>-1.902100</td>
      <td>0.942490</td>
      <td>-0.322518</td>
      <td>5.588279</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1</td>
      <td>0.088384</td>
      <td>-2.736063</td>
      <td>-0.215483</td>
      <td>-0.588779</td>
      <td>3.413264</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>-0.847198</td>
      <td>-1.718300</td>
      <td>-1.105616</td>
      <td>-0.505273</td>
      <td>-1.082864</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0</td>
      <td>-0.320159</td>
      <td>1.756683</td>
      <td>1.476829</td>
      <td>-1.037843</td>
      <td>8.531030</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1</td>
      <td>-0.487969</td>
      <td>-1.084512</td>
      <td>1.147471</td>
      <td>0.394563</td>
      <td>5.768816</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0</td>
      <td>-0.097839</td>
      <td>-0.655643</td>
      <td>-0.064105</td>
      <td>-1.649199</td>
      <td>5.679842</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0</td>
      <td>0.955571</td>
      <td>-0.853956</td>
      <td>0.749347</td>
      <td>-0.794555</td>
      <td>6.654270</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1</td>
      <td>-0.747153</td>
      <td>0.861207</td>
      <td>0.637005</td>
      <td>0.879909</td>
      <td>2.074678</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1</td>
      <td>1.184340</td>
      <td>-0.820462</td>
      <td>-0.798241</td>
      <td>0.272143</td>
      <td>2.424067</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0</td>
      <td>-0.941562</td>
      <td>-0.717335</td>
      <td>-1.962072</td>
      <td>-2.679478</td>
      <td>-2.208384</td>
    </tr>
    <tr>
      <th>15</th>
      <td>0</td>
      <td>0.061954</td>
      <td>1.247335</td>
      <td>-2.059399</td>
      <td>2.176928</td>
      <td>-7.798604</td>
    </tr>
    <tr>
      <th>16</th>
      <td>0</td>
      <td>1.671110</td>
      <td>1.880848</td>
      <td>-0.749895</td>
      <td>-0.011972</td>
      <td>5.476915</td>
    </tr>
    <tr>
      <th>17</th>
      <td>0</td>
      <td>-0.720185</td>
      <td>-0.048464</td>
      <td>-3.682315</td>
      <td>0.250626</td>
      <td>-9.847082</td>
    </tr>
    <tr>
      <th>18</th>
      <td>1</td>
      <td>-1.209025</td>
      <td>0.594774</td>
      <td>0.452681</td>
      <td>-0.639228</td>
      <td>6.160792</td>
    </tr>
    <tr>
      <th>19</th>
      <td>0</td>
      <td>0.666450</td>
      <td>1.340304</td>
      <td>1.219634</td>
      <td>0.508693</td>
      <td>7.987290</td>
    </tr>
    <tr>
      <th>20</th>
      <td>1</td>
      <td>-0.316183</td>
      <td>0.288424</td>
      <td>1.202293</td>
      <td>-1.018137</td>
      <td>10.800371</td>
    </tr>
    <tr>
      <th>21</th>
      <td>1</td>
      <td>0.172340</td>
      <td>-0.960865</td>
      <td>0.552074</td>
      <td>-0.132070</td>
      <td>7.261349</td>
    </tr>
    <tr>
      <th>22</th>
      <td>0</td>
      <td>0.447723</td>
      <td>0.817908</td>
      <td>-0.390960</td>
      <td>-0.497449</td>
      <td>3.733669</td>
    </tr>
    <tr>
      <th>23</th>
      <td>1</td>
      <td>0.369514</td>
      <td>1.160988</td>
      <td>-0.036910</td>
      <td>-1.412312</td>
      <td>10.157134</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1</td>
      <td>0.978639</td>
      <td>1.002424</td>
      <td>-0.726037</td>
      <td>-0.222755</td>
      <td>3.963156</td>
    </tr>
    <tr>
      <th>25</th>
      <td>0</td>
      <td>-1.301225</td>
      <td>0.381227</td>
      <td>1.235124</td>
      <td>2.317437</td>
      <td>0.102828</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0</td>
      <td>-0.460727</td>
      <td>0.415375</td>
      <td>-1.344034</td>
      <td>0.129310</td>
      <td>-2.840350</td>
    </tr>
    <tr>
      <th>27</th>
      <td>0</td>
      <td>-1.674894</td>
      <td>-0.219167</td>
      <td>-1.130843</td>
      <td>-1.038059</td>
      <td>-3.399894</td>
    </tr>
    <tr>
      <th>28</th>
      <td>0</td>
      <td>0.601086</td>
      <td>-0.880368</td>
      <td>-0.937390</td>
      <td>0.869635</td>
      <td>0.578146</td>
    </tr>
    <tr>
      <th>29</th>
      <td>1</td>
      <td>0.519467</td>
      <td>0.449896</td>
      <td>0.026430</td>
      <td>1.029819</td>
      <td>6.766147</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>0</td>
      <td>0.120537</td>
      <td>-3.120954</td>
      <td>1.473131</td>
      <td>0.032333</td>
      <td>6.469714</td>
    </tr>
    <tr>
      <th>71</th>
      <td>1</td>
      <td>0.228711</td>
      <td>-0.008374</td>
      <td>1.309662</td>
      <td>-0.660893</td>
      <td>11.122802</td>
    </tr>
    <tr>
      <th>72</th>
      <td>1</td>
      <td>-1.976085</td>
      <td>1.199087</td>
      <td>-0.390166</td>
      <td>0.566129</td>
      <td>-1.789751</td>
    </tr>
    <tr>
      <th>73</th>
      <td>0</td>
      <td>0.211299</td>
      <td>1.664436</td>
      <td>-0.631966</td>
      <td>-0.107809</td>
      <td>3.539535</td>
    </tr>
    <tr>
      <th>74</th>
      <td>0</td>
      <td>0.672581</td>
      <td>2.205682</td>
      <td>0.844343</td>
      <td>1.581004</td>
      <td>7.127058</td>
    </tr>
    <tr>
      <th>75</th>
      <td>1</td>
      <td>-1.755712</td>
      <td>-0.159558</td>
      <td>-0.861777</td>
      <td>0.688980</td>
      <td>-1.826065</td>
    </tr>
    <tr>
      <th>76</th>
      <td>0</td>
      <td>0.576626</td>
      <td>0.127073</td>
      <td>0.850716</td>
      <td>0.334624</td>
      <td>6.482678</td>
    </tr>
    <tr>
      <th>77</th>
      <td>1</td>
      <td>-0.173947</td>
      <td>-0.407799</td>
      <td>1.277835</td>
      <td>-0.174108</td>
      <td>7.722501</td>
    </tr>
    <tr>
      <th>78</th>
      <td>1</td>
      <td>1.793446</td>
      <td>0.316700</td>
      <td>1.076530</td>
      <td>1.611143</td>
      <td>9.772018</td>
    </tr>
    <tr>
      <th>79</th>
      <td>1</td>
      <td>-0.558026</td>
      <td>-0.178458</td>
      <td>-0.761499</td>
      <td>-0.015727</td>
      <td>-0.297279</td>
    </tr>
    <tr>
      <th>80</th>
      <td>0</td>
      <td>-0.682473</td>
      <td>0.270851</td>
      <td>-0.954590</td>
      <td>-0.484107</td>
      <td>0.030462</td>
    </tr>
    <tr>
      <th>81</th>
      <td>0</td>
      <td>-0.129743</td>
      <td>1.089140</td>
      <td>-0.638405</td>
      <td>1.458407</td>
      <td>-1.674821</td>
    </tr>
    <tr>
      <th>82</th>
      <td>0</td>
      <td>0.592878</td>
      <td>-0.235288</td>
      <td>0.035668</td>
      <td>-0.887291</td>
      <td>5.730212</td>
    </tr>
    <tr>
      <th>83</th>
      <td>1</td>
      <td>-0.845119</td>
      <td>-0.332484</td>
      <td>1.361238</td>
      <td>0.195806</td>
      <td>8.018649</td>
    </tr>
    <tr>
      <th>84</th>
      <td>0</td>
      <td>-1.032762</td>
      <td>0.357636</td>
      <td>0.218133</td>
      <td>-1.539139</td>
      <td>3.772423</td>
    </tr>
    <tr>
      <th>85</th>
      <td>0</td>
      <td>0.313567</td>
      <td>0.156096</td>
      <td>-0.538710</td>
      <td>-0.090811</td>
      <td>1.210189</td>
    </tr>
    <tr>
      <th>86</th>
      <td>0</td>
      <td>-1.606484</td>
      <td>1.537090</td>
      <td>0.326825</td>
      <td>-0.833555</td>
      <td>2.607500</td>
    </tr>
    <tr>
      <th>87</th>
      <td>1</td>
      <td>0.060540</td>
      <td>1.158225</td>
      <td>-1.465049</td>
      <td>0.788696</td>
      <td>-2.903798</td>
    </tr>
    <tr>
      <th>88</th>
      <td>0</td>
      <td>-0.557101</td>
      <td>1.614070</td>
      <td>-2.133689</td>
      <td>-0.114207</td>
      <td>-3.198860</td>
    </tr>
    <tr>
      <th>89</th>
      <td>0</td>
      <td>0.364759</td>
      <td>-0.754128</td>
      <td>-0.106209</td>
      <td>-0.626787</td>
      <td>4.098707</td>
    </tr>
    <tr>
      <th>90</th>
      <td>1</td>
      <td>0.519074</td>
      <td>-0.854893</td>
      <td>1.054564</td>
      <td>0.429074</td>
      <td>6.553203</td>
    </tr>
    <tr>
      <th>91</th>
      <td>1</td>
      <td>0.098454</td>
      <td>1.064374</td>
      <td>-0.140774</td>
      <td>0.533403</td>
      <td>5.680029</td>
    </tr>
    <tr>
      <th>92</th>
      <td>0</td>
      <td>1.435576</td>
      <td>-0.942384</td>
      <td>-1.706739</td>
      <td>0.765818</td>
      <td>-0.339778</td>
    </tr>
    <tr>
      <th>93</th>
      <td>1</td>
      <td>0.544879</td>
      <td>-0.614113</td>
      <td>-0.028148</td>
      <td>-1.792886</td>
      <td>8.194594</td>
    </tr>
    <tr>
      <th>94</th>
      <td>0</td>
      <td>-0.835324</td>
      <td>0.114021</td>
      <td>0.304025</td>
      <td>1.674463</td>
      <td>-1.785285</td>
    </tr>
    <tr>
      <th>95</th>
      <td>1</td>
      <td>-0.981402</td>
      <td>0.348162</td>
      <td>-0.006721</td>
      <td>0.343610</td>
      <td>1.657995</td>
    </tr>
    <tr>
      <th>96</th>
      <td>1</td>
      <td>-0.275160</td>
      <td>0.261869</td>
      <td>-2.552706</td>
      <td>-0.284243</td>
      <td>-3.720913</td>
    </tr>
    <tr>
      <th>97</th>
      <td>1</td>
      <td>0.104991</td>
      <td>0.547673</td>
      <td>0.886136</td>
      <td>-1.270264</td>
      <td>10.635857</td>
    </tr>
    <tr>
      <th>98</th>
      <td>1</td>
      <td>0.006807</td>
      <td>-0.489711</td>
      <td>0.319738</td>
      <td>1.301208</td>
      <td>0.025958</td>
    </tr>
    <tr>
      <th>99</th>
      <td>1</td>
      <td>-0.114278</td>
      <td>-0.511798</td>
      <td>0.248029</td>
      <td>0.973711</td>
      <td>3.129828</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 6 columns</p>
</div>



Prontinho. Agora, vamos dar uma olhada nas estatísticas descritivas e na distribuição da variável resposta. Lembre-se de que ela deve possuir distribuição normal.


```python
print("Estatísticas descritivas de y:")
dados['y'].describe()
```

    Estatísticas descritivas de y:





    count    100.000000
    mean       3.709140
    std        4.417438
    min       -9.847082
    25%        0.665959
    50%        3.753046
    75%        6.709719
    max       13.142979
    Name: y, dtype: float64




```python
# Elabora um histograma
plt.hist(y, color='red', bins=15)
plt.title('Histograma da variável resposta')
plt.show()
```


![png](/img/2018-02-25-regressao-linear-python/output_8_0.png)


A distribuição da variável parece "bem normal". Agora vamos estimar o modelo e mostrar os resultados.


```python
reg = sm.ols(formula='y~x1+x2+x3+x4+categoria', data=dados).fit()
print(reg.summary())
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                      y   R-squared:                       0.941
    Model:                            OLS   Adj. R-squared:                  0.938
    Method:                 Least Squares   F-statistic:                     301.7
    Date:                Sun, 25 Feb 2018   Prob (F-statistic):           2.98e-56
    Time:                        12:04:15   Log-Likelihood:                -148.15
    No. Observations:                 100   AIC:                             308.3
    Df Residuals:                      94   BIC:                             323.9
    Df Model:                           5                                         
    Covariance Type:            nonrobust                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    Intercept      2.9649      0.157     18.867      0.000       2.653       3.277
    x1             2.0837      0.118     17.692      0.000       1.850       2.318
    x2             0.7515      0.105      7.181      0.000       0.544       0.959
    x3             3.2290      0.106     30.330      0.000       3.018       3.440
    x4            -1.8808      0.107    -17.508      0.000      -2.094      -1.668
    categoria      1.3788      0.223      6.191      0.000       0.937       1.821
    ==============================================================================
    Omnibus:                        0.518   Durbin-Watson:                   2.065
    Prob(Omnibus):                  0.772   Jarque-Bera (JB):                0.655
    Skew:                           0.145   Prob(JB):                        0.721
    Kurtosis:                       2.730   Cond. No.                         2.83
    ==============================================================================
    
    Warnings:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.


Vamos começar pela primeira tabela apresentada. As medidas mais importantes para nós neste momento são o $R^2$ ajustado, a estatística de teste F, o p-valor dessa estatística e, caso queiramos comparar diferentes modelos, o log-likelihood, o *Akaike Information Criterion* (AIC) e o *Bayesian Information Criterion* (BIC). 

O $R^2$ ajustado nos dá uma medida de porcentagem da variância explicada pelo modelo. Este modelo tem um poder explicativo de 93,8%. Nada mau, hum? Mas não se engane! O $R^2$ não é uma medida de ajuste mas apenas nos diz o quanto de nossos dados esse modelo explica. O restante do que falta explicar é atribuído ao termo do erro, ou seja, todas as outras coisas que tem influência sobre nossa variável resposta mas que não estão no modelo ou não podem de alguma forma ser mensuradas. Em ciências naturais é comum obtermos um $R^2$ próximo de 100%. Já nas ciências sociais, costumamos ficar muito felizes quando conseguimos 30% dos dados explicados. A natureza dos dados estudados é bem diferente. A estatística de teste F e seu p-valor $< 0.001$ basicamente nos mostram que esse modelo é estatisticamente válido.

Quanto aos coeficientes da regressão, os $\beta$'s estimados, vejam os resultados. Parecem bem confiáveis, não é mesmo? O modelo pegou muito bem as rlações entre as variáveis e os valores verdadeiros estão todos dentro do intervalo de confiança estimado. Podemos interpretá-los da seguinte forma: quando todos os preditores são iguais a 0, o valor esperado de y é 2,96. Para cada aumento de 1 unidade em $x_1$, y aumenta, em média, 2,08. Para cada aumento de 1 unidade em $x_2$, y aumenta, em média, 0,75. Para cada aumento de 1 unidade em $x_3$, y aumenta, em média, 3,23. Para cada aumento de 1 unidade em $x_4$, y *diminui* (veja o sinal negativo no coeficiente), em média, 1,88. A variável categórica possui uma análise diferente. Os casos da categoria 1 y têm um valor esperado médio 1,38 maior do que os casos da categoria 0. O p-valor para todas os coeficientes estimados é menor que 0.001 o que mostra que eles são estatisticamente significativos.

Vamos agora plotar alguns gráficos de avaliação. Primeiro, vamos plotar um histograma dos resíduos ($\epsilon = y - \hat{y}$). Lembre-se de que os resíduos precisam ter distribuição normal com média 0. Em seguida, vamos plotar um gráfico de dispersão (*scatterplot*) dos dos valores preditos ($\hat{y}$) e resíduos. É preciso que os resíduos e a variável resposta sejam não correlacionados.


```python
y_hat = reg.predict()
res = y - y_hat

plt.hist(res, color='orange', bins=15)
plt.title('Histograma dos resíduos da regressão')
plt.show()
```


![png](/img/2018-02-25-regressao-linear-python/output_12_0.png)



```python
plt.scatter(y=res, x=y_hat, color='green', s=50, alpha=.6)
plt.hlines(y=0, xmin=-10, xmax=15, color='orange')
plt.ylabel('$\epsilon = y - \hat{y}$ - Resíduos')
plt.xlabel('$\hat{y}$ ou $E(y)$ - Predito')
plt.show()
```


![png](/img/2018-02-25-regressao-linear-python/output_13_0.png)


Agora, vamos observar apresentar apenas os coeficientes da regressão se quisermos facilitar a análise e traçar a reta de regressão entre as variáveis $y$ e $x_1$.


```python
coefs = pd.DataFrame(reg.params)
coefs.columns = ['Coeficientes']
print(coefs)
```

               Coeficientes
    Intercept      2.964863
    x1             2.083678
    x2             0.751478
    x3             3.229035
    x4            -1.880821
    categoria      1.378816



```python
plt.scatter(y=dados['y'], x=dados['x1'], color='blue', s=50, alpha=.5)
X_plot = sp.linspace(min(dados['x1']), max(dados['x1']), len(dados['x1']))
plt.plot(X_plot, X_plot*reg.params[1] + reg.params[0], color='r')
plt.ylim(-11,16)
plt.xlim(-2.5,3)
plt.title('Reta de regressão')
plt.ylabel('$y$ - Variável Dependente')
plt.xlabel('$x1$ - Preditor')
plt.show()
```


![png](/img/2018-02-25-regressao-linear-python/output_16_0.png)

