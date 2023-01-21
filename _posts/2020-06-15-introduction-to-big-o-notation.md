---
layout: post
title: Iniciando com a notação Big O.
description: A notação Big O é um tópico importante e sua importância universal origina-se do fato de descrever a eficiência do código escrito em qualquer linguagem de programação.
date: 2020-06-15 15:01:35 +0300
author: toni
image: '/images/posts/20200615/cover.jpg'
image_caption: 'Photo by [Dan Cristian Pădureț](https://unsplash.com/photos/xJLN32FO7AY) on [Unsplash](https://unsplash.com/)'
tags: [complexity, algorithms, data structures, big o notation, optimization]
featured: true
---

Big O notation is an important topic and its universal importance derives from the fact that it describes the efficiency of code written in any programming language. This topic has a lot of related subtopics, so this post will focus only in the basic concepts about how to recognize some nuances of ***Big O*** complexity, like ***O(n)***, ***O(2n)***, ***O(n²)***, ***O(log n)***, etc.

 <!-- Como esse é um tópico grande, este post abordará o básico sobre como reconhecer os diferentes tipos de complexidade do ***Big O***, como ***O(n)***, ***O(2n)***, ***O(n²)***, ***O(log n)***, etc. -->

### Space and time complexity
<!-- Complexidade de tempo e espaço -->

Big O notation can be used to describe the complexity of a section of code in terms of runtime and space. Thus, the Big O time complexity describes the worst-case execution time.

<!-- A notação Big O pode ser utilizada para descrever a complexidade de uma seção de código em termos de tempo de execução e espaço. Assim, a complexidade do tempo do Big O descreve o tempo de execução no pior cenário. -->

<!-- Já a complexidade do espaço Big O, descreve quanta memória é necessária para executar uma seção de código no pior cenário. Por exemplo, um loop for que copia uma matriz precisará de muito mais memória para ser executado do que um loop que simplesmente modifica uma matriz existente. -->

Big O space complexity describes how much memory is needed to run a section of code in the worst case scenario. For example, a for loop that copies an array will need much more memory to run than a for loop that simply modifies an existing array.


Let's consider two functions to see how Big O describes runtimes.

<script src="https://gist.github.com/toniesteves/84395d12fb43184d033e8567735571a5.js"></script>

<!-- A função acima acessa e atribui apenas um valor em um local, o tempo de execução será o mesmo, se o comprimento da matriz for 10 ou 10 milhões. Se o tempo de execução for constante, independentemente da entrada, diz-se que a função possui uma ***complexidade de tempo de O(1)***. -->

The above function accesses and assigns only one value in one place, the execution time will be the same whether the array length is 10 or 10 million. If the running time is constant, regardless of the input, the function is said to have a ***time complexity of O(1)***.

<!-- Quanto ao espaço, como a matriz já existe na memória e a função está apenas atualizando os valores na matriz, a função não usa memória adicional, independentemente do tamanho da matriz. Isso significa que a função também tem uma ***complexidade espacial de O(1)***. -->

As for space, as the array already exists in memory and the function is just updating the values in the array, the function doesn't use additional memory regardless of the array size. This means that the function also has a ***spatial complexity of O(1)***.

<script src="https://gist.github.com/toniesteves/672c16ba2961f365193541b816c3b8d3.js"></script>

<!-- No exemplo acima, o valor em cada índice da matriz está ficando dobrado. Ou seja, há um aumento ***linear*** de iterações no loop à medida que o comprimento da matriz aumenta, diz-se que esse código tem uma complexidade de tempo de execução de O(n). Além disso, agora estamos usando memória adicional e a quantidade de memória aumenta ***linearmente*** à medida que o comprimento da matriz aumenta. Isso significa que a função tem uma complexidade de espaço de ***O(n)***. -->

In **`double_and_copy_list_values`** function, the value at each index of the array is getting doubled. That is, there is a ***linear*** increase in iterations in the loop as the length of the array increases, this code is said to have a runtime complexity of O(n). Also, we are now using additional memory and the amount of memory increases ***linearly*** as the length of the array increases. This means that the function has a space complexity of ***O(n)***.

