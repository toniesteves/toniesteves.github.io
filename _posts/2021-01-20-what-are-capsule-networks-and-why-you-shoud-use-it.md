---
layout: post
title: What are Capsule Networks and why you should use it.
description: A brief introduction to Capsule Networks, Dynamic Routing and why they stand out from conventional CNNs.
date: 2021-01-20 15:01:35 +0300
author: toni
image: '/images/posts/20210120/cover.png'
image_caption: 'Photo by [Oliver Sjöström](https://unsplash.com/photos/m-qps7eYZl4) on [Unsplash](https://unsplash.com/)'
tags: [CNN, deep learning, neural-networks]
featured: true
---

In the field of deep learning, convolutional networks have been incredibly successful and are the main reason why deep learning is so common now. Although the results presented by these networks are incredibly good, this type of structure still has some disadvantages in its basic architecture, making them not work very well for some more specific tasks.

CNN's detect features in images and learn to recognize objects with that information. Initial layers detect simple features like the edges of an image, and deeper layers can detect more complex features like eyes, nose, or an entire face in the case of a face. Then all these learned features are used to make a final prediction. Here are the flaws of this system — there is no **spatial** information that is used anywhere in a CNN and the clustering function that is used to connect layers is really very inefficient.

In the most common convolutional network architectures, it is common to insert pooling layers, which operate independently, at each depth level of a convolutional network. The function of the ***Pooling*** layer is to progressively reduce the spatial size of the input representation, and thus reduce the number of parameters and network processing and, in this way, also control ***overfitting***.

It turns out that in the pooling process, a lot of important information is lost because only the most active neurons are chosen to be moved to the next layer.

And what's the problem with this approach?

This operation is the reason why valuable spatial information is lost between layers and in the words of Geoffrey E. Hinton:

