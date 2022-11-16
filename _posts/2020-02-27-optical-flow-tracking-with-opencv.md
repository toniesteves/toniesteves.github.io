---
layout: post
title: Rastreamento de Fluxo Óptico com OpenCV
description: Em visão computacional, o método Lucas Kanade é um método diferencial amplamente usado para estimativa de fluxo óptico, desenvolvido por Bruce D. Lucas e Takeo Kanade. O método assume que o fluxo é essencialmente constante em uma vizinhança local do pixel em questão e resolve as equações básicas de fluxo óptico para todos os pixels dessa vizinhança, pelo critério dos mínimos quadrados.
date: 2020-02-27 15:01:35 +0300
author: toni
image: '/images/posts/20200227/cover.png'
image_caption: 'Photo by [Oliver Sjöström](https://unsplash.com/photos/m-qps7eYZl4) on [Unsplash](https://unsplash.com/)'
tags: [opencv, computer vision, python]
featured:
---


A técnica conhecida como Fluxo Óptico permite que determinados pontos de um frame de vídeo sejam localizados em um frame anterior. Isto é o passo inicial por exemplo, para se determinar o deslocamento de um veículo entre dois quadros consecutivos do vídeo.

De forma mais objetiva, o fluxo óptico é o padrão de movimento aparente dos objetos de imagem entre dois quadros consecutivos causados pelo movimento do objeto ou da câmera. É um campo vetorial 2D, em que cada vetor é um vetor de deslocamento, mostrando o movimento dos pontos do primeiro quadro para o segundo.

![Optical Flow]({{site.baseurl}}/images/posts/20200227/optical-flow.jpeg){:loading="lazy"}

A imagem acima apresenta uma bola em movimento, se deslocando ao longo de 5 frames. A seta mostra seu vetor de deslocamento ao longo desses frames. O fluxo óptico tem muitas aplicações em áreas como Estrutura do Movimento, Compressão de vídeo, Estabilização de vídeo e etc.