<!-- Dado esses dois exemplos, fica claro que o primeiro trecho de código, com uma complexidade de tempo de ***O(1)*** será executado mais rapidamente em quase todos os casos. -->

Given these two examples, it is clear that the first code snippet, with a time complexity of ***O(1)*** will run faster in almost all cases.

> *Is it possible to find a specific input where an O(n) function performs faster than the O(1) function?*

Sure, but generally as the complexity of a function increases, the worst-case execution time also increases.

### The growth function

A question you may be asking yourself right now is: How is it possible to categorize a complex algorithm using a notation as simple as the Big O.

> The answer is: "The growth function".

Every algorithm is coded to solve problems. In a hypothetical situation, let's say there is an algorithm used to sort a matrix, when we perform an efficiency analysis we need to know how well this algorithm can perform and for that we need to know 2 points:

* The size of the problem: i.e. the size of the array here.
* The main process that will influence the result time: This includes the number of comparisons we would need to make. The more comparisons, the slower the algorithm.

That is, the efficiency of the algorithm can be defined in terms of the ***size of the problem*** and the ***processing step***. A growth function shows the relationship between the two factors, bringing out the time and/or space complexity in relation to the size of the problem.

<!-- Ou seja, a eficiência do algoritmo pode ser definida em termos do ***tamanho do problema*** e da ***etapa de processamento***. Uma função de crescimento mostra o relacionamento entre os dois fatores, trazendo à tona a complexidade de tempo e/ou espaço em relação ao tamanho do problema. -->

<!-- Antes de iniciar a próxima seção, vamos assumir uma função de crescimento aleatoria, para um algoritmo qualquer de classificação seja expresso pela seguinte notação: -->
Before starting the next section, let's assume a random growth function, for any sorting algorithm to be expressed by the following notation:

\begin{align}
  f(n) = 2n^2 + 4n + 6
\end{align}

### The asymptotic complexity

<!-- Não podemos medir todas as complexidades algoritimcas simplesmente relacionando tempo e espaço arbitráriamente, portanto, em vez de conhecer a eficiência exata, precisamos conhecer a **"complexidade assintótica"** definida pelo **termo dominante** de uma função de crescimento. -->

We cannot measure all algorithmic complexities simply by relating time and space arbitrarily, so instead of knowing the exact efficiency, we need to know the **"asymptotic complexity"** defined by the **dominant term** of a growth function.

<!-- > Em complexidade assintótica, um termo dominante é um termo que aumenta mais rapidamente à medida que o tamanho do problema aumenta. -->

> In asymptotic complexity, a dominant term is a term that increases more rapidly as the size of the problem increases.

