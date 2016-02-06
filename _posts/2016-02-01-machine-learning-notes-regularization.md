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

Logistic Regresssion Recap (Skip if you are familiar with notations in Andrew's class)
------
Take logistic regression as an example, we try to find parameter vector $\theta$ for the hypothesis 

$$ h_{\theta}(x) = \frac{1}{1+e^{-\theta^{T}x}} $$

$$ predict \ y=1 \ if \ h_{\theta}(x) > 0.5, y=0 \ otherwise $$

where x is a vector of training example, y is the binary label for that example, postive (1), negative (0). 

Moreover, note that $ h_{\theta}(x) > 0.5 $ only if $ \theta^{T}x > 0 $ [(why? See a picture)](https://www.google.com/search?q=sigmoid+function&client=ubuntu&espv=2&biw=1242&bih=599&tbm=isch&tbo=u&source=univ&sa=X&ved=0ahUKEwiK8MHg5-PKAhUD0WMKHWHkCz8QsAQINg&dpr=1.1#imgrc=YSEtQfp1mCwvOM%3A). We can write our decision boundary as 

$$ predict \ y=1 \ if \ \theta^{T}x > 0, y=0 \ otherwise $$

The cost function over all training examples: 

$$ cost(x) = \frac{1}{m} \sum_{i}^{m} -y \ log (h_{\theta}(x^{(i)})) - (1-y^{(i)}) log (1- h_{\theta}(x^{(i)})) $$

Where the number of training example is $ m $. 

Our goal is to find the vector $\theta$ that minimizes the cost function. Skipping over the math, the basic idea for minimization is to make $ \theta^{T}x $ as large as possible when y=1, and as small as possible for y=0. 

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

How does regularization reduce noise? 
------

The key is to realize that inner product between two vectors depend on their relative direction, AND their length $\left\Vert v_{1}\right\Vert$. 

{% include image.html
            img="image/regularization/innerproduct.gif"
            title="inner product"
            caption="Inner product, aka dot product, depends on relative angle and length of the two vectors" 
 %}

If two vectors have a large relative angle, say 89 deg, their inner product can still be made large by making the two vectors _longer_ despite that the angle is not in favor of that. Vice versa, if two vectors are short, their inner product can be made large by a small angle, say 5 deg.


Now let's look at an example. Green dots and red dots on either side of y-axis represent two classes of datapoints, our task is to draw a line that separates the two, (logistic regression).


{% include image.html
            img="image/regularization/reduce-noise-1.png"
            title="Reduce Noice 1"
            caption="In Graph 1 above, the black short line represents parameter $\theta$ we get, and the long green line is the decision boundary." 
 %}

In a situation where there are some noice in the y-component, we see that the line is not the best choice, some points are really close to the line. But it classifies all points correctly. 

Further more, recall that we want to minimize the cost function is minimized by making the inner product between one class and $\theta $ very large, and the very small (negative). The learning algorithm does so by making $\theta $ *longer*, even though it does not point to the optimal direction.

{% include image.html
            img="image/regularization/reduce-noise-2.png"
            title="Reduce Noise 2"
            caption="Not the best example, but now the line is further away from data points and $\theta$ points to the correct direction more." 
 %}

With regularization, the learning algorithm focuses more on *direction* of $\theta $, and tries to minimize its length while giving a lower cost. 

Here the inner product between theta and red points can still be pretty large, but not because $\theta $ is long, but because $\theta$ is in the optimal direction. 


How does regularization prevent overfitting
------

{% include image.html
            img="image/regularization/prevent-overfit-1.png"
            title="Prevent Overfit 1"
            caption="Say the two red points on upper left and green dots on lower right are outliers" 
 %}

Without regularization we might choose a $\theta$ that classifies all points correctly. But what if the two red points on the left and two green points on the right are outliers, and we really want to draw a line in the middle? 

{% include image.html
            img="image/regularization/prevent-overfit-2.png"
            title="Prevent Overfit 2"
            caption="The line is now drawn in the middle" 
 %}

With regularization, the function starts consider the trade off between classifying points correctly and the length of $\theta$. Since $ cost(x) = \frac{1}{m} \sum_{i}^{m} -y \ log (h_{\theta}(x)) - (1-y) log (1- h_{\theta}(x)) + \lambda \|\theta\|^{2}$ increases with $\lambda \|\theta\|^{2}$, the longer $\theta$ is, the larger $\lambda \|\theta\|^{2}$ will be. 

We might be getting a line like graph 2 in which theta does not have a y-component. Given enough normal data (green dots on the left, red dots on the right), the cost of making $\theta$ longer in the y direction is higher than classifying all points correctly. 

How does regularization underfit
------

 
With a few more points added to Graph 1, we may no longer consider the points outliers. It is a good idea to draw a line that classifies all points correctly. 

{% include image.html
            img="image/regularization/underfit-1.png"
            title="Underfit 1"
            caption="We expect the line to be slanted to the left, since all data are now considered normal and we'd like to classify them correctly" 
 %}

But with heavy regularization by increasing $\lambda$ in the cost function, we may still end up getting a straight line in the middle. The cost of giving  extra length in y direction can outweigh the cost of classifying points correctly. 

{% include image.html
            img="image/regularization/underfit-2.png"
            title="Underfit 2"
            caption="The learning algorithm refuses to give $\theta$ extra length in the y direction, thus the line is straight along y-axis." 
 %}


Generalization to Linear Regression: 
------

So far we have only seen the effect of regularization on logistic regression, but what about other models? Consider the following dataset which we can fit it perfectly and with a quadratic hypothesis by adding an extra quadratic feature




{% include image.html
            img="image/regularization/linear-reg-1.png"
            title="Linear Regression with a quadratic feature"
            caption="With a quadratic feature added, we have a curve that fits all data correctly, instead of a line. Not a great example, but the line is indeed quadratic and curved slighly. " 
 %}


But a linear hypothesis (with 0 being the parameter to the quadratic term) might be a fair choice. It fits nicely with all points except for one. 

{% include image.html
            img="image/regularization/linear-reg-2.png"
            title="Linear Regression with a quadratic feature"
            caption="" 
 %}


Since regularization penalizes parameter based on its length, giving the quadratic term a non-zero value is going to cost more. Thus, a hypothesis with regularization might still prefer the linear one. 