O algoritmo Gunnar-Farneback foi desenvolvido para produzir resultados da técnica de fluxo óptico denso (ou seja, em uma grade densa de pontos). O primeiro passo é aproximar cada vizinhança de ambos os quadros por polinômios quadráticos. Posteriormente, considerando esses polinômios quadráticos, um novo sinal é construído por um deslocamento global. Finalmente, esse deslocamento global é calculado pela equação dos coeficientes nos rendimentos dos polinômios quadráticos. Você pode conferir [aqui](https://www.youtube.com/watch?v=a-v5_8VGV0A&t=61m30s) uma palestra em vídeo, que explica perfeitamente como o algoritmo Farneback funciona.


### Let's Code!

Primeiramente vamos importar o OpenCV e o numpy

{% highlight python %}
import cv2
import numpy as np
{% endhighlight %}


Precisamos então definir a fonte do video. Vamos definir então uma variável que recebe um [`cv2.VideoCapture()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-videocapture), ou simplesmente passe o valor 0 como parâmetro para obter informações da sua webcam. Em seguida, vamos ler o primeiro quadro utilizando utilizando a função [`read()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-read), além de definir o esquema de cores para a escala de cinza utilizando o método [`cv2.cvtColor()`](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html#cvtcolor).


{% highlight python %}
cap = cv2.VideoCapture(0)
_, frame = cap.read()
old_gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
{% endhighlight %}

Bom, inicialmente precisamos definir uma função de callback que retorne as cordenadas de um ponto especifico, mapeando o espaço vetorial ao seu redor e assim iniciarmos o fluxo óptico. Dessa forma, podemos linkar essa função ao clique do mouse, assim nossa função ficaria da seguinte forma.

{% highlight python %}
def select_point(event, x, y, flags, params):
  global point, point_selected, old_points
  if event == cv2.EVENT_LBUTTONDOWN:
    point = (x, y)
    point_selected = True
    old_points = np.array([[x, y]], dtype=np.float32)

{% endhighlight %}

Observe que precisamos tornar esse ponto uma variável global, dada a necessidade de manipular esse ponto mais adiante no código

Vamos iniciar nossa janela principal e informar a ela que existe um callback associado a um evento do mouse.

{% highlight python %}
cv2.namedWindow("Frame")
cv2.setMouseCallback("Frame", select_point)
{% endhighlight %}

*Observe que é através do nome especifcado na função [`namedWindow()`](https://docs.opencv.org/2.4/modules/highgui/doc/user_interface.html?highlight=namedwindow) que referenciaremos a janela principal em todos os locais do código.*

Além disso, vamos adicionar uma flag de controle que servirá para iniciar o fluxo óptico a partir do clique e detecção do ponto.

{% highlight python %}
point_selected = False
point = ()
old_points = np.array([[]])
{% endhighlight %}

Vamos configurar o fluxo de vídeo. Para isso vamos ler a entrada de vídeo quadro a quadro utilizando loop. Como queremos processar o vídeo inteiro, leremos os quadros até que [`isOpened()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-isopened) retorne verdadeiro. De forma similar ao primeiro quadro, converteremos cada quadro em escala de cinza.


{% highlight python %}
while cap.isOpened():
  _, frame = cap.read()
  curr_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
{% endhighlight %}

Além disso, vamos atualizar nossa flag de controle para que seja demarcado na tela o local do clique inicial.

{% highlight python %}
if point_selected is True:
  cv2.circle(frame, point, 5, (0, 0, 255), 2)
{% endhighlight %}

A seguir, o fluxo óptico será calculado usando [`calcOpticalFlowPyrLK()`](https://docs.opencv.org/2.4/modules/video/doc/motion_analysis_and_object_tracking.html), que faz referência a Pyramid Lucas Kanade. Essa função recebe como parametros dois quadros consecultivos, que definiremos través de prev_gray e gray_frame. Depois disso, será necessário definir outros parâmetros de otimização obrigatórios ao algorítmo, que passaremos através de um dicionário. Logo em seguida ao detectarmos os novos pontos, vamos atualizar seus valores dentro do loop de forma a atualizar o fluxo óptico

{% highlight python %}
if point_selected is True:
           ...
new_points, status, error = cv2.calcOpticalFlowPyrLK(prev_gray, gray_frame, old_points, None, **lk_params)
prev_gray = gray_frame.copy()
old_points = new_points
{% endhighlight %}

Nosso dicionário de parâmetros ficou da seguinte forma:

{% highlight python %}
lk_params = dict(winSize = (15, 15), maxLevel = 5, criteria = (cv2.TERM_CRITERIA_EPS | cv2.TERM_CRITERIA_COUNT, 10, 0.03))
{% endhighlight %}


*No dicionário dos parâmetros obrigatorios do algorítmo, é possível manipular aleatoriamente e escolher parâmetros diferentes e ver como isso mudará os resultados.*

**winsize**: Mapeia uma área de 15x15 pixels ao redor do ponto definido.*

**maxlevel**: Definimos o nível do algoritmo, pois quanto menor a janela, menos pixeis e melhor a detecção do fluxo óptico [(Pyramid Analysis)](https://en.wikipedia.org/wiki/Pyramid_(image_processing)).*

**criteria**: Parâmetro que específica os critérios de finalização e iterações do algoritmo de pesquisa iterativa (mais iterações, mais exaustiva é a pesquisa pelos pontos).*

Por fim, fazemos a limpeza.

{% highlight python %}
cap.release()
cv2.destroyAllWindows()
{% endhighlight %}

### Resultado

![Optical Flow Gif](https://miro.medium.com/max/640/1*nD1OGLH9ZwA_pfz4nVEV_A.gif){:loading="lazy"}

O código está disponível no Github [aqui](https://github.com/toniesteves/optical-flow)

### Explicação

Antes de detectar o objeto, o campo vetorial ao redor do ponto definido é mapeado, e isso acontece a cada novo frame em que o objeto se desloca no vídeo. O método Lucas Kanade assume que o deslocamento do conteúdo da imagem entre dois instantes próximos (frames ou quadros) é pequeno e aproximadamente constante tomando em consideração os pixeis vizinhos a um ponto *p*.

Assim, o fluxo óptico, em uma definição de mais alto nível, é o movimento de objetos entre quadros consecutivos de sequência, causados pelo movimento relativo entre o objeto e a câmera. Podemos expressar o fluxo óptico da seguinte forma:

![Optical Flow]({{site.baseurl}}/images/posts/20200227/optical_flow_math.png){:loading="lazy"}

Entre quadros consecutivos, podemos expressar a intensidade da imagem
$$(I)$$ em função do ponto $$(x,y)$$ e do tempo $$(t)$$. Assim se extraírmos a primeira imagem $$I(x,y,t)$$ e movermos seus pixeis representados por $$(dx, dy)$$ sob o tempo $$(t)$$, obteremos uma nova imagem $$I(x+dx, y+dy)$$.

Vamos assumir inicialmente que as intensidades de pixel de um objeto são constantes entre quadros consecutivos.

\begin{align}
  I(x, y, t) = I(x + \delta x, y + \delta y, t + \delta t)
\end{align}

### Conclusão
Espero que esse material tenha sido útil e faça sentido pra você, principalmente aos iniciantes. De forma elementar apresentamos conceitos relacionados a fluxos ópticos. Além disso nas referências do artigo é possível encontrar um material muito útil utilizado para elaboração desse artigo que pode te ajudar a ampliar seus conhecimentos no tema.

Lembrando que qualquer feedback , seja positivo ou negativo é só entrar em contato através do meu [twitter](https://twitter.com/toni_esteves) ou [linkedin](https://www.linkedin.com/in/toniesteves/) ou os comentários aqui em baixo :)

### References

* [1] [A Review On Particle Image Velocimetry And Optical Flow Methods In Riverine Environment.](https://www.researchgate.net/publication/320908264_A_Review_On_Particle_Image_Velocimetry_And_Optical_Flow_Methods_In_Riverine_Environment)

* [2] [Optical Flow](https://docs.opencv.org/3.4/d4/dee/tutorial_optical_flow.html)

* [3] [Wikipedia Optical Flow](https://en.wikipedia.org/wiki/Optical_flow)

* [4] [Implementing Lucas-Kanade Optical Flow algorithm in Python](https://sandipanweb.wordpress.com/2018/02/25/implementing-lucas-kanade-optical-flow-algorithm-in-python/)