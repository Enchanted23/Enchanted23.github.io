---
layout: blog_post
title: Multi-sense Word Embeddings
date: 2017-11-20 10:32:00 +0300
description: Multi-sense Word Embeddings using Multilingual Context
img: word-embeddings.png
tags: [multi-sense word embeddings, crosslingual training, Bayesian non- parametrics, multilingual distributional information]
---

**This blog is a brief analysis of the outstanding paper Beyond Bilingual: Multi-sense Word Embeddings using Multilingual Context which is the first approach with the ability to leverage multilingual corpora efficiently for multi-sense representation learning.**

![]({{site.baseurl}}/assets/img/Multi-sense-pics/abstract.pdf)



## Introduction of Multi-sense Word Embeddings

> Work on inducing multi-sense embeddings can be divided in two broad categories – two-staged approaches and joint learning approaches.

![]({{site.baseurl}}/assets/img/Multi-sense-pics/related-work.pdf)

The model in this paper adopts the joint learning approach without clustering the contexts first. 

![]({{site.baseurl}}/assets/img/Multi-sense-pics/two-keys.pdf)

**Multilingual distributional information and  nonparametric sense modeling are keys to the success of the multi-sense embedding approach in this paper.**

We can see from later that this model taking advantage of both multilingual distributional information and  nonparametric sense modeling learns high-quality embeddings using substantially less data and parameters than prior state-of-the-art.



## Multilingual VS. Bilingual

Sometimes bilingual information is not enough to discover a new sense in a english words. Let us look at the picture below.

![]({{site.baseurl}}/assets/img/Multi-sense-pics/advantage.pdf)

In this case, we cannot find two different senses of the word “interest” only with English text and French text. Thus we need the multilingual information to help us.



## Model Analysis

The basic model in this paper can be depicted in the picture below.

* This picture only shows the represntation and structure of the bilingual training process. But do not worry, the structure can be easily extended to multilingual.

![]({{site.baseurl}}/assets/img/Multi-sense-pics/bilingual-structure.pdf)

The arrows shows the process below:

1.  **Extending the skip-gram representation to fully describe the relations among an Engish word , its translation to French and their neighboring words** 
2. **Writing the learning algorithm base on this modeling approach to acquire embeddings of all French words and embeddings of different senses of the English words**
3. **Finally, using the embeddings of different senses of the English words to disarmbiguate the sense for a word given a monolingual context (English context in this case)**







