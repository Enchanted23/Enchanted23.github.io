**This blog is a brief analysis of the outstanding paper Beyond Bilingual: Multi-sense Word Embeddings using Multilingual Context which is the first approach with the ability to leverage multilingual corpora efficiently for multi-sense representation learning.**

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/abstract.pdf)



## Introduction of Multi-sense Word Embeddings

> Work on inducing multi-sense embeddings can be divided in two broad categories – two-staged approaches and joint learning approaches.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/related-work.pdf)

The model in this paper adopts the joint learning approach without clustering the contexts first. 

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/two-keys.pdf)

**Multilingual distributional information and  nonparametric sense modeling are keys to the success of the multi-sense embedding approach in this paper.**

We can see from later that this model taking advantage of both multilingual distributional information and  nonparametric sense modeling learns high-quality embeddings using substantially less data and parameters than prior state-of-the-art.



## Multilingual VS. Bilingual

Sometimes bilingual information is not enough to discover a new sense in a english words. Let us look at the picture below.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/advantage.pdf)

In this case, we cannot find two different senses of the word “interest” only with English text and French text. Thus we need the multilingual information to help us.



## Model Analysis

### model description

The basic model in this paper can be depicted in the picture below.

- This picture only shows the represntation and structure of the bilingual training process. But do not worry, the structure can be easily extended to multilingual.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/bilingual-structure.pdf)

The arrows shows the process below:

1. **Extending the skip-gram representation to fully describe the relations among an Engish word , its translation to French and their neighboring words** 
2. **Writing the learning algorithm base on this modeling approach to acquire embeddings of all French words, embeddings of different senses of the English words and corresponding distribution probabilities**
3. **Finally, using the embeddings of different senses of the English words and corresponding distribution probabilities to disarmbiguate the sense for a word given a monolingual context (English context in this case)**

### the analysis of the value of the factor modeling the dependence of the English context word on foreign word

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/two-side.pdf)

### experimental setup

Let us see how this paper use the multilingual information properly, the picture below show the sources and preprocessing of the parallel corpora.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/Parallel Corpora.pdf)

The parameter tuning method in this paper is also amazing, which considers both fairness and proficiency.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/Parameter Tuning.pdf)

### experimental results

We all know that a great method or theory should be proven by some well-desined verified experiments, so the intelligent writers of this paper did research thoroughly and found several suitable tasks or standards to prove their ideas.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/results.pdf)

Some supplementary explanations of WSI:

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/Word Sense Induction (WSI).pdf)

To make the story more explicit,  the paper uses qualitative illustration by offering PCA plots for the vectors for {apple, bank, interest, itu nes, potato, west, monetary, desire} with multiple sense vectors for appl e,interest and bank.

![]({{site.baseurl}}/img/in-post/Multi-sense-pics/Qualitative Illustration.pdf)



## References

**Upadhyay Shyam, Chang Kai-Wei, Zou James, Taddy Matt, and Kala Adam. 2017. Beyond bilingual: Multi-sense word embeddings using multilingual context. In ICLR.**

