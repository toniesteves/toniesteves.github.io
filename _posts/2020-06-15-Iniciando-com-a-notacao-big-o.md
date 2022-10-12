---
layout: post
title: Iniciando com a notação Big O.
description: A notação Big O é um tópico importante e sua importância universal origina-se do fato de descrever a eficiência do código escrito em qualquer linguagem de programação.
date: 2020-06-15 15:01:35 +0300
author: toni
image: '/images/posts/20200615/cover.jpg'
image_caption: 'Photo by [Dan Cristian Pădureț](https://unsplash.com/photos/xJLN32FO7AY) on [Unsplash](https://unsplash.com/)'
tags: [Complexity, Algorithms, Data Structures, Big O Notation, Python]
featured: true
---

A notação Big O é um tópico importante e sua importância universal origina-se do fato de descrever a eficiência do código escrito em qualquer linguagem de programação. Como esse é um tópico grande, este post abordará o básico sobre como reconhecer os diferentes tipos de complexidade do ***Big O***, como ***O(n)***, ***O(2n)***, ***O(n²)***, ***O(log n)***, etc.

Complexidade de tempo e espaço
A notação Big O pode ser utilizada para descrever a complexidade de uma seção de código em termos de tempo de execução e espaço. Assim, a complexidade do tempo do Big O descreve o tempo de execução no pior cenário.

Já a complexidade do espaço Big O, descreve quanta memória é necessária para executar uma seção de código no pior cenário. Por exemplo, um loop for que copia uma matriz precisará de muito mais memória para ser executado do que um loop que simplesmente modifica uma matriz existente.

Vejamos duas funções para ver como o Big O descreve os tempos de execução.

<script src="https://gist.github.com/toniesteves/84395d12fb43184d033e8567735571a5.js"></script>

A função acima acessa e atribui apenas um valor em um local, o tempo de execução será o mesmo, se o comprimento da matriz for 10 ou 10 milhões. Se o tempo de execução for constante, independentemente da entrada, diz-se que a função possui uma ***complexidade de tempo de O(1)***.

Quanto ao espaço, como a matriz já existe na memória e a função está apenas atualizando os valores na matriz, a função não usa memória adicional, independentemente do tamanho da matriz. Isso significa que a função também tem uma ***complexidade espacial de O(1)***.

<script src="https://gist.github.com/toniesteves/672c16ba2961f365193541b816c3b8d3.js"></script>

No exemplo acima, o valor em cada índice da matriz está ficando dobrado. Ou seja, há um aumento ***linear*** de iterações no loop à medida que o comprimento da matriz aumenta, diz-se que esse código tem uma complexidade de tempo de execução de O(n). Além disso, agora estamos usando memória adicional e a quantidade de memória aumenta ***linearmente*** à medida que o comprimento da matriz aumenta. Isso significa que a função tem uma complexidade de espaço de ***O(n)***.

Dado esses dois exemplos, fica claro que o primeiro trecho de código, com uma complexidade de tempo de ***O(1)*** será executado mais rapidamente em quase todos os casos.

> *É possível encontrar uma entrada específica em que uma função O(n) performe mais rapidamente do que a função O(1) ?*

Claro, mas geralmente à medida que a complexidade de uma função aumenta, o tempo de execução do pior cenário também aumenta.

### A função de crescimento

Uma pergunta que você pode estar se fazendo nesse momento é: Como é possível categorizar um algoritmo complexo usando uma notação tão simples como a Big O.

> A resposta é: "Utilizando a função de crescimento".

Todo algoritmo é codificado para resolver problemas. Em uma situação hipotética, digamos que exista um algoritmo utilizado para classificar uma matriz. quando fazemos análise de eficiência, precisamos saber quão bem esse algoritmo pode executar e para isso, é necessário conhecer 2 fatores:

* O tamanho do problema: ou seja, o tamanho da matriz aqui.
* O processo principal que influenciará o tempo do resultado: Aqui estão incluídos o número de comparações que precisaríamos fazer. Quanto mais comparações, mais lento será o algoritmo.

