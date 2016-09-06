---
layout: post
title: 'GBMs, Step Functions, and Deep Learning'
comments: true
---

Deep Learning is awesome, I'm a huge fan. I took a class in the Spring of 2016 just on [Deep Learning](https://github.com/franciscojavierarceo/ECBME6040) and got to dive into the really awesome work people are doing in computer vision, natural language processing (NLP), and data mining.

I've recently been trying to test DL on more general problems and have found that DL algorithms (e.g., MLPs) don't necessarily do better than other algorithms all the time, which is not shocking. In fact, I've heard passively in class that a proof exists showing that for any given algorithm, you can always construct a dataset the model will fail to learn; interestingly, data that fool learning algorithms are an active area of research. In the DL literature some folks call data like this [adversarial examples](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45471.pdf). So I started to look a little more into this problem and I thought it would be fun to make a quick [simulation](https://github.com/franciscojavierarceo/Python/blob/master/Step%20Functions.ipynb) and write about my findings.

## Gradient Boosted Decision Trees

As a lot of people in the data science/data mining area might know, [Gradient Boosting Machine](https://statweb.stanford.edu/~jhf/ftp/trebst.pdf) is a pretty popular algorithm that can approximate a variety of different functions through a series of decision trees. One thing that's great about decision trees is that they can learn very jagged functions, (e.g., 2-dimensional step-functions). So I simulated some data with a fairly silly output function that looks like this

<center>
    <img src="/assets/images/stepfunction_true.png">
</center>

This is a pretty ugly function where at the critical points of the input, the output simply shifts by a constant value. In fact, this is a continuous function everywhere except at the *critical* points where the left and right limits are not equal to the limit point ([i.e., discontinuities of the first type](https://en.wikipedia.org/wiki/Classification_of_discontinuities)).

Now we can estimate this function using Sklearn's [Gradient Boosting Machine](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingClassifier.html) (GBM) in Python pretty trivially, then we can look at what the model thinks the test set should be given the input data.

<p style="text-align: center; color:gray;"> 
    <img src="/assets/images/stepfunctiongbm.gif">
    <br>
    <i>A gif of a GBM, whoaaa</i>
</p>

We see that the GBM does a great job! In fact, we can calculate the RMSE here and find that it's almost 0. How does a Neural Network do? 

## The Multilayer Perceptron (MLP)

Using the wonderful [Keras](https://keras.io/) library in Python, I estimated an [MLP](https://en.wikipedia.org/wiki/Multilayer_perceptron) with 10 hidden layers, 200 neurons/units in each layer, and ReLU activation units. Here's what the Neural Network (NN) learned:

<p style="text-align: center; color:gray;"> 
    <img src="/assets/images/stepfunctionmlp.gif">
    <br>
    <i>A gif of an MLP, woot!</i>
</p>

Interesting right? The NN learns a smooth function and seems to struggle with the boundary points of the threshold function. 

I should note that I tried experimenting with a variety of different parameters but most provided a result similar to this one. I tried using a maxout layer, adding dropout, increasing the number of neurons and hidden layers, and testing different activation functions. 

Here we find that the specification for this NN wasn't able to learn this threshold function but we were able to learn it fairly easily using GBM (at least with respect to the computation time of both algorithms). 

One of the most important theoretical properties of NNs is that even a *single hidden layer* feed-forward neural network with a finite number of neurons and a activation layer (squashing function) can approximate any continuos function on a compact subset of $R^n$; this is known as the [Universal Approximation Theorem](https://en.wikipedia.org/wiki/Universal_approximation_theorem). Now we know exactly why the NN failed, which is due entirely to the discontinuity of the underlying function! So, it's no surprise that the NN struggles to learn this function, since they are always learning smoooth approximations of the input space. 


Lastly, I thought I'd juxtapose the two residual plots as function of the two features because it further emphasizes the point that the MLP is learning a function that is too smooth.

{:.center}
{:height="100px" width="100px"}
![Look at them there residuals](/assets/images/stepfunction_res.png)

Neural Networks and Deep Learning are awesome and have had a ton of success on a variety of different problems and they have a huge amount of potential in the future, but it's always nice knowing when an algorithm will break. I think it'd be interesting to see what modifications to neural networks are necessary to make learning step functions of this nature easier for them to learn (I'm sure someone much brighter than me will figure it out!).

Anyways, thanks for reading!
-Francisco
