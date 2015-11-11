---
layout: post
title: 'My first post'
---

# Jekyll pages, Python, and learning to work with GitHub

I recently began working with GitHub and learning the ways of the codes. *It's amazing.*

GitHub is a great way to version your code, especially when working with groups, to ensure 
recovery and quality. GitHub Markdown (formatted) pages are really great because of the ease and intuitive behavior.

## Things I hope to accomplish in this blog:

* Write about Machine Learning topics that I find interesting
* Provide an "economist's" introduction to hacking/data science
* Provide more clear insight on what some of the fancy machine learning algorithms do.
* Provide an overview of some simple NLP methods
* Give my personal opinions about various tools/pieces of software
* Create cool visualizations of data
	* Maybe I'll even offer a tutorial

---
## Code
One of the many great things that Jekyll does is formatting code for you. Below is some simple code
that takes in a column of text (from some arbitrary .csv file) and outputs a sparse matrix
of unigrams/bag-of-words stored as binary variables (also called one-hot-encoding)
that you can use for a simple regression (which is what this code does).

	import scipy
	import os, sys
	import numpy as np
	import pandas as pd
	import matplotlib.pyplot as plt
	from sklearn import linear_model, decomposition
	from sklearn.pipeline import Pipeline
	from sklearn.grid_search import GridSearchCV
    from sklearn.feature_extraction.text import CountVectorizer
	def roc_plot(actual,pred,ttl):
    	fpr, tpr, thresholds = roc_curve(actual, pred)
    	roc_auc = auc(fpr, tpr)
	    print("The Area Under the ROC Curve : %f" % roc_auc)
	    # Plot ROC curve
    	plt.clf()
	    plt.plot(fpr, tpr, color='red',label='ROC curve (area = %0.2f)' % roc_auc)
	    plt.plot([0, 1], [0, 1], 'k')
	    plt.xlim([0.0, 1.0])
	    plt.ylim([0.0, 1.0])
    	plt.grid()
    	plt.xlabel('False Positive Rate')
    	plt.ylabel('True Positive Rate')
	    plt.title('ROC Curve'+'\n'+ttl)
	    plt.legend(loc="lower right")
	    plt.show()

	def Build_STDM(docs, **kwargs):
    	''' Build Sparse Term Document Matrix '''
    	vectorizer = CountVectorizer(**kwargs)
    	sparsematrix= vectorizer.fit_transform(docs)
	    vocab = vectorizer.vocabulary_.keys()
	    return sparsematrix, vocab
	
	df1 = pd.read_csv('myregressiondata.csv',sep=',')
	varchar = df1['MyTextField']
	xs = xs[:,1:10]
	y = np.hstack((np.ones(100/2),np.zeros(100/2))).reshape((100,1))
	betas = scipy.sparse.linalg.inv(xs.T.dot(xs)).dot(xs.T).dot(y)
	bdf= pd.DataFrame()
	bdf['Words'] = vocab[1:10]
	bdf['betas'] = betas

	print bdf
	logistic = linear_model.LogisticRegression(penalty='l2',tol=0.0001,
                                           fit_intercept=True,intercept_scaling=1)
	MyModel = logistic.fit(xs,y)
	betas = MyModel.coef_.ravel()
	ys = logistic.predict_proba(xs)[:,0]

	# Call the function on e-mail messages. The token_pattern is set so that terms are only
	# words with two or more letters (no numbers or punctuation)
	xs, vocab = Build_STDM(varchar)
	print xs[:,1:10]
	print vocab[0:10]