Ou seja, a eficiência do algoritmo pode ser definida em termos do ***tamanho do problema*** e da ***etapa de processamento***. Uma função de crescimento mostra o relacionamento entre os dois fatores, trazendo à tona a complexidade de tempo e/ou espaço em relação ao tamanho do problema.

Antes de iniciar a próxima seção, vamos assumir uma função de crescimento aleatoria, para um algoritmo qualquer de classificação seja expresso pela seguinte notação:

\begin{align}
  f(n) = 2n^2 + 4n + 6
\end{align}

### A complexidade assintótica

Não podemos medir todas as complexidades algoritimcas simplesmente relacionando tempo e espaço arbitráriamente, portanto, em vez de conhecer a eficiência exata, precisamos conhecer a **"complexidade assintótica"** definida pelo **termo dominante** de uma função de crescimento.

> Em complexidade assintótica, um termo dominante é um termo que aumenta mais rapidamente à medida que o tamanho do problema aumenta.

Segundo [Cormen](https://www.amazon.com.br/Introduction-Algorithms-Thomas-H-Cormen/dp/0262033844), Quando observamos tamanhos de entrada grandes o suficiente para tornar relevante apenas a ordem de crescimento do tempo de execução, estamos estudando a eficiência assintótica dos algoritmos. Ou seja, estamos preocupados com a forma como o tempo de execução de um algoritmo aumenta com o tamanho da entrada no limite, à medida que o tamanho da entrada aumenta sem limite. Normalmente, um algoritmo assintoticamente mais eficiente será a melhor opção para todas as entradas, exceto as muito pequenas.

Para a função de crescimento que definimos anteriormente, vamos desenhar uma tabela:

| **$$n$$** | **$$2n^2$$** | **$$4n$$** | **$$6$$** | **$$f(n)$$** |
|:-------:|:----------:|:--------:|:-------:|:----------:|
|    1    |      2     |     4    |    6    |     12     |
|    10   |     200    |    40    |    6    |     246    |
|    15   |     450    |    60    |    6    |     516    |
|    20   |     800    |    80    |    6    |     886    |
|    25   |    1250    |    100   |    6    |    1356    |
|    30   |    1800    |    120   |    6    |    1926    |
|    35   |    2450    |    140   |    6    |    2596    |
|    40   |    3200    |    160   |    6    |    3366    |


Considerando nossa função aleatória $$f(n)=2n²+4n+6$$, é possível observar que, à medida que n cresce, o termo $$2n^2$$ domina o resultado da função, que é $$f(n)$$. Portanto, neste caso, $$2n^2$$ é o nosso termo dominante.

Apenas um disclaimer antes de prosseguir: dizer que um termo é dominante à medida que $$n$$ cresce, não significa que ele seja maior que os outros termos para todos os valores de $$n$$. Você pode ver quando $$n = 1$$, os termos $$4n$$ e a constante $$(6)$$ são maiores que $$2n^n$$. Baseando-se nas considerações acima, podemos, de forma grosseira, definir a seguinte equação:

> Big O = Complexidade Assintótica = Termo Dominante

Podemos dizer que a complexidade assintótica da fórmula acima é $$O(2n^2)$$ ?

De fato, sim, mas a complexidade assintótica também é chamada de **"ordem do algoritmo"**. A palavra **"ordem"** aqui significa **"aproximadamente"** e todas as funções de uma mesma ordem são equivalentes. Além disso, como só temos interesse no **termo dominante**, isso significa que podemos ignorar outros termos e constantes. Então a constante $$2$$ aqui pode ser eliminada, a função acima, por exemplo, pertence à ordem $$n^2$$. Então, finalmente conseguimos ver algo familiar: $$O(n^2)$$

Depois de entender alguns dos conceitos que sustentam a notação Big O, vamos ver alguns exemplos e regras que você pode aplicar ao analisar as notações.