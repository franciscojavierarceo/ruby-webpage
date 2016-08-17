---
layout: post
title: 'Bayesian Deep Learning'
comments: true
---

So I've been checking out a new Python library, [Edward](https://github.com/blei-lab/edward), developed by [David Blei](http://www.cs.columbia.edu/~blei/)'s ML research group at Columbia recently. 

It's been fairly interesting thusfar, but I haven't dived extraordinarily deep into the documentation yet. Instead, I spent some time tinkering with one of their examples: [Bayesian Neural Networks](https://raw.githubusercontent.com/blei-lab/edward/master/examples/bayesian_nn.py). 

What is this model? It's fairly interesting. It's a Neural network with a Gaussian prior distribution on the weights (with an assumed variance of 1 and unknown mean). As I've mentioned probably too many times on this site, I've studied both Bayesian ML and Deep Learning, so I was particularly excited about this so I could put the two together! Interestingly, Bayesian Deep Learning is a [growing area of research](https://arxiv.org/pdf/1604.01662v2.pdf) with a lot of potential. Hopefully, I'll explore this area more because it'd neatly tie two things I really love. 

Back to my main point in writing this: what I did with the tutorial. 

Their example shows a neat graph of the learned posterior predictive distribution of the of a 2-hidden layer neural network at iteration 0 and iteration 1000, which is really cool. I thought it'd be cool to add two things: (1) a look at the Variational objective function and (2) a dynamic, full view of the posterior as a function of the iterations. It's always interesting to see how parameters behave as ML models begin to learn the appropriate functions, so I thought this exercise would be fun (I also love the excuse to make a gif whenever I can).

![Le Variational Objective Function](/assets/images/vof_bnn.jpeg)

Above is a look at the variational objective function, it sucks at first (to be expected) but seems to converge pretty quickly!

Now here's a look at the posterior predictive learned during training. Pretty neat huh? 
![Whoaaa a gif](/assets/images/movie_bayesian_nn.gif)

That's all for now, next I want to try this model out on MNIST and see how it performs, which I think will be a fun exercise.

Anyways, thanks for reading! Laslty, feel free to peak at the code to make this gif. It's available on my [github](https://github.com/franciscojavierarceo/edward/blob/master/examples/bayesian_nn.py)!