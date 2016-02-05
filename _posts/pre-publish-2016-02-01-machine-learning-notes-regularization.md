---
layout: post
title: "Machine Learning Notes Regularization"
description: "Notes for Andrew Ng's machine learning course on Coursera. Intended for people who have watched linear regression and logistic regression"
category: machine-learning
tags: []
---
{% include JB/setup %}


In Andrew's course we were introduced a few features of regularization term: 

* Regularization prevents overfitting
* Too much regularization underfits data
* Regularization helps to ignore noisy feature

This post gives some examples to each of the above, and will hopefully give you better intuition 

> Note, if you have not watched Andrew Ng's video, the following might be hard to understand

###Recap (Skip if you are familiar with notations in Andrew's class)
Take logistic regression as an example, we try to find parameter vector $\theta$ for the hypothesis 

$$ h_{\theta}(x) = \frac{1}{1+e^{-\theta^{T}x}} $$

$$ predict \ y=1 \ if \ h_{\theta}(x) > 0.5, y=0 \ otherwise $$

where x is a vector of training example, y is the binary label for that example, postive (1), negative (0). 

Moreover, note that $ h_{\theta}(x) > 0.5 $ only if $ \theta^{T}x > 0 $. We can write our decision boundary as 

$$ predict \ y=1 \ if \ \theta^{T}x > 0, y=0 \ otherwise $$

The cost function over all training examples: 

$$ cost(x) = \frac{1}{m} \sum_{i}^{m} -y \ log (h_{\theta}(x^{(i)})) - (1-y^{(i)}) log (1- h_{\theta}(x^{(i)})) $$

Where the number of training example is $ m $. 

Our goal is to find the vector $\theta$ that minimizes the cost function. The basic idea is to make $ \theta^{T}x $ as large as possible when y=1, and as small as possible for y=0. 

Cost function changes slightly with regularization: 

$$ cost(x) = \frac{1}{m} \sum_{i}^{m} -y \ log (h_{\theta}(x)) - (1-y) log (1- h_{\theta}(x)) + \lambda \|\theta\|^{2}$$

Where $ \| \theta \| ^{2} $ is the length of the vector $ \theta \in \mathbb{R}^{n}$

$$ \|\theta\|^{2} = \sum_{j}^{n} (\theta_{j})^{2} $$

With regularization, we not only try to find $\theta$ that classify all points correctly, but also a short theta i.e. one that minimizes $ \|\theta\|^{2} $. The cost function now has a trade off between length of theta, and classification accuracy depending on the regularization factor $ \lambda $ 

Notations summarized here: 

| symbol | type | semantics  |
|:--------|:-----:|:----------|
|-----------------|------------|-----------------|
| $ y^{(i)} $ | scalar $ \in \lbrace0,1\rbrace $ |  label for training example i  |
| $ x^{(i)} $     | vector  $ \mathbb{R}^{n} $      | feature vector for tranining example i |
| $ \theta $ | vector $ \mathbb{R}^{n} $ | parameter vector for hypothesis |
| m | scalar | number of training examples|
| n | scalar | number of features |
| $ \lambda $ | scalar | regularization parameter to control how much regularization we want

###How does regularization reduce noise? 

The key is to realize that inner product between two vectors depend on their relative direction, AND their length. 

//TODO






The first graph has v1(0,1) v2(3,2) and  =2. 
The second graph has v1(1,1) v2(2,1)  = 5. The angle betwee two vectors is small, yielding larger inner product. 
The third graph will have a negative inner product, as the angle formed is greater than 90. 

Now let's look at an example. Green dots and red dots on either side of y-axis represent two classes of datapoints



In Graph 1 above, the black short line represents  we get, and the long green line is the decision boundary. The decision boundary and  are always orthogonal to each other.

In this graph, we see that the line is not the best choice, but classifies all points correctly. Further more, the cost function is minimized by making  *longer*. The inner product between red points and  is very large, and the inner product between  and green points is very small (negative), though it is not optimal .



With regularization, the learning algorithm focuses more on *direction* of , and tries to minimize its length while giving a lower cost. 

Here the inner product between theta and red points can still be pretty large, but not because  is long, but because  is in the optimal direction. 


###How does regularization prevent overfitting







 Without regularization we might choose a  that classifies all points correctly. But what if the two red points on the left and two green points on the right are outliers, and we really want to draw a line in the middle? 

With regularization, the function starts consider the trade off between classifying points correctly and the length of . 
We might be getting a line like graph 2 in which theta does not have a y-component. The cost of making  longer in the y direction is higher than classifying all points correctly. 

###How does regularization underfit

 
With a few more points added to Graph 1, we may no longer consider the points outliers. It is a good idea to draw a line that classifies all points correctly. 



But with heavy regularization, we may still end up getting a straight line in the middle. The cost of giving  extra length in y direction can outweigh the cost of classifying points correctly. 




###Generalization to Linear Regression: 


So far we have only seen the effect of regularization on logistic regression, but what about other models? Consider the following dataset


We can fit it perfectly and with a quadratic hypothesis by adding an extra  feature


Not a great example, but the line is indeed quadratic and curved slighly. 




But a linear hypothesis (with 0 being the parameter to the quadratic term) might be a fair choice. It fits nicely with all points except for one. 




Since regularization penalizes parameter based on its length, giving the quadratic term a non-zero value is going to cost more. Thus, a hypothesis with regularization might still prefer the linear one. 

