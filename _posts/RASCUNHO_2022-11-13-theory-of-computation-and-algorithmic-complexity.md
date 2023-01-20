---
layout: post
title: Teoria da Computação e Máquinas de Turing
description: Big O notation is an important topic and its universal importance derives from the fact that it describes the efficiency of code written in any programming language.
date: 2020-06-15 15:01:35 +0300
author: toni
image: '/images/posts/20221113/cover.jpg'
image_caption: 'Photo by [Dan Cristian Pădureț](https://unsplash.com/photos/xJLN32FO7AY) on [Unsplash](https://unsplash.com/)'
tags: [complexity, theory of computation, optimization]
featured: true
---

### Máquinas de Turing 


  A Máquina de Turing é um <b>dispositivo teórico</b> conhecido como máquina universal, concebido pelo matemático britânico Alan Turing (1912-1954). Num sentido preciso, é um <b>modelo abstrato de um computador</b>, que se restringe apenas aos aspectos lógicos do seu funcionamento (memória, estados e transições), e não a sua implementação física. Turing provou que para qualquer sistema formal existe uma máquina de Turing que pode ser programada para imitá‐lo. Era este sistema formal genérico, com a habilidade de imitar qualquer outro sistema formal, o que Turing procurava essencialmente. Tais sistemas chamam‐se Máquinas de Turing Universais. O lógico matemático Alonzo Church chegou a definir: <i>"Qualquer processo aceito por nós homens como um algoritmo é precisamente o que uma máquina de Turing pode fazer"</i>.



  Em abril de 1936, Turing mostrou seus resultados para John Von Neumann em Princeton, quando os computadores, no sentido moderno, ainda não existiam. Turing criou os conceitos e a fundamentação matemática, que nove anos depois seria a tecnologia utilizada para materializar os primeiros computadores eletrônicos, com grande participação de Neumann, ou seja, a transformação da lógica de suas ideias abstratas em engenharia real. Durante este período, Turing retornou à Inglaterra e a ideia viveu apenas em sua mente. A correspondência entre instruções lógicas, a ação da mente humana e uma máquina, que poderia ser fisicamente construída, foi a contribuição definitiva de Alan Mathison Turing.


Sendo assim, uma máquina de Turing consiste em:

* Uma fita, dividida em células, uma adjacente à outra. Cada célula contém um símbolo de algum alfabeto finito. O alfabeto contém um símbolo especial branco (aqui escrito como ¬) e um ou mais símbolos adicionais. Assume-se que a fita é arbitrariamente extensível para a esquerda e para a direita, isto é, a máquina de Turing possui tanta fita quanto é necessário para a computação. Assume-se também que células que ainda não foram escritas estão preenchidas com o símbolo branco.

* Um cabeçote, que pode ler e escrever símbolos na fita e mover-se para a esquerda ou para a direita.
Um registrador de estados, que armazena o estado da máquina de Turing. O número de estados diferentes é sempre finito e há um estado especial denominado estado inicial com o qual o registrador de estado é inicializado.

* Uma tabela de ação (ou função de transição) que diz à máquina que símbolo escrever, como mover o cabeçote (que pode movimentar-se para esquerda e para a direita) e qual será seu novo estado, dados o símbolo que ele acabou de ler na fita e o estado em que se encontra. Se não houver entrada alguma na tabela para a combinação atual de símbolo e estado então a máquina para.


  Note que cada parte da máquina é finita e sua quantidade de fita potencialmente ilimitada dá uma quantidade ilimitada de espaço de memória.



### Comparação com máquinas reais


Frequentemente diz-se que as máquinas de Turing, ao contrário de autômatos mais simples, são tão poderosas quanto máquinas reais, e são capazes de executar qualquer operação que um programa real executa. O que está faltando neste enunciado é que praticamente qualquer programa particular executando em uma máquina particular e dada uma entrada finita é, na verdade, nada além de um autômato finito determinístico, já que a máquina em que executa pode estar apenas em uma quantidade finita de configurações. Máquinas de Turing poderiam de fato ser equivalentes a uma máquina que tenha uma quantidade ilimitada de espaço de armazenamento. Podemos questionar então por que as máquinas de Turing são modelos úteis de computadores reais. Há várias maneiras de responder a isto:


1. diferença está apenas na habilidade de uma máquina de Turing de manipular uma quantidade ilimitada de dados. No entanto, dada uma quantidade finita de tempo, uma máquina de Turing (como uma máquina real) pode apenas manipular uma quantidade finita de dados.

2. Como uma máquina de Turing, uma máquina real pode ter seu espaço de armazenamento aumentado conforme a necessidade, através da aquisição de mais discos ou outro meio de armazenamento. Se o suprimento destes for curto, a máquina de Turing pode se tornar menos útil como modelo. Mas o fato é que nem as máquinas de Turing, nem as máquinas reais precisam de quantidades astronômicas de espaço de armazenamento para fazer a maioria das computações que as pessoas normalmente querem que sejam feitas. Frequentemente o tempo de processamento requerido é o maior problema.

3. Máquinas reais são muito mais complexas que uma máquina de Turing. Por exemplo, uma máquina de Turing descrevendo um algoritmo pode ter algumas centenas de estados, enquanto o autômato finito determinístico equivalente em uma dada máquina real tem quadrilhões.

4. Máquinas de Turing descrevem algoritmos independentemente de quanta memória eles utilizam. Há um limite máximo na quantidade de memória que qualquer máquina que conhecemos tem, mas este limite pode crescer arbitrariamente no tempo. As máquinas de Turing nos permitem fazer enunciados sobre algoritmos que (teoricamente) valerão eternamente, independentemente dos avanços na arquitetura de computadores convencionais.

