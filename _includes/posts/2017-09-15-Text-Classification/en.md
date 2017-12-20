> some simple ways to do text classification

## Introduction

### 1. Definition

**Input:**
a document d + a fixed set of classes C = {c1, c2, ... , cn}

**Output：**
a predicted class ci from C

### 2. Some simple application

- spam detection
- authorship attribution
- age/gender identification
- sentiment analysis
- assigning subject categories, topics or genes
- ......

## Traditional methods

### 1. Naive Bayes

![](http://upload-images.jianshu.io/upload_images/7248047-a9fbc902b6bca055.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**two assumptions：**

1. Bag of words assumption：
   position doesn't matter

2. Conditional independency：

   ![](http://upload-images.jianshu.io/upload_images/7248047-83f97cb1f1de612b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**to compute these probabilities：**

![](http://upload-images.jianshu.io/upload_images/7248047-885455a278a41c4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**add-one smoothing** to prevent the situation in which we get zero：(you can add other number as well)

![](http://upload-images.jianshu.io/upload_images/7248047-9a09b8a0115dc190.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**to deal with unknown/unshown words：**

![](http://upload-images.jianshu.io/upload_images/7248047-a1cc432e8bd3b937.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**main features：**

- very fast, low storage requirements
- robust to irrelevant features
- good in domains with many equally important features
- optimal if the indolence assumption hold
- lacks accuracy in general

### 2. SVM

**cost function of SVM：**

![](http://upload-images.jianshu.io/upload_images/7248047-cdc59bc9852bfe52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**SVM decision boundary**

when C is very large：

![](http://upload-images.jianshu.io/upload_images/7248047-80626e5bc0c1b4de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/7248047-e7a7e8b435c9c107.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**about kernel：**

![](http://upload-images.jianshu.io/upload_images/7248047-b6acd4ebc476164c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/7248047-4d4caed355a07259.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/7248047-1c6a0cb542fa1a4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**until now，it seems that the SVM are only applicable to two-class classification.**

![](http://upload-images.jianshu.io/upload_images/7248047-a42c40d8c15dfe9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Comparing with Logistic regression：**

![](http://upload-images.jianshu.io/upload_images/7248047-d7f92efb41d32a3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## Neural network methods

### 1. CNN

**(1) the paper Convolutional Neural Networks for Sentence Classification which appeared in EMNLP 2014**

**(2) the paper A Sensitivity Analysis of (and Practitioners' Guide to) Convolutional Neural Networks for Sentence Classification**

![](http://upload-images.jianshu.io/upload_images/7248047-b53b1cea13d0f9df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

The model uses multiple filters to obtain multiple features. These features form the penultimate layer and are passed to a fully connected softmax layer whose output is the probability distribution over labels.

![](http://upload-images.jianshu.io/upload_images/7248047-6ee602c694a4d0d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

For regularization we employ dropout on the penultimate layer with a constraint on l2-norms of the weight vectors. Dropout prevents co-adaptation of hidden units by randomly dropping out.

**Pre-trained Word Vectors**

We use the publicly available word2vec vectors that were trained on 100 billion words from Google News.

**Results**

![](http://upload-images.jianshu.io/upload_images/7248047-437c1622e45dbaed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**There is simplified implementation using Tensorflow on Github：https://github.com/dennybritz/cnn-text-classification-tf**

### 2. RNN

**the paper Hierarchical Attention Networks for Document Classification which appeared in NAACL 2016**

> in this paper we test the hypothesis that better representations can be obtained by incorporating knowledge of document structure in the model architecture

1. **It is observed that different words and sentences in a documents are differentially informative.**

2. **Moreover, the importance of words and sentences are highly context dependent.**

   i.e. the same word or sentence may be dif- ferentially important in different context

> Attention serves two benefits: not only does it often result in better performance, but it also provides in- sight into which words and sentences contribute to the classification decision which can be of value in applications and analysis

**Hierarchical Attention Network**

![](http://upload-images.jianshu.io/upload_images/7248047-ef79652a535a1c5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**If you want to learn more about Attention Mechanisms：http://www.wildml.com/2016/01/attention-and-memory-in-deep-learning-and-nlp/**

In the model they used the GRU-based sequence encoder.

**1. Word Encoder：**

![](http://upload-images.jianshu.io/upload_images/7248047-b77f7c07f92db489.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2. Word Attention：**

![](http://upload-images.jianshu.io/upload_images/7248047-9f7cd0612d33d3ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/7248047-5f193bd24820bd42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3. Sentence Encoder：**

![](http://upload-images.jianshu.io/upload_images/7248047-193591d47aa55f4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4. Sentence Attention：**

![](http://upload-images.jianshu.io/upload_images/7248047-1317c10bfd594957.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**5. Document Classification：**
**Because the document vector v is a high level representation of document d：**

![](http://upload-images.jianshu.io/upload_images/7248047-b62d01c9acc7d997.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*j* is the label of document *d*

![](http://upload-images.jianshu.io/upload_images/7248047-0a22987eb37a49fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Results**

![](http://upload-images.jianshu.io/upload_images/7248047-64b323363e6bb727.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**There is simplified implementation written in Python on Github：https://github.com/richliao/textClassifier**



## References

https://www.cs.cmu.edu/%7Ediyiy/docs/naacl16.pdf

https://www.cs.cmu.edu/%7Ediyiy/docs/naacl16.pdf

https://www.coursera.org/learn/machine-learning/home/

https://www.youtube.com/playlist?list=PL6397E4B26D00A269

