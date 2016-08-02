---
layout: post
title: 'GBMs, Step Functions, and Deep Learning'
comments: true
---

Deep Learning is awesome, I'm a huge fan. I took a class in the Spring of 2016 just on [Deep Learning](https://github.com/franciscojavierarceo/ECBME6040) and got to dive into the really awesome work people are doing in computer vision, NLP, and data mining.

I've recently been trying to test DL on more general problems and have found that DL algorithms (e.g., MLPs) don't necessarily do better than other algorithms all the time, which is not shocking. In fact, I've heard passively in class that a proof exists showing that for any given algorithm, you can always construct a dataset it will fail to learn. In the DL literature we call data like this [adversarial examples](https://arxiv.org/pdf/1412.6572.pdf). 

So, one thing that's great about decision trees is that they can learn very jagged functions, (e.g., 2-dimensional step-functions). So I simulated some data with a fairly silly output function that looks like this:

![A look at the true function](/assets/images/stepfunction_true.png)

Here at the critical points of the input, the output simply shifts by a constant value. This is a pretty ugly function because it almost doesn't seem continuous, you may think it's discrete, but it's not. 

Now we can estimate this function using [GBM](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingClassifier.html) in Python prettty trivially, then we can look at what it thinks the test set should be given the input data.

![Gradient Boosting Machine's Estimation of the Function](/assets/images/stepfunction_gbm.png)

We see that the GBM does a great job! In fact, we can calculate the RMSE here and find that it's almost 0. How does a Neural Network do? I used an MLP with 10 hidden layers with 200 neurons/units in each layer and ReLU activation units. Here's what the NN learned

![Nueral Network's Estimation of the Function](/assets/images/stepfunction_nns.png)

Interesting right? The NN learns a smooth function and seems to struggle with the boundary points of the threshold function, I tried experimenting with a variety of different parameters but most provided a result similar to this one. 

Here we find that the specification for this NN wasn't able to learn this threshold function but we were able to learn it trivially with GBM (in terms of computation time of both algorithms). This most certaintly has to do with the activation functions and how information is passed in the forward stage of the NN, it's not able to learn to shut-off the units relating to these boundary points (or simply learn that they're an additive constant on the boundary of the two features).

Lastly, I thought I'd juxstapose the two residual plots (plots of the errors) as function of the two features because it further emphasizes the point the the MLP is learning an overly smooth of a function.

![Residuals from a GBM](/assets/images/stepfunction_gbm_res.png) 

![Residuals from an MLP](/assets/images/stepfunction_nns_res.png)

Neural Networks and Deep Learning are awesome and have a huge amount of potential in the future and have had a ton of success in the present, I think it'd be interesting to see what modifications to neural networks are necessary to make learning step functions of this nature easier for them to learn.

Anyways, thanks for reading!
-Francisco
