---
layout: post
title: 'My first post'
---

# Jekyll pages, Python, and learning to work with GitHub

I recently began working with GitHub and learning the ways of the codes. *It's amazing.*

GitHub is a great way to version your code, especially when working with groups, to ensure 
recovery and quality. GitHub Markdown (formatted) pages are really great because of the ease and intuitive behavior.

## Things I hope to accomplish in this blog:

* Write about Machine Learning things I find interesting
* Provide an "economists" introduction to hacking/data science
* Provide more clear insight on what some of the fancy machine learning algorithms do.
* Create cool visualizations of data
	* Maybe I'll even offer a tutorial
* Provide an overview of some simple NLP methods
* Give my personal opinions about various tools/pieces of software

---


## Universities I've attended
![Columbia](https://raw.githubusercontent.com/franciscojavierarceo/franciscojavierarceo.github.io/master/assets/images/Columbia-University.png)   
![Clemson](https://raw.githubusercontent.com/franciscojavierarceo/franciscojavierarceo.github.io/master/assets/images/clemson-university.png)   
![Illinois State](https://raw.githubusercontent.com/franciscojavierarceo/franciscojavierarceo.github.io/master/assets/images/illinois-state.png)   

---
## Python Code
This is what some code looks like in a nice format.
	def Build_STDM(docs, **kwargs):
    	''' Build Spares Term Document Matrix '''
    	from sklearn.feature_extraction.text import CountVectorizer
	    import pandas as pd
    	vectorizer = CountVectorizer(**kwargs)
    	sparsematrix= vectorizer.fit_transform(docs)
	    vocab = vectorizer.vocabulary_.keys()
	    return sparsematrix, vocab

	# Call the function on e-mail messages. The token_pattern is set so that terms are only
	# words with two or more letters (no numbers or punctuation)
	xs, vocab = Build_STDM(varchar)
	print xs[:,1:10]
	print vocab[0:10]

