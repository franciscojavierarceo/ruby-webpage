---
layout: post
title: 'PCA and Regression'
comments: true
---

So one thing I've always found myself puzzled by is why someone would use PCA as a first-stage model and feeding the scores into a second stage model (e.g., linear regression). Doing this (potentially) loses a lot information, so I don't see why people think it makes sense. 

I understand the argument about stabalizing the variance of your input space, but with sufficient data and a good regularization strategy, you can solve those problems directly. 

So I decided to make a [Jupyter Notebook](https://github.com/franciscojavierarceo/Python/blob/master/SVD%20and%20Regression.ipynb) highlighting why it's a bad choice. The intuition is identical to what I said above, but with a little more mathematical rigor and some simulations to provide some additional validation. 

The neat idea I had was to visualize how the error of the PCA approximation and the linear regression error behave as a function of (1) the true error, (2) the rank of the approximation in PCA, (3) the true rank of the original matrix, and (4) the correlation structure of the features/design matrix. This simulation samples from a multivariate normal and assumes a normally distributed error term (it may or may not be interesting to see how this behaves for discrete distrbutions--though I expect it'd be similar).

So here's what happened in the first simulation I specificed an uncorrelated covariance matrix (features are not correlated) and I chose a matrix rank from 10 - 31 and an approximation of 1-K components with an error iterated from 1 to 3. Below is the gif as with the error as the time dimension.

![Whoaaa a 3d gif with a gradient](/assets/images/3dplot_gif.GIF)

Pretty neat visualization, right? The z axis shows the ratio of two [RMSEs](https://en.wikipedia.org/wiki/Root-mean-square_deviation) one when using all of the features for the linear regression and the second when using the PCA scores as a feed into a second stage model.

We see that when we have small error the approx 

![Even more gif, yay](/assets/images/3dplotcorr_gif.GIF)