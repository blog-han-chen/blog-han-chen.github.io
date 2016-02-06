---
layout: post
title: "What Does Machine Learning Mean to me"
description: ""
category: 
tags: []
---
{% include JB/setup %}
{:center: style="text-align: center"}

 It's Math
 ---------
 

I always imagined machine learning task as writing sophisticated programs that somehow has "smart" into it. But it really boils down to building a math model based on the data that we have. 

For simple tasks such as linear regression (yes, machine learning doesn't have to be complicated) you might already know the math behind it. 

For complicated tasks such as recoginizing hand-written digits from an image, it is also a math model. We can represent a 30x30 image as a 30x30 = 900 dimension vector, with each element of the vector being the pixel value. The label of that image (digit 0,1,2 .. 9) can be represented by a 10 dimensional binary vector 

$$ y = 
        \begin{bmatrix}
        0 \\
        1 \\
        0 \\
        \vdots \\
        0 \\
        \end{bmatrix}
$$

where an $1$ in the $n$-th dimension represent digit $n$. 
{:center}

We can come up with a function with some parameter(s) to compute $y$ from the 900D input vector, and another function to compute the error of classification. The parameter that minimizes the error is the best classifies images. 

The process of minizing the error is the training step in machine learning. And just like how you would minimize any other function, you take the derivative, look for parameters that would set it to zero. Most of the time you won't solve for parameters directly, some algorithms such as gradient descent (kind of similar to Newton's Method, correct me if not) can be used. 



 Its'optimization
 ---------
 

Having the correct model is not enough, we also need it to run fast on a computer. Some basic ideas are 

* map reduce: divide a task such as $\sum_{i}^{100}x^{i}$ to smaller tasks such as $sum1 = \sum_{i}^{50}x^{i} \ , sum2=\sum_{i=51}^{100}x^{i}$ then send them to different computers to compute, then stitch back the result $ sum = sum1 + sum2 $

* stochastic gradient descent (just a fancy name): instead of trying to find the parameter to a model that fits all the data at once, find one that fits one data point at a time. Then iterate through all the data we have. 

Example, finding a line that separates two classes of points: 
<div style="text-align: center" >
{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.1.1-sgd.png"
            title="2.1.1-sgd"
            caption="A line that fits 2 points" 
            display="inline" 
            size="small" %}

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.1.2-sgd.png"
            title="2.1.2-sgd"
            caption="A line that fits 3 points" 
            display="inline" 
            size="small" %}

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.1.3-sgd.png"
            title="2.1.3-sgd"
            caption="A line that fits 4 points" 
            display="inline" 
            size="small" %}

...

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.1.4-sgd.png"
            title="2.1.4-sgd"
            caption="Final line" 
            display="inline" 
            size="small" %}
</div>
* mini-batch gradient descent: instead of one point, iterate on a group of points. 
<div style="text-align: center" >

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.2.1-bgd.png"
            caption="A line that fits a few points" 
            display="inline" 
            size="small" %}

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.2.2-bgd.png"
            caption="More points added" 
            display="inline" 
            size="small" %}

...

{% include image.html
            img="image/what-does-machine-learning-mean-to-me/2.2.3-bgd.png"
            caption="Final line" 
            display="inline" 
            size="small" %}
</div>


It's domain knowledge. 
---------

For the previous two parts someone might already have done work for you. Since you are the one applying machine learning technique to _your_ data, this part can't be skipped. 

One of the reasons that we choose neural network to compute vision or speech related tasks is that we know the human brain is exceptionally good at that. It might be an overkill to use it for linear regression. 

As another example, suppose we need to classify spam emails, we use the number of times "money" appears in the body as a feature. But emails from banks might contain the word "money" too and they might not be spams. It does not matter which model we choose in this case, we need better feature. 