<!-- Segundo [Cormen](https://www.amazon.com.br/Introduction-Algorithms-Thomas-H-Cormen/dp/0262033844), Quando observamos tamanhos de entrada grandes o suficiente para tornar relevante apenas a ordem de crescimento do tempo de execução, estamos estudando a eficiência assintótica dos algoritmos. Ou seja, estamos preocupados com a forma como o tempo de execução de um algoritmo aumenta com o tamanho da entrada no limite, à medida que o tamanho da entrada aumenta sem limite. Normalmente, um algoritmo assintoticamente mais eficiente será a melhor opção para todas as entradas, exceto as muito pequenas. -->

According to [Cormen](https://www.amazon.com.br/Introduction-Algorithms-Thomas-H-Cormen/dp/0262033844), When we observe input sizes large enough to make only the order of growth of time relevant of execution, we are studying the asymptotic efficiency of the algorithms. That is, we are concerned with how the running time of an algorithm increases with the size of the input at the limit, as the size of the input increases without limit. Typically, an asymptotically more efficient algorithm will be the best choice for all but very small inputs.

<!-- Para a função de crescimento que definimos anteriormente, vamos desenhar uma tabela: -->

Let's consider the following table, for the growth function we defined earlier:

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


<!-- Considerando nossa função aleatória $$f(n)=2n²+4n+6$$, é possível observar que, à medida que n cresce, o termo $$2n^2$$ domina o resultado da função, que é $$f(n)$$. Portanto, neste caso, $$2n^2$$ é o nosso termo dominante. -->

If we take our random function $$f(n)=2n²+4n+6$$, it is possible to observe that, as n grows, the term $$2n^2$$ dominates the result of the function, which is $$f (n)$$. So in this case, $$2n^2$$ is our dominant term.

<!-- Apenas um disclaimer antes de prosseguir: dizer que um termo é dominante à medida que $$n$$ cresce, não significa que ele seja maior que os outros termos para todos os valores de $$n$$. Você pode ver quando $$n = 1$$, os termos $$4n$$ e a constante $$(6)$$ são maiores que $$2n^n$$. Baseando-se nas considerações acima, podemos, de forma grosseira, definir a seguinte equação: -->

Just a disclaimer before we moving on: saying that a term is dominant as $$n$$ grows does not mean that it is larger than the other terms for all values of $$n$$. You can see when $$n = 1$$, the terms $$4n$$ and the constant $$(6)$$ are greater than $$2n^n$$. Based on the above considerations, we can roughly define the following equation:

> Big O =  Asymptotic Complexity = Dominant Term

<!-- Podemos dizer que a complexidade assintótica da fórmula acima é $$O(2n^2)$$ ? -->

Can we say that the asymptotic complexity of the above formula is $$O(2n^2)$$ ?

<!-- De fato, sim, mas a complexidade assintótica também é chamada de **"ordem do algoritmo"**. A palavra **"ordem"** aqui significa **"aproximadamente"** e todas as funções de uma mesma ordem são equivalentes. Além disso, como só temos interesse no **termo dominante**, isso significa que podemos ignorar outros termos e constantes. Então a constante $$2$$ aqui pode ser eliminada, a função acima, por exemplo, pertence à ordem $$n^2$$. Então, finalmente conseguimos ver algo familiar: $$O(n^2)$$ -->

Indeed, yes, but asymptotic complexity is also called **"algorithm order"**. The word **"order"** here means **"approximately"** and all functions of the same order are equivalent. Also, since we are only interested in the **dominant term**, this means that we can ignore other terms and constants. So the constant $$2$$ here can be eliminated, the above function, for example, belongs to the order $$n^2$$. So we finally get to see something familiar: $$O(n^2)$$

<!-- Depois de entender alguns dos conceitos que sustentam a notação Big O, vamos ver alguns exemplos e regras que você pode aplicar ao analisar as notações. -->

After understanding some of the concepts that underpin the Big O notation, let's look at some examples and rules you can apply when analyzing the notations.

### Big O notation

As already stated, the Big O notation is a way of measuring the efficiency of algorithms based on time and space. To measure time complexity, the size of the input is compared to the time required for the algorithm to run. The best type of algorithm, for example, is one that always takes the same amount of time to run, regardless of the size of the input. To measure space complexity, it compares the size of the input to the amount of memory an algorithm uses. Each algorithm is assigned a time complexity, from worst to best case, with the overall time complexity being the expected case.

> Knowing the complexities of time allows us to create better algorithms.

Let's consider a for loop. You can run it on an array of 10 items and it will run quickly, but if the same iteration is run on an array of 10,000 items, the execution time will be much slower.

<script src="https://gist.github.com/toniesteves/cf3bac803eeda233aa726e2aca3d994d.js"></script>

Therefore, Big O annotation allows determining how long an algorithm will take to run. This allows us to understand how a piece of code will scale in addition to measuring algorithmic efficiency.

### $$O(1)$$

Consider the code snippet below:

<script src="https://gist.github.com/toniesteves/73e7964dc1c4395fcbf4c38411c9d1a6.js"></script>

This notation has a constant time. The time is consistent for each run.

*Example: Imagine that it takes you exactly 15 seconds to fill a cup of coffee and then that particular task is considered complete. It doesn't matter if you fill a cup, or a hundred cups, you fill each cup in a consistent period of time.*,

So, as per our example above, we don't care about **$$O(1)$$**, **$$O(2)$$**, etc. We round to **$$O(1)$$**, meaning our operation is a flat line in terms of scalability. It will take the same amount of time.

### $$O(n)$$

Now consider the following code snippet:

<script src="https://gist.github.com/toniesteves/78f18cc88c1c8c4b0f7ad9ddc52ad368.js"></script>

Our example loop is **$$O(n)$$** as it runs for all values in our input. Operations increase **linearly** with inputs over a single iteration for each item. The term (n) represents the number of entries. Thus, the algorithm runs in linear time.

### $$O(n^2)$$

Let's say we wanted to register a series of pairs from an array of items. We can do like this:

<script src="https://gist.github.com/toniesteves/acfb13cbb95415e25d4dc9b6ba653188.js"></script>

A general rule of thumb is that if you encounter nested loops, use multiplication to calculate the notation. So in our example above, we're doing $$O(n*n)$$ which becomes $$O(n^2)$$. This is known as **quadratic** time, which means that every time the number of elements increases, we increase the operations quadratically. Code that runs in O(n²) should be avoided, as the number of operations increases significantly as you introduce more elements. This notation is also known as **polynomial-time** notation.

*Polynomial time is a polynomial function of the input. A polynomial function looks like $$n^2$$ or $$n^3$$ and so on.*

[Bubblesort](https://pt.wikipedia.org/wiki/Bubble_sort#:~:text=O%20bubble%20sort%2C%20ou%20ordena%C3%A7%C3%A3o,o%20maior%20elemento%20da%20sequ%C3%AAncia.) is a good example of an $$O(n^2)$$ algorithm. The sort algorithm takes the first number and replaces it with the adjacent number if they are in the wrong order. It does this for each number, until all the numbers are in the correct order — and therefore sorted.

### $$O log(n)$$

A logarithmic algorithm halves a list every time it runs. Let's look at binary search. Given the ordered list below

<script src="https://gist.github.com/toniesteves/0a0eda6792feb7b8de52d2e374bd28c7.js"></script>

In general, the algorithm follows these steps:

* Go to the middle of the list.
* Check that this element is the answer.
* Otherwise, check if this element is more than the item we want to find.
* If it is, ignore the right side (all numbers above the midpoint) of the list and choose a new midpoint.
* Start again, locating the midpoint in the new list.

The algorithm halves the input every time it iterates. So it's logarithmic. Other examples of **logarithmic** algorithms include:

* [Fibonacci Sequence](https://www.geeksforgeeks.org/program-for-nth-fibonacci-number/)
* [Perform a search in binary search tree](https://en.wikipedia.org/wiki/Binary_search_tree)
* [AVL tree search](https://www.cs.auckland.ac.nz/software/AlgAnim/AVL.html)

### $$O(2^n)$$

The exponential time is $$2^n$$, where 2 depends on the permutations involved.

This algorithm is the slowest. So, let's say we have a password composed only of numbers (10 numbers, from 0 to 9). we want to crack a password that has a length of $$n$$. With a [brute force algorithm](https://en.wikipedia.org/wiki/Brute-force_search) on all combinations, we will have $$10^n$$ possible combinations.

An example of **exponential** time is finding all [subsets of a set](https://skerritt.blog/a-primer-on-set-theory/).

<script src="https://gist.github.com/toniesteves/2c134f64854df41826d1be0120f2ea1f.js"></script>

Exponential notation grows depending on the size of the input. Exponential algorithms are the most expensive, but like polynomial algorithms, you can use one trick or another. Let's say we have to calculate $$10^4$$. You would need to do the following:

\begin{align}
  10 ∗ 10 ∗ 10 ∗ 10 = 10^2 * 10^2
\end{align}

So we would have to calculate 10 squared twice!

*What if we store that value somewhere and use it later so we don't have to recalculate?*

This is the principle of dynamic programming. When faced with an exponential algorithm, [dynamic programming](https://skerritt.blog/dynamic-programming/) can often be used to speed it up.

### Classification

Now we know how to parse, we should know which is the fastest. We can sort them from fastest to slowest in the following order:


\begin{align}
  O(1) < O(log n) < O(\sqrt{n}) < O(n) < O(n log n) < O(n^2) < O(n^3) < O(2^n) < O(n!)
\end{align}

Here's our Big O notation chart, where the numbers are reduced so that we can see all the different lines and can understand the huge differences between these complexities.

![Big_O_notation]({{site.baseurl}}/images/posts/20200615/big_o_notation.png){:loading="lazy"}

Other common complexities

**Cubic $$O(n^3)$$**: — This complexity occurs when we run 3 nested loops, every n times.

**$$O(n!)$$ Factorial**: — When an algorithm calculates the entire permutation of a given matrix is $$O(n!)$$. An example would be solving the [traveling salesman](https://en.wikipedia.org/wiki/Travelling_salesman_problem) problem through brute force research.

**Linearithmic $$O(n log n)$$**: — When an algorithm performs logarithmic operations n times, we say that it has a linearithmic or $$O(n log n)$$ complexity. [Merge sort](https://en.wikipedia.org/wiki/Merge_sort) is a popular example of linearithmic complexity.

### Conclusion

Once you know what's really behind it. Now let's look at some more general rules that you can apply when analyzing.

* Remove the constants. As already stated at the beginning of this article, if we have an algorithm expressed in the form $$O(2n)$$, it is possible to remove the constant 2 and consider the complexity as $$O(n)$$.

* Remove non-dominant terms. $$O(n^2+n)$$ becomes $$O(n^2)$$. Keep only the dominant term in Big O notation.

* Assignment statements and if statements that are executed only once, regardless of the size of the problem, are $$O(1)$$.

* A simple for loop from 0 to n (no inner loops) is usually $$O(n)$$ (linear complexity);

* A nested loop of the same type (or delimited by the first parameter of the loop) gives $$O(n^2)$$ (quadratic complexity);

* A loop in which the control parameter is divided by two at each step (and which ends when it reaches 1), gives $$O(log n)$$ (logarithmic complexity);

* A "while" loop can vary depending on the actual number of iterations it will run.

* A loop with a non-O(1) execution inside simply multiplies the complexity of the loop body by the number of times the loop will be executed.

A note here is that, some code snippets may include initializations and some of them may be complex enough to take into account the efficiency of an algorithm.

That's it?

Yea! The hardest part is figuring out how complex our algorithm is initially. Simplifying is the easy part! Remember the golden rule of Big O notation:

> "What's the worst case scenario here?"

I left all the code used in this post available [here](https://www.kaggle.com/code/toniesteves/big-o-notation), in case you want to go deeper.

***

Finally, I hope this material has been useful and makes sense to you, especially for beginners. In addition, in the article references you can find a very useful material used to prepare this article that can help you expand your knowledge on the subject.

Remembering that any feedback, whether positive or negative, just get in touch through my [twitter](https://twitter.com/estevestoni), [linkedin](https://www.linkedin.com/in/toniesteves/) or in the comments below. Thanks :)

### References

* [1] [Introduction to Algorithms By Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, Clifford Stein]( https://books.google.com.br/books?id=aefUBQAAQBAJ&printsec=frontcover&hl=pt-BR&source=gbs_ge_summary_r&cad=0#v=onepage&q&f=false)

* [2] [Data Structures and Algorithms in Swift](https://books.google.com.br/books/about/Data_Structures_and_Algorithms_in_Swift.html?id=cxzZDwAAQBAJ&redir_esc=y)

* [3] [Comparação assintótica de funções](https://www.ime.usp.br/~pf/analise_de_algoritmos/aulas/Oh.html)

* [4] [Big O Notation and Algorithm Analysis with Python Examples](https://stackabuse.com/big-o-notation-and-algorithm-analysis-with-python-examples/)

* [5] [All You Need to Know About Big O Notation [Python Examples]](https://skerritt.blog/big-o/)

* [6] [An Introduction to Big O Notation](https://dev.to/lofiandcode/an-introduction-to-big-o-notation-210o)

* [7] [Big O Notation Part 2](https://programmingblah.com/Big-O-Notation-Part-2/)

* [8] [Know Thy Complexities](https://www.bigocheatsheet.com/)

* [9] [Cracking the Coding Interview](https://app.gitbook.com/s/-LhAOhtBZYfKTSfedwMZ/books/cracking-the-coding-interview)