> "The pooling operation used in convolutional neural networks is a big mistake and the fact that it works so well is a disaster." — [Geoffrey E. Hinton](https://www.reddit.com/r/MachineLearning/comments/2lmo0l/ama_geoffrey_hinton/clyj4jv/)

Thus, conventional CNNs have some limitations:

* There is a considerable loss of precise spatial relationships in the pooling process, between higher-level features, such as a nose and a mouth, in the process performed by CNN's. Precise spatial relationships are necessary for identity recognition
* CNN's are good at detecting features, but less effective at exploring spatial relationships between features (perspective, size, orientation). That is, CNNs fail to reach the understanding of geometric relationships for radically new points of view.
* Vulnerability to GANS. Through the GANS we can only change a few critical pixels (or neurons), and the image would not change a human's perspective (the image would still look identical to the naked eye), but it could trick the CNN classifier into believing that the image is something completely different. Imagine how devastating it would be if an autonomous vehicle interpreted a stop sign as a speed limit sign?

*Any similarity is not purely coincidental, given that human vision ignores irrelevant details using a carefully determined sequence of fixation points to ensure that only a small fraction of the optical matrix is processed by our brain at the highest resolution.*

To address this problem, [Hinton](https://arxiv.org/pdf/1710.09829.pdf) proposed that we use a process, loosely called "routing-by-agreement". This means that features identified in a lower-level layer (nose, eyes, mouth) will only be sent to a higher-level layer that matches its content. Finally, if the grouped features resemble that of an eye or a mouth, it will arrive at a “face” or if it contains fingers and a palm, it will be sent to a “hand”.

This solution, which encodes spatial information in resources and at the same time uses a strategy called dynamic routing, was named ***Capsule Networks*** and was presented by Geoffrey Hinton at [NIPS 2017](https://nips.cc/Conferences/2017).

### Capsule Networks

Capsule Networks provide a way to detect parts of objects in an image and represent spatial relationships between those parts. This means that capsule networks, in free translation, are able to recognize the same object, such as a face, in a variety of different poses and with the typical number of features (eyes, nose, mouth), even if they haven't seen this one. pose on the training data. Capsule networks have an architecture composed of parent and child nodes that in turn build a complete image of an object.

![Capsule_Netorks]({{site.baseurl}}/images/posts/20210120/capsule-n.png){:loading="lazy"}

### What are "Capsules"?

Capsules are a small cluster of neurons where each neuron in that cluster represents various properties of a specific part of the image. Some examples of properties include: position and orientation in an image, width and texture, color, and so on.

![Capsules]({{site.baseurl}}/images/posts/20210120/capsules.gif){:loading="lazy"}

As shown in the image above, each capsule produces a vector, u, with a magnitude and orientation.

* ***Magnitude (m)*** = the probability that a part exists; a value between 0 and 1.
* ***Orientation (theta)*** = the state of the part properties.

These output vectors allow us to perform what we have already called "routing-by-agreement", to build a parse tree that recognizes whole objects as composed of many smaller parts.

***Do not worry! We will see shortly what "routing by agreement" means.***

The vector *magnitude* is a value between 0 and 1 that indicates the probability that a specific part of a whole exists and has been detected in an image. This is a normalized function of the weighted inputs for a specific capsule; a nonlinear function called [squashing](https://www.sciencedirect.com/topics/computer-science/squashing-function).

The *orientation* of the vector, in turn, represents the state of the properties of a part of that whole; this orientation will change if one of the properties changes.

---

*A capsule is a group of neurons whose activity vector represents the instantiation parameters of a specific type of entity, such as an object or a part of the object. We use the activity vector length to represent the probability that the entity exists and its orientation to represent the instantiation parameters.* [[3]](https://arxiv.org/abs/1710.09829)

*Magnitude* is a special property of each part that must remain very high even when an object is in a different orientation, as shown below.

Let's say a capsule detects a cat's face in an image and produces a vector with magnitude 0.9. This means that it detects a face with 90% confidence.

![Capsule_Netorks_Cat]({{site.baseurl}}/images/posts/20210120/capsule-n-cat.png){:loading="lazy"}


If we consider for a different image of this cat's face, one in which the cat is in profile for example, the orientation of the output vector of this capsule will change. The properties, position, orientation, and shape of the face part have changed in this new image, and the orientation of the output vector changes with each of these property changes. These changes are changes in neural activities within a capsule. The magnitude of the vector should remain very close to 0.9, as the capsule must still be certain that the face exists in the image.


![Capsule_Netorks_Cat_upsidown]({{site.baseurl}}/images/posts/20210120/capsule-n-cat-upsidown.png){:loading="lazy"}


That is, when these relationships are tied to the internal representation of data, it becomes very easy for the model to understand that what it sees is just another view of something it has seen before.

Consider the following image. You can easily recognize that this is the Statue of Liberty, although all the images show the same image from different angles.

![Statue-of_Liberty]({{site.baseurl}}/images/posts/20210120/statue-liberty.jpeg){:loading="lazy"}

*Your brain can easily recognize that this is the same object, even though all the pictures are taken from different angles. **CNNs do not have this feature**.*

This is because the internal representation of the Statue of Liberty in your brain is not viewing angle dependent. You've probably never seen these exact pictures of him, but you can still immediately tell what it was.

> So why have a vector output instead of a single value? Why is guidance a useful value?


### Dynamic Routing

Dynamic routing is a process for finding the best connections between the output of one capsule and the inputs of the next layer of capsules. It allows capsules to communicate with each other and determine how data moves through them, based on real-time changes in network inputs and outputs. That is, no matter what kind of input image a capsule network sees, dynamic routing ensures that the output of a capsule is sent to the appropriate parent capsule in the next layer.


### Routing example: Maxpooling

You may have seen an example of a simple routing process in a convolutional neural network. The convolutional network extracts features from an image through a convolutional layer. In a typical CNN architecture, this filtered information is then passed to a maxpooling layer.

*If you would like to remember what the role of the **pooling layer** is, you can visit this other article [here](https://medium.com/toniesteves/agrupando-conceitos-e-classificando-imagens-com-deep-learning-5b2674f99539).*

The **maxpooling** layer creates a route that ignores all resources except the most "active" or high-value ones in the previous convolutional layer.

![max-pooling]({{site.baseurl}}/images/posts/20210120/max-pooling.png){:loading="lazy"}

This routing process performed by CNN's discards a lot of pixel information and produces very different results for images of the same object in different orientations.

*So how does dynamic routing work and how does it improve a simple routing process like maxpooling?*

When a capsule network is initialized, the child capsules are not sure where their outputs should go, as they act as the input to the next layer of parent capsules. In fact, each pod starts with a list of possible parents, which are all parent pods in the next layer. This possibility is represented by a value called the ***coupling coefficient***, $$C$$, which is the probability that the output of a given capsule will go to a ***parent capsule*** in the next layer. A child node with two possible parents will start with equal coupling coefficients for both $$(0.5)$$.

![coupling-coefficient]({{site.baseurl}}/images/posts/20210120/coupling-coefficient.png){:loading="lazy"}

### Routing by Agreement

Dynamic routing is an iterative process that updates these coupling coefficients. The upgrade process, performed during network training, is as follows for a single capsule:

* A child capsule represents a part of an entire object. Each child capsule produces some output vector $$u$$; its magnitude represents the existence of a part and its orientation represents the generalized posture of the part.

* For each possible parent, a child capsule computes a prediction vector, $$\hat u$$ , which is a function of its output vector, $$u$$, times a weight matrix, $$W$$. You can think of $$W$$ as a linear transformation — a translation or rotation , for example, which relates the pose of a part to the pose of a larger part or the whole object (e.g. if a nose is pointing to the left, it is likely that the entire face of which it is a part is also pointing to the left). So $$\hat u$$ is a prediction about the pose of a larger part, represented by a parent capsule.

* If the prediction vector has a large dot product with the output vector of the parent capsule, $$v$$, then these vectors are considered concordant and the coupling coefficient between that parent and the daughter capsule increases. Simultaneously, the coupling coefficient between that infant capsule and all other parents decreases.

* This dot product between the parent output vector, $$v$$, and a prediction vector, $$\hat u$$, is known as a capsule agreement measure.

This process is called "routing-by-agreement". If the orientation of the output vectors of the capsules in successive layers is aligned, they agree that they must be coupled and the connections between them are strengthened. The coupling coefficients are calculated by a [softmax](https://en.wikipedia.org/wiki/Softmax_function) function that operates on the agreements, a, between capsules and transforms them into probabilities such that the coefficients between a child and its possible parents add up to 1.


![routing-by-agreement]({{site.baseurl}}/images/posts/20210120/routing-by-agreement.gif){:loading="lazy"}

### Capsule Network architecture in MNIST

A Capsule Network can be divided into two main parts:

1. A convolutional encoder.
2. A fully connected linear decoder.

![mnist-capsule-network]({{site.baseurl}}/images/posts/20210120/mnist-capsule-network.png){:loading="lazy"}

### Encoder

The encoder takes the input image and learns how to represent it as a 16-dimensional vector that contains all the information needed to essentially render the image.

* **Convolutional Layer** — detects features that are later parsed by capsules. As proposed in the article, it contains 256 grains of size 9x9x1.

* **Primary (lower) capsule layer** — This layer is the lower level capsule layer I described earlier. It contains 32 different capsules and each capsule applies eighth $$9 \times 9 \times 256$$ convolutional kernels to the output of the previous convolutional layer and produces a 4D vector output.

* **Digit Capsule Layer (Top)** — This layer is the top-level capsule layer that the primary caps would target (using dynamic routing). This layer produces 16D vectors that contain all the instantiation parameters needed to reconstruct the object.


### Decoder

![decoder]({{site.baseurl}}/images/posts/20210120/decoder.png){:loading="lazy"}

The decoder takes the 16D vector from the Digit Capsule and learns how to decode the instantiation parameters given in an image of the object it is detecting.

The decoder is used with a [Euclidean distance](https://pt.wikipedia.org/wiki/Dist%C3%A2ncia_euclidiana) loss function to determine how similar the reconstructed feature is compared to the actual feature it is being trained on. This ensures that the pods only keep information that will benefit from recognizing digits within their vectors. The decoder is a really simple feed-forward neural network which is described below.

* **Layer 1**: Fully Connected (Dense)
* **Layer 2**: Fully connected (Dense)
* **Layer 3**: Fully Connected (Dense) — Final Result with 10 classes


### Conclusion

*Capsule Networks* introduce a new concept in the field of computer vision, which can be used in deep learning to better model hierarchical relationships within the representation of internal knowledge of a neural network. The intuition behind them is very simple and elegant.

Hinton and his team proposed a way to train this network composed of capsules and successfully trained it on a simple dataset.

However, there are still challenges. Current implementations are much slower than other modern deep learning models. Also, we need to see if they work well on more difficult datasets and across different domains.

I have got some repositories on Github that address the practical implementation of Capsules Network and I am making them available below with references to implementation of these networks:

* [Repository 1](https://github.com/XifengGuo/CapsNet-Keras) (Keras Implementation)
* [Repository 2](https://github.com/llSourcell/capsule_networks) (Tensorflow Implementation)
* [Repository 3](https://github.com/higgsfield/Capsule-Network-Tutorial) (Pytorch Implementation)

In addition, Geoffrey Hinton's own presentation on Capsule Networks is available on youtube.


  <!-- [![Geoffrey Hinton – Capsule Networks](https://img.youtube.com/vi/x5Vxk9twXlE/0.jpg)](https://www.youtube.com/watch?v=x5Vxk9twXlE) -->

<p><iframe src="https://www.youtube.com/embed/x5Vxk9twXlE" loading="lazy" frameborder="0" allowfullscreen></iframe></p>


Finally, I hope this material has been useful and makes sense to you, especially for beginners. In addition, in the references section you can find a very useful material used to prepare this article that can help you expand your knowledge on the subject.

Remembering that any feedback, whether positive or negative, just get in touch through my twitter, linkedin or in the comments below. Thanks :)

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






