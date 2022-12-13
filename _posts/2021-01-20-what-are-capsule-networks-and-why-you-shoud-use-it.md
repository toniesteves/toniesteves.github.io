---
layout: post
title: O que são Capsule Networks e porquê utilizar.
description: Uma introdução as Capsule Networks, Roteamento Dinâmico e porquê elas merecem destaque diante das convencionais CNNs.
date: 2021-01-20 15:01:35 +0300
author: toni
image: '/images/posts/20210120/cover.png'
image_caption: 'Photo by [Oliver Sjöström](https://unsplash.com/photos/m-qps7eYZl4) on [Unsplash](https://unsplash.com/)'
tags: [CNN, deep learning, neural-networks]
featured: true
---

No campo do aprendizado profundo, as redes convolucionais têm sido incrivelmente bem-sucedidas e são o principal motivo pelo qual o aprendizado profundo é tão comum agora. Apesar dos resultados apresentados por essas redes serem incrivelmente bons, esse tipo de estrutura ainda possui algumas devantagens em sua arquitetura básica, fazendo com que não funcionem muito bem para algumas tarefas mais específicas.

CNN's detectam recursos em imagens e aprendem a reconhecer objetos com essas informações. As camadas iniciais detectam recursos simples como as bordas de uma imagem, e camadas mais profundas podem detectar recursos mais complexos, como olhos, nariz ou um rosto inteiro no caso de uma face. Em seguida, todos esses recursos aprendidos são utilizados para fazer uma previsão final. Aqui estão as falhas deste sistema — não existem informações espaciais que são usadas em qualquer lugar em uma CNN e a função de agrupamento que é usada para conectar camadas, é realmente muito ineficiente.

Nas arquiteturas de redes convolucionais mais comuns, é comum inserir camadas de Pooling, que operam de forma independente, em cada nível de profundidade de uma rede convolucional. A função da camada de Pooling é reduzir progressivamente o tamanho espacial da representação de entrada, e assim diminuir a quantidade de parâmetros e o processamento da rede e, dessa forma controlar também o overfitting.

Acontece que no processo de pooling, muitas informações importantes são perdidas porque apenas os neurônios mais ativos são escolhidos para serem movidos para a próxima camada.

E qual o poblema com essa abordagem?

Esta operação é a razão pela qual informações espaciais valiosas se perdem entre as camadas e nas palavras de **Geoffrey E. Hinton**:

