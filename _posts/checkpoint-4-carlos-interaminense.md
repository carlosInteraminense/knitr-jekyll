---
layout: post
title:  Analisando dados de séries com boxplot
published: true
tags: [htmlwidgets, r]

author: "Carlos Interaminense"
date: "May 29, 2017"
output: html_document
---





## Relatório Final



#### Avaliação dos dados que serão usados neste relatório:

Para responder as questões deste relatório, será usada uma base de dados coletada do IMDB ([www.imbd.com](www.imbd.com)) sobre séries de TV.  
Todas as análises foram feitas a partir de 4 séries escolhidas pelo autor deste relatório: Sense8, The 100, Daredevil e Flash.  
As questões, deste relatório, foram respondidas com base na análise das variáveis:  
        --> *User Rating*: classificação de usuários IMDB (para mais informações sobre *User Rating*, favor acessar [este link](http://www.imdb.com/help/show_leaf?votestopfaq));  
        --> *series_name*: indica os nomes das séries que foram avaliadas;   
        --> *season*: indica a temporada da série e   
        --> *series_ep*: indica qual o episódio da temporada.  

É esperado que *User Rating* seja um valor entre 0 e 10, para ter certeza que **todos** os valores estão nessa faixa de valor, podemos verificar o seguinte histograma:

![plot of chunk unnamed-chunk-3](/minhas-analises/figure/source/problema-1-cp-4/checkpoint-4-carlos-interaminense/unnamed-chunk-3-1.png)

A partir do histograma anterior, podemos perceber a variável *User rating* possui poucos valores até 6 e que a maioria está localizada entre 8 e 9, incluindo-os. Valores próximos de 10 são minoria também, assim como os valores abaixo de 6.

Para responder as perguntas a seguir, foi considerado o mesmo subconjunto de séries usado no checkpoint 1: Sense8, The100, Daredevil e Flash



#### Perguntas Escolhidas:

##### **1) Existe uma tendência de que as séries sempre melhoram ou pioram de uma temporada para outra?**
**R.** Para responder esta pergunta, será análisada as medianas de cada temporada de cada série. A mediana foi a medida estatística escolhida porque ela não "sofre" com valores destoantes. A seguir é apresentado os gráficos das medianas do *UserRating* para cada temporada de cada série.
Para melhor entendimento do porquê que a mediana também foi usada, considere o gráfico da série **Daredevil** abaixo, percebe-se que o valor da mediana da segunda temporada é maior do que o da primeira. Portanto, isso significa que pelo menos metade dos episódios da segunda temporada de Daredevil teve melhor *UserRating* do que os da primeira. 

![plot of chunk unnamed-chunk-5](/minhas-analises/figure/source/problema-1-cp-4/checkpoint-4-carlos-interaminense/unnamed-chunk-5-1.png)

Ao observar o gráfico acima, percebe-se que não existe uma tendência que as séries sempre melhoram ou sempre pioram de uma temporada para outra. Existem dois exemplos, do subconjunto usando, que as temporadas foram melhores de uma temporada para a outra, que foram: Sense8 e Daredevil. Porém, para The 100 e Flash isso não acontece, pois as temporadas não seguem uma melhoria ou um decaimento entre todas temporadas. Percebe-se que da primeira para a segunda temporada de ambas (The 100 e Flash) existe uma melhora, porém, entre a segunda e a terceira temporada há um decaimento.


##### **2) O último episódio de cada temporada é, em geral, mais bem avaliado que os outros episódios da mesma temporada?**
Para responder esta pergunta, gráficos para cada temporada de cada série são apresentados a seguir, em que é possível visualizar as classificassões de cada episódio das temporadas e, com isso, perceber se o último episódio de cada temporada tem uma melhor avaliação que os demais da mesma temporada.  
Percebe-se que a quantidade de temporadas que tem o último episódio mais bem avaliado (6 temporadas) é superior a quantidade de temporadas que não tem (5 temporadas). Porém, a diferença é pequena (apenas 1), para o subconjunto de séries que foi considerado neste relatório. Portanto, para o conjunto de séries usado, o último episódio das temporadas foi o mais bem avaliado, no geral.
O resultado pode ser visto no gráfico a seguir:

![plot of chunk unnamed-chunk-6](/minhas-analises/figure/source/problema-1-cp-4/checkpoint-4-carlos-interaminense/unnamed-chunk-6-1.png)

#### Duas outras perguntas a partir dos resultados anteriores:
#### **3) A partir do subconjunto que foi escolhido, qual a série que teve a temporada mais regular? E a mais irregular?**

**R.** Para responder esta pergunta, foi computado o bloxpot de cada temporada das séries. Para saber qual a temporada mais regular, é preciso observar o bloxpot que tem uma menor distância inter quartis. Neste caso, podemos observar que a segunda temporada de Sense8 foi a temporada mais regular, seguida pela primeira temporada de Daredevil.  
A partir do gráfico a seguir, podemos prceber que a terceira temporada de The100 foi a temporada mais irregular do subconjunto de dados usados no presente trabalho.

![plot of chunk unnamed-chunk-7](/minhas-analises/figure/source/problema-1-cp-4/checkpoint-4-carlos-interaminense/unnamed-chunk-7-1.png)


{% highlight r %}
seriesEscolhidas %>% 
  group_by(series_name, season) %>%
  summarise(IQR = IQR(UserRating)) %>%
  arrange(IQR)
{% endhighlight %}



{% highlight text %}
## # A tibble: 11 x 3
## # Groups:   series_name [4]
##    series_name season   IQR
##          <chr>  <int> <dbl>
##  1      Sense8      2 0.200
##  2   Daredevil      1 0.300
##  3     The 100      1 0.300
##  4     The 100      4 0.400
##  5     The 100      2 0.425
##  6   Daredevil      2 0.500
##  7      Sense8      1 0.550
##  8       Flash      1 0.650
##  9       Flash      2 0.700
## 10       Flash      3 0.775
## 11     The 100      3 1.925
{% endhighlight %}

      
#### **4) A quantidade de episódios influencia na regularidade da temporada de uma série?**
**R.** Para responder esta pergunta, temos que primeira saber a quantidade de episódios por temporada de cada série, para isso, um histograma foi computada para cada série, que podemos ver na figura a seguir.

{% highlight text %}
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
{% endhighlight %}

![plot of chunk unnamed-chunk-9](/minhas-analises/figure/source/problema-1-cp-4/checkpoint-4-carlos-interaminense/unnamed-chunk-9-1.png)

Percebe-se que a série que possui mais episódios por temporada é a Flash, seguda por The100, Sense8 e Daredevil.  
Se analisarmos o gráfico da questão anterior (**3**) podemos perceber que a série que tem as temporadas mais irregulares é Flash, pois, no geral, apresenta os boxplot com as maiores distâncias inter quartis que as demais, seguida por Daredevil, Sense8 e The100, que surpreendentimente apresenta as temporadas com menor irregularidade do subconjunto analisado. Perceba que, apesar de The100 ter sido analisada como a pior série no Checkpoint 1, por apresentar a menor mediana das avaliações gerais, ela apresenta as temporadas mais regulares.  

