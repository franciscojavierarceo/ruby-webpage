---
layout: post
title: 'PCA and Regression'
comments: true
---

So one thing I've always found myself puzzled by is why someone would use PCA as a first-stage model and feeding the scores into a second stage model (e.g., linear regression). Doing this (potentially) loses a lot information, so I don't see why people think it makes sense. 

I understand the argument about stabalizing the variance of your input space, but with sufficient data and a good regularization strategy (e.g., L1), you can solve those problems directly. 

So I decided to make a [Jupyter Notebook](https://github.com/franciscojavierarceo/Python/blob/master/SVD%20and%20Regression.ipynb) highlighting why it's a bad choice. The intuition is identical to what I said above, but with a little more mathematical rigor and some simulations to provide some additional validation/insight. 

The neat idea I had was to visualize how the error of the PCA approximation and the linear regression error (technically, I plot their ratio) behave as a function of (1) the true error, (2) the rank of the approximation in PCA, (3) the true rank of the original matrix, and (4) the correlation structure of the features/design matrix. This simulation samples from a multivariate normal and assumes a normally distributed error term (it may or may not be interesting to see how this behaves for discrete distrbutions--though I expect it'd be similar).

So here's what happened in the first simulation I specificed an uncorrelated covariance matrix (features are not correlated) and I chose a matrix rank from 10 - 31 and an approximation of 1-K components with an error iterated from 1 to 3. Below is the gif with the error as the time dimension (i.e., I decrease the error from 3.0 to 1.0 as the gif continues).

{:.center}
![Whoaaa a 3d gif with a gradient](/assets/images/3dplot_gif.GIF)

Pretty neat visualization, right? The z axis shows the ratio of two [RMSEs](https://en.wikipedia.org/wiki/Root-mean-square_deviation) one when using all of the features for the linear regression and the second when using the SVD/PCA scores as a feed into a second stage model. If the ratio is close to 1, that means we're not losing anything from the approximation. If the ratio is greater than 1 that means we are losing information. Notice that at the final iteration when the Model Error = 1.0, the error rate of the PCA 2-stage model can be as high as 5x the error of the single stage model!

We see that when we have large error the approximation is less impactful but as we decrease the amount of noise in the system the performance of the low-rank approximation causes us to do much worse. This result is intuitive because as the noise decreases in the system the features are proportionally more important, so taking an approximation of them and attributing the error to your outcome becomes more impactful, thus our ability to learn the function reduces.

For the first simulation I generated the covariance matrix from a uniform distribution but this implies the features are uncorrelated, so I thought I'd experiment with a correlated covariance matrix  in the plot below.

{:.center}
![Even more gif, yay](/assets/images/3dplotcorr_gif.GIF)

We see that the the ratio of the two models now produces a varying shape on the z axis, which makes sense since different rank approximations will produce different shapes for varying ranks of the design matrix. 

Ultimately the conclusion remains the same, using PCA/SVD as a pipeline to a regression model leads to a significant degridation in model performance.

Obviously this particular example was pathological and we *defined* the outcome to be exactly our reconstruction error, we can certainly create an example that loses no performance in model degredation but it's very rare that we will do better from PCA/SVD and if we do, it's likely due to sampling error and is unlikely to hold repeatedly. 

Again, PCA/SVD is awesome for a ton of other things, but it's a suboptimal choice to use as a pipeline for supervised learninsupervised learning. Use the features themselves, you can always do better.

Thanks for reading!
-Francisco

.center {
    text-align: center;
}