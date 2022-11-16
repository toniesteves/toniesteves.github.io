---
layout: post
title: A brief introduction to Shadow Mode in Machine Learning.
description: A simple strategy to mitigate risks from models in production without impacting real users along the way.
date: 2021-07-13 15:01:35 +0300
author: toni
image: '/images/posts/20210713/cover.png'
image_caption: 'Photo by [Oliver Sjöström](https://unsplash.com/photos/m-qps7eYZl4) on [Unsplash](https://unsplash.com/)'
tags: [machine learning, data science]
featured: false
---

AI-driven organizations are using data and machine learning to solve their toughest problems and are reaping huge benefits. But ML systems have the ability to create technical debt if not managed properly. Operationalizing and managing the lifecycle of ML models, data, and experiments is where things get tricky. In fact,[ over 87% of data science projects never make it to production](https://www.forbes.com/sites/forbestechcouncil/2019/04/03/why-machine-learning-models-crash-and-burn-in-production/?sh=776c3ec52f43).

The strategies you adopt when putting a software project into production have the potential to save you from costly and high-impact business mistakes. When we talk about machine learning systems, these precautions are even more important. Usual activities of a data scientist such as data wrangling, feature engineering, or debugging of model bugs in production can be very challenging, especially when production data inputs are difficult to replicate exactly.

The "Shadow Mode", or in literal translation the Shadow Mode, is one of these deployment strategies and, in this article, I intend to briefly explain a little more about this approach, as well as its advantages, disadvantages and challenges.

### Why should you care about machine learning model deployments?

Imagine a data scientist who is working on a new fraud detection model that requires a dozen additional features that none of the bank's other credit risk models use, but this scientist is determined that the performance improvements guarantee inclusion. of these extra features.

The current performance of the model is relatively good, but due to some business changes it needs to have part of its code rewritten, thus starting the feature engineering steps in the production model. The features are all captured in the configuration and all tests pass — assuming you implement tests in your model code. The model is released to customers, where it starts to identify fraudulent users.

Somewhere between the data science team and the deployment, one of the features was created incorrectly. Maybe it was a configuration error, or maybe the pipeline code has an outlier, or it could just have been a miscommunication. Now, in another scenario for example, instead of using a key feature that distinguishes fraudsters from non-fraudsters, an older version of that feature that doesn't distinguish is used.

This leads the model to define that some customers of your business that should be identified as frauds are not being, which therefore means that fraudulent operations are taking place and your company is absorbing a financial loss (the specific data is not the crux of the matter, the issue is that there is a subtle error). But there is no immediate dramatic effect, so no alarms sound and a few months pass.

One day, three months later, someone discovers the bug. At this point, thousands of transactions have been carried out and users who should be considered potential fraudsters are not, leading the company to absorb considerable losses.

This is a story to illustrate a point. Many industries that use machine learning to make important predictions are at risk of similar disasters. Think machine learning models in healthcare, agriculture, shipping and logistics, legal services, the list is extensive. But this story could have a very different ending if alternative deployment strategies were in place, and these strategies are what we will consider in the next sections.

### What is Shadow Mode?

"Shadow Mode" or "Dark Launch" — [the latter a nomenclature adopted by Google](https://cloud.google.com/blog/products/gcp/cre-life-lessons-what-is-a-dark-launch-and-what-does-it-do-for-me) — is a technique where production traffic and data are run through a newly deployed version of a machine learning model or service, without the service or model actually returning the answer or prediction to clients and/or other systems. Instead, the old version of the service or model continues to serve answers or predictions, and the results of the new version are merely captured and stored for analysis.

That is, to launch a model in "Shadow Mode", you deploy the new model along with the old model, both active. The current model continues to handle all requests, but the shadow mode model also runs on some (or all) of the requests. This allows you to safely test the new model against real data, avoiding the risk of service interruptions.

Shadow mode should not be confused with [flagging/toggling](https://martinfowler.com/articles/feature-toggles.html) feature. Feature flags can be used to turn shadow mode on or off, but they are a separate type of technique and do not provide the central concurrent testing element for shadow mode. Broadly speaking, there are two main reasons to use shadow mode:

* **First**: Ensure your model is handling inputs properly.

* **Second**: Ensure your service can handle the incoming load.

*Feature Toggles (often called Feature Flags) is a powerful technique that allows teams to modify system behavior without changing code. We can keep this complexity in check by using smart toggle implementation practices and appropriate tools to manage our toggle configuration, but we should also aim to limit the number of switches in our system.*

### When using Shadow Mode.

Shadow mode is a great way to test out some scenarios:

* **Engineering**: With a model in shadow mode, you can test that the pipeline is working and that the model is getting the inputs you expect and returning the results in the correct format. You can also check things like latency received by the model.

* **Outputs**: It is possible to validate that the model's response data has the expected distribution (eg, your model is not reporting just a single value for all inputs).

* **Performance**: It is possible to validate that the model in shadow mode is producing results comparable to or better than the model currently in production.

*Another point worth noting is that shadow mode works well when the model output doesn't need a user action to validate it. We can cite models where you try to influence the user, for example a recommendation model where success means more sales converted. Models that require explicit user validation for validation are best tested using an[ A/B test](https://en.wikipedia.org/wiki/A/B_testing).*

> The main difference between an A/B test and shadow mode is that in an A/B test the traffic is split between the two models, while in shadow mode both models operate on the same events.

### How does deploying in shadow mode work.
There are two general methods that are used to deploy a model in shadow mode. Both are API-relative for the active model: in front of the API or behind the API.

### In front of the API
To put a model in shadow mode in front of the API, you must host two API endpoints: one for the active model and one for the shadow mode. The caller makes a call to the two models and may even disregard the shadow model's response, but must record it so that the results can be compared.

![shadow-model-in-front]({{site.baseurl}}/images/posts/20210713/shadow-model-in-front.png){:loading="lazy"}

This way of deployment is suitable for situations where the team consuming the model's predictions is averse to change or has very strict requirements for how the shadow model should work, because it gives them control.

#### The advantages of this method are:

* **The caller has control.** They decide when to change the shadow model to live. They can instantly roll back if problems occur. They might even stop the experiment if it's harming your system.

* **The call may be different.** If the shadow model requires different input (perhaps a new ID associated with the user), your API might differ from the production model.

#### The main disadvantages are:

* **The change is closer to the customer.** The calling code is usually closer to the business area, so any bugs introduced during shadow model integration are likely to have more impact.

* **Tighter coordination is needed.** The team that owns the model and the team that calls it will have to make changes to their code: the model team to generate an endpoint and the calling team to add the call to the second model, as well as a logging action of the predictions made .


### Behind the API

To put a model in shadow mode behind the API, you change the code that responds to requests from that API by calling both the active model and the shadow model. The results of both models must be logged, but the result returned must be that of the active model only.

![shadow-model-behind]({{site.baseurl}}/images/posts/20210713/shadow-model-behind.png){:loading="lazy"}

This method is ideal when you want to move quickly (and break things) because you can change the shadow model without having to coordinate with the calling team. To the outside world, the API remains (in theory) unchanged and therefore hides the tests being done behind it.

#### The advantages of this method are:

* **The model host has control.** You can change the shadow model, turn it on, off and switch to a new one whenever you want. You can register exactly what you are interested in registering.

* **Little coordination with other teams is required.** To the outside world; no one else needs to change your code.

#### The main disadvantages are:

* **The model in shadow mode must be compatible with the active model.** Since the outside world is not changing what passes to the API, your shadow model is restricted to the same inputs as the production model (although you can choose to use just a subset or get additional inputs through some other method).

* **You still need to change the calling code.** Eventually, when the shadow mode model is ready to replace the production model, you will need to change the API version and change the calling code to use this new version. That means there's a little extra work to do once you're satisfied with the test results.


### The main challenges are:

* High cost due to additional features required to support new model version in shadow mode with current active model.

* Increased complexity to configure deployment strategies such as in-place upgrade.

* Performance tests must be carefully designed and deployed.

* Performance tests can include system performance (load testing, latency, and so on) and model performance (model metrics comparison).

### What "Shadow Mode" definitely is not.

* **An A/B test.** As previously mentioned in an A/B test, traffic is split between the two models, while in shadow mode both models operate on the same events.

* **A complementary model.** The results returned by the model in shadow mode should not be combined with the results of the model currently in production. If the idea is to build an ensemble, this ensemble will compose a new model by itself, which in turn will be the model present in shadow mode.

* **A model that makes business decisions directly.** The shadow mode model will not present results to the business area, nor will it expose customers to a new version of its service. Your results will be recorded and evaluated by the responsible team, but with the aim of optimizing and providing insights into the model that is currently in production.

### Conclusion

Deploy models in shadow mode is an easy way to test your model on live data. It is flexible and allows you to empower the right team to control the experiment. Also, the sooner you put your model into production to evaluate real-world data, the better feedback you get on that model's behavior.

Finally, I hope this material has been useful and makes sense to you, especially for beginners. In addition, in the references section you can find very useful material used to prepare this article that can help you expand your knowledge on the subject.

Remembering that any feedback, whether positive or negative, just get in touch through my twitter, linkedin or in the comments below. Thanks :)

### Referencies

* [1] [Machine Learning Design Patterns](https://www.oreilly.com/library/view/machine-learning-design/9781098115777/)

* [2] [Software Engineering for Machine Learning: A Case Study](https://www.microsoft.com/en-us/research/uploads/prod/2019/03/amershi-icse-2019_Software_Engineering_for_Machine_Learning.pdf)

* [3] [The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf)

* [4] [MLOps: Continuous delivery and automation pipelines in machine learning](https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning)

* [6] [Deploy shadow ML models in Amazon SageMaker](https://aws.amazon.com/blogs/machine-learning/deploy-shadow-ml-models-in-amazon-sagemaker/)

* [7] [Questions Answered — Shadow Deployment In Machine Learning](https://medium.com/mlops-community/questions-answered-shadow-deployment-in-machine-learning-5ee5a8854e10)