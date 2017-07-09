---
layout: post
published: true
tags: [htmlwidgets, r]

title: "Análise dos filmes que Adam Sandler participou."
author: "Carlos Interaminense"
date: "June 18, 2017"
output: html_document
---







{% highlight r %}
from_page <- read_html("https://www.rottentomatoes.com/celebrity/adam_sandler/") %>% 
    html_node("#filmographyTbl") %>%
    html_table(fill=TRUE) %>% # Faz parse
    as.tibble()

filmes = from_page %>% 
    filter(RATING != "No Score Yet", 
           `BOX OFFICE` != "—", 
           CREDIT != "Executive Producer") %>%
    mutate(RATING = as.numeric(gsub("%", "", RATING)), 
           `BOX OFFICE` = as.numeric(gsub("[$|M]", "", `BOX OFFICE`))) %>%
    filter(`BOX OFFICE` > 0) %>%
    filter(RATING != 0) %>%
    filter(`BOX OFFICE` != 'NA')
{% endhighlight %}

Neste post vamos analisar os dados dos filmes que Adam Sandler participou. A análise será com base no sucesso de público (bilheteria) e da crítica dos telespectadores, em que essas informações são do ([Rotten Tomatoes](https://www.rottentomatoes.com/celebrity/adam_sandler/)).

Em primeiro lugar, vamos analisar os dados d bilheteria e avaliação, em que ambos não podem ter valores negativos e a avaliação não pode ser maior que 100%. Como podemos observar nos gráficos a seguir (bilheteria e avaliação, respectivamente), todos os dados são válidos e, portanto, podemos continuar com nossa análise.


{% highlight r %}
filmes %>%
  ggplot(aes(x=`BOX OFFICE`)) +
  geom_histogram() +
  geom_rug() +
  xlab("Bilheteria") +
  ylab("Quantidade") +
  ggtitle("Bilheteria dos filmes")
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/minhas-analises/figure/source/problema-3-cp-1/2017-07-09-filmes-adam/unnamed-chunk-3-1.png)



{% highlight r %}
filmes %>%
  ggplot(aes(x = RATING)) +
  geom_histogram() +
  geom_rug() +
  xlab("Avaliação") +
  ylab("Quantidade") +
  ggtitle("Média das Avaliações dos telespectadores")
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/minhas-analises/figure/source/problema-3-cp-1/2017-07-09-filmes-adam/unnamed-chunk-4-1.png)

Após uma análise rápidas de cada dado que vamos usar neste post, vamos analisar as duas variáveis juntas tentando dividir os filmes em grupos, como podemos observar nos gráficos a seguir:




{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(Avaliação, Bilheteria) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")

plota_hclusts_2d(agrupamento_h_2d, 
                 filmes, 
                 c("Avaliação", "Bilheteria"), 
                 linkage_method = "complete", ks = 1:6)
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/minhas-analises/figure/source/problema-3-cp-1/2017-07-09-filmes-adam/unnamed-chunk-6-1.png)

Abaixo, apenas o gráfico com 3 grupos foi escolhido para realizarmos uma análise dos filmes desses grupo.


{% highlight r %}
plota_hclusts_2d_modificado(agrupamento_h_2d, 
                 filmes, 
                 c("Avaliação", "Bilheteria"), 
                 linkage_method = "complete", ks = 3)
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}

Podemos "Rotular" os três grupos do gráfico acima como:  
1-  "Fracassos²", visto que esses foram os filmes que, no geral, tiveram as piores avaliações e os menores públicos (bilheteria);  
2- Faltou _Marketing_, pois esses filmes não tiveram bons públicos (bilheteria), mas foram os mais bem avaliados, segundo o ([Rotten Tomatoes](https://www.rottentomatoes.com/celebrity/adam_sandler/)); e  
3- Bons de _Marketing_, pois possuem um bom público (bilheteria) e não possuem boas avaliações (menos que 50%).

Um exemplo de filme que eu assisti foi Click que, segundo o gráfico acima, está no grupo 3. Se eu fosse um avaliador, daria uma avaliação maior para esse filme que teve uma avaliação de 32%. Portanto acho que ele deveria ficar num outro grupo que não existe no gráfico acima, com boas avaliações e boas bilheterias.
  
  
  
  
PS: é importante lembrar que foi pedido que os códigos fossem mostrados. =)
