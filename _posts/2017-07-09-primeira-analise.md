---
layout: post
title:  Primeira vez no R (Problema 1 Checkpoint 1)
date: 2017-07-09 01:01:23
published: true
tags: [htmlwidgets, r]

author: "Carlos Interaminense"
date: "19 de maio de 2017"
output: html_document
---


{% highlight r %}
library("ggplot2")
library("dplyr")
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'dplyr'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:stats':
## 
##     filter, lag
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
{% endhighlight %}



{% highlight r %}
library("tidyr")
library("readr")
{% endhighlight %}



## R Markdown


{% highlight r %}
dados = read.csv(file="series_from_imdb.csv", stringsAsFactors = FALSE)
{% endhighlight %}

Para responder as questões deste relatório, será usada uma base de dados coletada do IMDB ([www.imbd.com](www.imbd.com)) sobre séries de TV.  
Todas as análises foram feitas a partir de 4 séries escolhidas pelo autor deste relatório: Sense8, The 100, Daredevil e Flash.  
As questões, deste relatório, foram respondidas com base na análise da variável *User Rating*, que é a classificação de usuários IMDB (para mais informações sobre *User Rating*, favor acessar [este link](http://www.imdb.com/help/show_leaf?votestopfaq)).  

É esperado que *User Rating* seja um valor entre 0 e 10, para ter certeza que **todos** os valores estão nessa faixa de valor, podemos verificar o seguinte histograma:


{% highlight r %}
dados %>%
  ggplot(aes(x = UserRating)) + 
  geom_histogram()
{% endhighlight %}



{% highlight text %}
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/minhas-analises/figure/source/problema-1-cp-1/2017-07-09-primeira-analise/unnamed-chunk-3-1.png)

A partir do histograma anterior, podemos perceber a variável *User rating* possui poucos valores até 6 e que a maioria está localizada entre 8 e 9, incluindo-os. Valores próximos de 10 são minoria também, assim como os valores abaixo de 6.

1) Escolha um subconjunto de séries (eu sugiro entre 3 e 6) para usar nesse problema:  
   R. Escolhi 4 séries que assisti e gostei bastante: "Sense8, The 100, Daredevil e Flash.

{% highlight r %}
seriesEscolhidas=  dados %>% filter(series_name %in% c("Sense8", "The 100", "Daredevil", "Flash"))
{% endhighlight %}

2) Responda as seguintes duas perguntas:  
a. Qual das séries que você escolheu é mais bem avaliada no IMDB? A diferença é grande? Pequena?  
  R. Para avaliar as séries foi usada a variável UserRating, explicada anteriormente. Como cada episódio possui um UserRating, foi considerado todos os valores de UserRatig de cada série e a mediana foi usada como o valor de avaliação das séries.  
  A mediana foi a medida estatística escolhida porque ela não "sofre" com valores destoantes.  
  Então, conclui-se que Sense8 foi a série mais bem avaliada no IMDB, entretanto, percebe-se que a diferença entre as três primeiras séries é relativamente baixa (0.55, diferença entre o primeiro lugar e o último).  


{% highlight r %}
seriesEscolhidas %>% 
  group_by(series_name) %>%
    summarise(mediana = median(UserRating)) %>%
      arrange(-mediana)
{% endhighlight %}



{% highlight text %}
## # A tibble: 4 x 2
##   series_name mediana
##         <chr>   <dbl>
## 1      Sense8    9.00
## 2   Daredevil    8.95
## 3       Flash    8.70
## 4     The 100    8.35
{% endhighlight %}


{% highlight r %}
seriesEscolhidas %>%
  ggplot(aes(x = series_name, y = UserRating)) +
  geom_boxplot() +
  geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/minhas-analises/figure/source/problema-1-cp-1/2017-07-09-primeira-analise/unnamed-chunk-6-1.png)

b. Qual das séries que você escolheu tem episódios de qualidade mais irregular segundo o IMDB? A diferença é grande? Pequena?  
  R.  Visualmente, a partir dos gráficos a seguir, em que cada ponto do gráfico representa um episódio da série no eixo x e o valor do *User rating* no eixo y, percebe-se que a série The 100 apresenta uma qualidade mais irregular que as demais. Pois se analisarmos a reta que mais se aproxima de todos os pontos, pode-se perceber que ela é a série com qualidade mais irregular.  
  A diferença de irregularidade de qualidade entre as séries é pequena, mas pode-se perceber que Sense8 e Daredevil têm irregularidades parecidas assim como Flash e The 100.
  

{% highlight r %}
seriesEscolhidas %>%
  ggplot(aes(x = series_ep, y = UserRating)) + 
  geom_line() + 
  geom_point() +
  facet_wrap(~series_name, scale="free_x") +
  geom_smooth(method=lm)
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/minhas-analises/figure/source/problema-1-cp-1/2017-07-09-primeira-analise/unnamed-chunk-7-1.png)
