---
layout: post
title: 'GBMs, Step Functions, and Deep Learning'
comments: true
---

Deep Learning is awesome, I'm a huge fan. I took a class in the Spring of 2016 just on [Deep Learning](https://github.com/franciscojavierarceo/ECBME6040) and got to dive into the really awesome work people are doing in computer vision, NLP, and data mining.

I've recently been trying to test DL on more general problems and have found that DL algorithms (e.g., MLPs) don't necessarily do better than other algorithms all the time, which is not shocking. In fact, I've heard passively in class that a proof exists showing that for any given algorithm, you can always construct a dataset the model will fail to learn. In the DL literature we call data like this [adversarial examples](https://arxiv.org/pdf/1412.6572.pdf). So I started to look a little more into this problem and I thought it would be fun to make a quick [simulation](https://github.com/franciscojavierarceo/Python/blob/master/Step%20Functions.ipynb) and blog post about my findings.  

As a lot of people in the data science/data mining area might know, Gradient Boosting Machine is a pretty popular algorithm that can approximate a variety of different functions through a series of decision trees. One thing that's great about decision trees is that they can learn very jagged functions, (e.g., 2-dimensional step-functions). So I simulated some data with a fairly silly output function that looks like this

<center><img src="/assets/images/stepfunction_true.png">
</center>

This is a pretty ugly function where at the critical points of the input, the output simply shifts by a constant value. 

Now we can estimate this function using Sklearn's [Gradient Boosting Machine](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingClassifier.html) in Python prettty trivially, then we can look at what the model thinks the test set should be given the input data.

{:.center}
![Gradient Boosting Machine's Estimation of the Function](/assets/images/stepfunctiongbm.gif)

We see that the GBM does a great job! In fact, we can calculate the RMSE here and find that it's almost 0. How does a Neural Network do? I used an MLP with 10 hidden layers with 200 neurons/units in each layer and ReLU activation units. Here's what the NN learned:

{:.center}
![MLP's Estimation of a Step-Function](/assets/images/stepfunctionmlp.gif)

Interesting right? The NN learns a smooth function and seems to struggle with the boundary points of the threshold function. I should note that I tried experimenting with a variety of different parameters but most provided a result similar to this one (e.g., using a maxout layer, using dropout, increasing the number of numbers, hidden layers, various activation functions). 

Here we find that the specification for this NN wasn't able to learn this threshold function but we were able to learn it trivially using GBM (at least with respect to the computation time of both algorithms). This most certaintly has to do with the activation functions and the fact that the NN tries to learn a smooth decision boundary near the critical points.

Lastly, I thought I'd juxtapose the two residual plots (plots of the errors) as function of the two features because it further emphasizes the point that the MLP is learning an overly smooth function.

{:.center}
{:height="100px" width="100px"}
![Look at them there residuals](/assets/images/stepfunction_res.png)

Neural Networks and Deep Learning are awesome and have a huge amount of potential in the future and have had a ton of success in the present, I think it'd be interesting to see what modifications to neural networks are necessary to make learning step functions of this nature easier for them to learn.

Anyways, thanks for reading!
-Francisco