> "The pooling operation used in convolutional neural networks is a big mistake and the fact that it works so well is a disaster." — [Geoffrey E. Hinton](https://www.reddit.com/r/MachineLearning/comments/2lmo0l/ama_geoffrey_hinton/clyj4jv/)

Assim as CNN's convencionais apresentam algumas limitações:

* Existe uma perda consideravel de relações espaciais precisas no processo de pooling, entre as caracteristicas de nível superior, como um nariz e uma boca, no processo efetuado pelas CNN's. Relações espaciais precisas são necessárias para o reconhecimento de identidade

* As CNN's são boas em detectar recursos, mas menos eficazes em explorar as relações espaciais entre recursos (perspectiva, tamanho, orientação). Ou seja, CNN’s não conseguem alcançar o entendimento de relações geométricas para pontos de vista radicalmente novos.

* Vulnerabilidade a GANS. Através das GANS podemos alterar apenas alguns pixels (ou neurônios) críticos, e a imagem não mudaria a perspectiva de um humano (a imagem ainda pareceria idêntica a olho nu), mas poderia enganar o classificador da CNN para acreditar que a imagem é algo completamente diferente. Imagine como seria devastador se um veículo autônomo interpretasse um sinal de pare como um sinal de limite de velocidade?


*Qualquer semelhança não é mera coincidência, dado que a visão humana ignora detalhes irrelevantes usando uma sequência cuidadosamente determinada de pontos de fixação para garantir que apenas uma pequena fração da matriz óptica seja processada pelo nosso cérebro na resolução mais alta.*

Para resolver esse problema, [Hinton](https://arxiv.org/pdf/1710.09829.pdf) propôs que usássemos um processo, denominado em livre tradução, de “roteamento por acordo”. Isso significa que os recursos identificados em uma camada de nível inferior (nariz, olhos, boca) serão enviados apenas para uma camada de nível superior que corresponda ao seu conteúdo. Por fim, se as características agrupadas se assemelham a de um olho ou de uma boca, chegará a um “rosto” ou se contiver dedos e uma palma, será enviado a “mão”.

Esta solução que codifica informações espaciais em recursos e que utiliza, ao mesmo tempo, uma estratégia chamada de roteamento dinâmico, recebeu o nome de ***Capsule Networks***, e foi apresentada por Geoffrey Hinton, no [NIPS 2017](https://nips.cc/Conferences/2017).


### Capsule Networks

Capsule Networks fornecem uma maneira de detectar partes de objetos em uma imagem e representar relacionamentos espaciais entre essas partes. Isso significa que as redes cápsula, em livre tradução, são capazes de reconhecer o mesmo objeto, como um rosto, em uma variedade de poses diferentes e com o número típico de características (olhos, nariz, boca), mesmo que não tenham visto essa pose nos dados de treinamento. As capsule networks possuem uma arquitetura composta de nós pais e filhos que por sua vez constroem uma imagem completa de um objeto.

![Capsule_Netorks]({{site.baseurl}}/images/posts/20210120/capsule-n.png){:loading="lazy"}

### E o que são "Capsulas"?

Cápsulas são um pequeno agrupamento de neurônios em que cada neurônio desse agrupamento representa várias propriedades de uma parte específica da imagem. Alguns exemplos de propriedades incluem: posição e orientação em uma imagem, largura e textura, cor e etc.

![Capsules]({{site.baseurl}}/images/posts/20210120/capsules.gif){:loading="lazy"}

Como mostrado no imagem acima, cada cápsula produz um vetor, u, com uma magnitude e orientação.

* ***Magnitude (m)***  = a probabilidade de que uma parte exista; um valor entre 0 e 1.
* ***Orientation (theta)*** = o estado das propriedades da peça.

Esses vetores de saída nos permitem executar o que já denominamos de roteamento por acordo, para construir uma árvore de análise que reconhece objetos inteiros como compostos de várias partes menores.


***Don't worry! Veremos logo a frente o que significa "roteamento por acordo".***

A *magnitude* do vetor é um valor entre 0 e 1 que indica a probabilidade de que uma parte especifica de um todo existe e foi detectada em uma imagem. Esta é uma função normalizada das entradas ponderadas para uma cápsula específica; uma função não linear chamada de [squashing](https://www.sciencedirect.com/topics/computer-science/squashing-function).

A *orientação* do vetor por sua vez, representa o estado das propriedades de uma parte desse todo; esta orientação mudará se uma das propriedades mudar.

---

*Uma cápsula é um grupo de neurônios cujo vetor de atividade representa os parâmetros de instanciação de um tipo específico de entidade, como um objeto ou uma parte do objeto. Usamos o comprimento do vetor de atividade para representar a probabilidade de que a entidade existe e sua orientação para representar os parâmetros de instanciação.* [[3]](https://arxiv.org/abs/1710.09829)

A *magnitude* é uma propriedade especial de cada parte que deve permanecer muito alta mesmo quando um objeto está em uma orientação diferente, como mostrado abaixo.

Digamos que uma cápsula detecte a face de um gato em uma imagem e produza um vetor com magnitude de 0,9. Isso significa que ele detecta uma face com 90% de confiança.

![Capsule_Netorks_Cat]({{site.baseurl}}/images/posts/20210120/capsule-n-cat.png){:loading="lazy"}

Se considerarmos para uma imagem diferente da face desse gato, uma na qual o gato encontra-se de perfil por exemplo, a orientação do vetor de saída desta cápsula mudará. As propriedades, posição, orientação e forma da parte da face foram alteradas nesta nova imagem, e a orientação do vetor de saída muda com cada uma dessas alterações de propriedade. Essas mudanças são mudanças nas atividades neurais dentro de uma cápsula. A magnitude do vetor deve permanecer muito próxima a 0.9, já que a cápsula ainda deve ter certeza de que o rosto existe na imagem.


![Capsule_Netorks_Cat_upsidown]({{site.baseurl}}/images/posts/20210120/capsule-n-cat-upsidown.png){:loading="lazy"}


Ou seja, quando esses relacionamentos são agrupados à representação interna de dados, torna-se muito fácil para o modelo entender que o que ele vê é apenas outra visão de algo que ele já viu anteriormente.

Considere a imagem abaixo por exemplo. Você pode reconhecer facilmente que esta é a Estátua da Liberdade, embora todas as imagens a mostrem de diferentes ângulos.

![Statue-of_Liberty]({{site.baseurl}}/images/posts/20210120/statue-liberty.jpeg){:loading="lazy"}

*Seu cérebro pode reconhecer facilmente que este é o mesmo objeto, embora todas as fotos sejam tiradas de ângulos diferentes. **CNNs não têm esse recurso**.*

Isso ocorre porque a representação interna da Estátua da Liberdade em seu cérebro não depende do ângulo de visão. Você provavelmente nunca viu essas fotos exatas dele, mas ainda assim consegue perceber imediatamente o que era.

> Então, por que ter uma saída vetorial em vez de um único valor? Por que a orientação é um valor útil?

O fato de a saída de uma cápsula ser um vetor, com alguma orientação, torna possível usar um poderoso mecanismo de roteamento dinâmico para garantir que a saída de uma cápsula seja enviada para a cápsula-mãe apropriada na próxima camada de cápsulas.

### Roteamento Dinâmico

O roteamento dinâmico é um processo para encontrar as melhores conexões entre a saída de uma cápsula e as entradas da próxima camada de cápsulas. Ele permite que as cápsulas se comuniquem umas com as outras e determinem como os dados se movem através delas, de acordo com as mudanças em tempo real nas entradas e saídas da rede. Ou seja, não importa que tipo de imagem de entrada uma rede cápsula vê, o roteamento dinâmico garante que a saída de uma cápsula seja enviada para a cápsula pai apropriada na camada seguinte.


### Exemplo de roteamento: Maxpooling

Você já deve ter visto um exemplo de processo de roteamento simples em uma rede neural convolucional. A rede convolucional extrai features de uma imagem por meio de uma camada convolucional. Em uma arquitetura CNN típica, essas informações filtradas são então passadas para uma camada de maxpooling.

* Se você quer relembrar qual a função da camada de **pooling**, pode visitar esse outro artigo [aqui](https://medium.com/toniesteves/agrupando-conceitos-e-classificando-imagens-com-deep-learning-5b2674f99539).*

A camada **maxpooling** cria uma rota que ignora todos os recursos, exceto os àqueles mais “ativos” ou de alto valor na camada convolucional anterior.

![max-pooling]({{site.baseurl}}/images/posts/20210120/max-pooling.png){:loading="lazy"}

Esse processo de roteamento efetuado pelas CNN's, descarta muitas informações de pixel e produz resultados muito diferentes para imagens do mesmo objeto em orientações diferentes.

*Então, como funciona o roteamento dinâmico e como ele melhora um processo de roteamento simples como o maxpooling?*

Quando uma rede cápsula é inicializada, as cápsulas filhas não têm certeza de para onde suas saídas devem ir, pois atuam como entrada para a próxima camada de cápsulas pais. Na verdade, cada cápsula começa com uma lista de possíveis pais, que são todas as cápsulas pais na próxima camada. Essa possibilidade é representada por um valor chamado **coeficiente de acoplamento**, $$C$$, que é a probabilidade de que a saída de uma determinada cápsula vá para uma **cápsula-mãe** na próxima camada. Um nó filho com dois pais possíveis começará com coeficientes de acoplamento iguais para ambos $$(0.5)$$.

![coupling-coefficient]({{site.baseurl}}/images/posts/20210120/coupling-coefficient.png){:loading="lazy"}

Os coeficientes de acoplamento em todos os pais possíveis podem ser representados como uma distribuição de probabilidade discreta. Por fim, em todas as conexões entre uma cápsula filha e todas as cápsulas pai possíveis, os coeficientes de acoplamento devem somar 1.

### Roteamento por Acordo

O roteamento dinâmico é um processo iterativo que atualiza esses coeficientes de acoplamento. O processo de atualização, realizado durante o treinamento da rede, é o seguinte para uma única cápsula:

* Uma cápsula filha representa uma parte de um objeto inteiro. Cada cápsula filha produz algum vetor de saída u; sua magnitude representa a existência de uma parte e sua orientação representa a postura generalizada da parte.

* Para cada pai possível, uma cápsula filha calcula um vetor de previsão,  $$\hat u$$, que é uma função de seu vetor de saída, $$u$$ vezes uma matriz de peso, $$W$$. Você pode pensar em $$W$$ como uma transformação linear — uma translação ou rotação , por exemplo, que relaciona a pose de uma parte com a pose de uma parte maior ou do objeto inteiro (por exemplo, se um nariz está apontando para a esquerda, é provável que todo o rosto do qual faz parte também esteja apontando para a esquerda). Então $$\hat u$$ é uma previsão sobre a pose de uma parte maior, representada por uma cápsula pai.

* Se o vetor de predição tem um produto escalar grande com o vetor de saída da cápsula pai, v, então esses vetores são considerados concordantes e o coeficiente de acoplamento entre aquele pai e a cápsula filha aumenta. Simultaneamente, o coeficiente de acoplamento entre aquela cápsula infantil e todos os outros pais diminui.

* Este produto escalar entre o vetor de saída, $$v$$, e um vetor de previsão, $$\hat u$$, é conhecido como uma medida de "concordância da cápsulas".

Esse processo é chamado de roteamento por acordo. Se a orientação dos vetores de saída das cápsulas em camadas sucessivas estiver alinhada, eles concordam que devem ser acoplados e as conexões entre eles são fortalecidas. Os coeficientes de acoplamento são calculados por uma função [softmax](https://en.wikipedia.org/wiki/Softmax_function) que opera sobre os acordos, a, entre cápsulas e os transforma em probabilidades tais que os coeficientes entre um filho e seus possíveis pais somam $$1$$.

![routing-by-agreement]({{site.baseurl}}/images/posts/20210120/routing-by-agreement.gif){:loading="lazy"}

### Arquitetura de uma Capsule Network no MNIST

Uma Rede Capsula pode ser dividida em duas partes principais:

1. Um codificador convolucional.
2. Um decodificador linear totalmente conectado.

![mnist-capsule-network]({{site.baseurl}}/images/posts/20210120/mnist-capsule-network.png){:loading="lazy"}

### Codificador

O codificador recebe a imagem de entrada e aprende como representá-la como um vetor de 16 dimensões que contém todas as informações necessárias para essencialmente renderizar a imagem.

* **Camada Convolucional** — detecta recursos que são posteriormente analisados pelas cápsulas. Conforme proposto no artigo, contém 256 grãos de tamanho $$9 \times 9 \times 1$$.

* **Camada da cápsula primária (inferior)** — Esta camada é a camada da cápsula de nível inferior que descrevi anteriormente. Contém 32 cápsulas diferentes e cada cápsula aplica oitavos kernels convolucionais $$9 \times 9 \times 256$$ à saída da camada convolucional anterior e produz uma saída vetorial de 4 dimensões.

* **Camada de cápsula de dígito (superior)** — Esta camada é a camada de cápsula de nível superior para a qual as cápsulas primárias direcionariam (usando roteamento dinâmico). Esta camada produz vetores de 16 dimensões que contêm todos os parâmetros de instanciação necessários para reconstruir o objeto.

### Decoder

![decoder]({{site.baseurl}}/images/posts/20210120/decoder.png){:loading="lazy"}

O decodificador pega o vetor 16D da Digit Capsule e aprende como decodificar os parâmetros de instanciação dados em uma imagem do objeto que está detectando.

O decodificador é usado com uma função de perda que utiliza a [distância euclidiana](https://pt.wikipedia.org/wiki/Dist%C3%A2ncia_euclidiana) para determinar o quão semelhante o recurso reconstruído é comparado ao recurso real do qual está sendo treinado. Isso garante que as cápsulas mantenham apenas informações que se beneficiarão no reconhecimento de dígitos dentro de seus vetores. O decodificador é uma rede neural de feed-forward realmente simples que é descrita abaixo.

* **Camada 1**: Totalmente Conectada (Densa)
* **Camada 2**: Totalmente conectada (Densa)
* **Camada 3**: Totalmente Conectada (Densa) — Resultado Final com 10 classes


### Conclusão

*Capsule Networks* introduzem um novo conceito no campo da visão computacional, que pode ser utilizado no aprendizado profundo para melhor modelar relacionamentos hierárquicos dentro da representação de conhecimento interno de uma rede neural. A intuição por trás deles é muito simples e elegante.

Hinton e sua equipe propuseram uma forma de treinar essa rede composta de cápsulas e a treinaram com sucesso em um conjunto de dados simples.

No entanto, ainda existem desafios. As implementações atuais são muito mais lentas do que outros modelos modernos de aprendizado profundo. Além disso, precisamos ver se eles funcionam bem em conjuntos de dados mais difíceis e em diferentes domínios.

Selecionei alguns repositórios no Github que abordam a implementação prática das Capsules Network e estou deixando disponiveis abaixo com referências de implementação dessas redes:

* [Repository 1](https://github.com/XifengGuo/CapsNet-Keras) (Implementação em Keras)
* [Repository 2](https://github.com/llSourcell/capsule_networks) (Implementação em Tensorflow)
* [Repository 3](https://github.com/higgsfield/Capsule-Network-Tutorial) (Implementação em Pytorch)

Além disso tem disponível no youtube a apresentação do próprio Geoffrey Hinton sobre Capsule Networks.

  <!-- [![Geoffrey Hinton – Capsule Networks](https://img.youtube.com/vi/x5Vxk9twXlE/0.jpg)](https://www.youtube.com/watch?v=x5Vxk9twXlE) -->

<p><iframe src="https://www.youtube.com/embed/x5Vxk9twXlE" loading="lazy" frameborder="0" allowfullscreen></iframe></p>

Finalmente, espero que esse material tenha sido útil e faça sentido pra você, principalmente aos iniciantes. Além disso na seção de referências é possível encontrar um material muito útil utilizado para elaboração desse artigo que pode te ajudar a ampliar seus conhecimentos no tema.

Lembrando que qualquer feedback, seja positivo ou negativo é so entrar em contato através do meu [twitter](https://twitter.com/estevestoni), [LinkedIn](https://www.linkedin.com/in/toniesteves/ ), [Github](https://github.com/toniesteves) ou nos comentário aqui em baixo. Obrigado :)

### References

* [1] [CS231n Convolutional Neural Networks for Visual Recognition.](https://cs231n.github.io/convolutional-networks/)

* [2] [Capsule Networks – A survey](https://www.sciencedirect.com/science/article/pii/S1319157819309322)

* [3] [Dynamic Routing Between Capsules](https://arxiv.org/abs/1710.09829)

* [4] [A Comparative Study of Routing Methods in Capsule Networks](http://www.diva-portal.org/smash/record.jsf?pid=diva2%3A1314210&dswid=9385)

* [5] [Matrix Capsules with EM Routing](https://openreview.net/pdf?id=HJWLfGWRb)

* [6] [Understanding Hinton’s Capsule Networks. Part 2. How Capsules Work](https://pechyonkin.me/capsules-2/)

* [7] [Understanding Hinton’s Capsule Networks. Part 3. Dynamic Routing Between Capsules.](https://pechyonkin.me/capsules-3/)

* [8] [Does the Brain do Inverse Graphics? - Geoffrey Hinton, Alex Krizhevsky, Navdeep Jaitly, Tijmen Tieleman & Yichuan Tang](http://helper.ipam.ucla.edu/publications/gss2012/gss2012_10754.pdf)

* [9] [Understanding Dynamic Routing between Capsules (Capsule Networks)](https://jhui.github.io/2017/11/03/Dynamic-Routing-Between-Capsules/)

* [10] [Dynamic Routing Between Capsules - Yannic Kilcher](https://www.youtube.com/watch?v=nXGHJTtFYRU)