5. Máquinas de Turing simplificam o enunciado de algoritmos. Algoritmos executando em máquinas abstratas equivalentes a Turing são normalmente mais gerais que suas contrapartes executando em máquinas reais, porque elas têm tipos com precisão arbitrária disponíveis e nunca precisam tratar condições inesperadas (incluindo, mas não somente, acabar a memória).


Uma maneira em que máquinas de Turing são pobres modelos para programas é que muitos programas reais, tais como sistemas operacionais e processadores de texto, são escritos para receber entradas irrestritas através da execução, e, portanto, não param. Máquinas de Turing não modelam bem tal "computação contínua" (mas ainda podem modelar porções dela, tais como procedimentos individuais).



Outra limitação de máquinas de Turing é que elas não modelam a organização estrita de um problema específico. Por exemplo, computadores modernos são, na verdade, instâncias de uma forma mais específica de máquina de computação, conhecido como máquina de acesso aleatório. A principal diferença entre esta máquina e a máquina de Turing é que esta utiliza uma fita infinita, enquanto a máquina de acesso aleatório utiliza uma sequência indexada numericamente (tipicamente um campo inteiro). O resultado desta distinção é que há otimizações computacionais que podem ser executadas baseadas nos índices em memória, o que não é possível numa máquina de Turing geral. Assim, quando máquinas de Turing são utilizadas como base para tempo de execuções restritos, um "falso limite inferior" pode ser provado em determinados tempos de execução de algoritmos (graças à premissa falsa de simplificação da máquina de Turing). Um exemplo disto é uma "ordenação por contagem", o que aparentemente viola o limite inferior (n log n) em algoritmos de ordenação.


### Máquinas de Turing determinísticas e não-determinísticas


Se a tabela de ação tem no máximo uma entrada para cada combinação de símbolo e estado, então a máquina é uma máquina de Turing determinística (MTD). Se a tabela de ação contém múltiplas entradas para uma combinação de símbolo e estado, então a máquina é uma máquina de Turing não-determinística (MTND ou MTN).


### Máquina de Turing Não-Determinística (MNT)



Uma máquina de Turing não determinística é um tipo teórico de computador no qual comandos específicos podem permitir uma série de ações, em vez de um comando específico que leva a apenas uma ação permitida no modelo determinístico de computação.
Onde a programação determinística é uma condição simples de 'entrada $$X$$ leva à ação $$Y$$', uma configuração de máquina de Turing não determinística teoricamente permitiria que a entrada $$X$$ levasse a uma variedade de ações Y(array)
Máquinas de Turing não determinísticas podem realmente fornecer uma direção para o futuro da computação inteligente ou artificialmente inteligente. Ao desatar o trabalho computacional do paradigma determinístico, os computadores poderiam aprender a resolver problemas mais complicados e 'pensar' mais como humanos.
Um tipo de máquina de Turing não-determinística é a máquina de Turing probabilística. Aqui, o conjunto de ações ($$Y$$) mencionado acima é determinado por meio de alguma distribuição de probabilidade. Outra maneira de dizer isso é que quando a máquina tem mais de uma escolha, ela vai para um modelo probabilístico, analisa esse modelo e faz uma escolha de acordo.
Existem muitas outras maneiras de ordenar uma máquina de Turing não determinística, mas o princípio é que o computador precisa escolher entre um conjunto de opções disponíveis. Alguns modelos de Turing não determinísticos em uma configuração de aprendizado de máquina podem consistir no computador seguindo caminhos de lógica para um final aceito ou rejeitado e, em seguida, voltando e escolhendo uma ação de acordo.



### Diferença entre Algoritmos Determinísticos e Não Determinísticos

 Em um algoritmo determinístico, para uma determinada entrada em particular, o computador sempre produzirá a mesma saída passando pelos mesmos estados, mas no caso do algoritmo não determinístico , para a mesma entrada, o compilador pode produzir saídas diferentes em execuções diferentes. De fato, algoritmos não determinísticos não podem resolver o problema em tempo polinomial e não podem determinar qual é o próximo passo. Os algoritmos não determinísticos podem mostrar diferentes comportamentos para a mesma entrada em diferentes execuções e há um grau de aleatoriedade nisso.

| **Algoritmo Determinístico** | **Algoritmo não-determinístico** | 
|:----------------------------:|:--------------------------------:|
| Para uma determinada entrada, o computador fornecerá sempre a mesma saída. | Para uma determinada entrada, o computador fornecerá diferentes saídas em diferentes execuções.| 
| Pode resolver o problema em tempo polinomial. | Não é possível resolver o problema em tempo polinomial    |  
| Pode determinar o próximo passo de execução. | Não é possível determinar a próxima etapa de execução devido a mais de um caminho que o algoritmo pode seguir.    |  



### Referências

* [1] [A Review On Particle Image Velocimetry And Optical Flow Methods In Riverine Environment.](https://www.researchgate.net/publication/320908264_A_Review_On_Particle_Image_Velocimetry_And_Optical_Flow_Methods_In_Riverine_Environment)

* [2] [Optical Flow](https://docs.opencv.org/3.4/d4/dee/tutorial_optical_flow.html)

* [3] [Wikipedia Optical Flow](https://en.wikipedia.org/wiki/Optical_flow)

* [4] [Implementing Lucas-Kanade Optical Flow algorithm in Python](https://sandipanweb.wordpress.com/2018/02/25/implementing-lucas-kanade-optical-flow-algorithm-in-python/)
