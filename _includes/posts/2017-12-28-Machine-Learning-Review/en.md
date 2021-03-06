> Yesterday, I decided to review what I had learned about Machine learning, enhancing my knowledge of the fied and preparing for some interviews.

*I will begin with reviewing some of the books I have read or courses I have taken. Then I will go deeper into some problems in specific projects or tasks.*

## Machine Learning (Watermelon Book)

### 1. Model Evaluation and Selection

**Overfitting:**  An overfitted model is a statistical model that **contains more parameters than can be justified by the data**. In statistics, overfitting is "the production of an analysis that corresponds too closely or exactly to a particular set of data, and may therefore fail to fit additional data or predict future observations reliably".

**Underfitting:** It occurs when a statistical model cannot adequately capture the underlying structure of the data. Underfitted model is a model where **some parameters or terms that would appear in a correctly specified model are missing**.

**Evaluation:**

1. **hold-out**

   $$ D=S\cup T ,\;\; S\cap T= \varnothing$$

2. **cross validation**

   $$D = D_1 \cup D_2 \cup …D_k$$

   $$D_i \cap D_j = \varnothing \; (i \neq j)$$

   We use k-1 subsets as tranning set every time and leave one subset as validation set. (*k-fold cross validation*)

   special occasion (m samples , m=k): Leave-One-Out (LOO)


3. **bootstrapping**

   bootstrap sampling: randomly pick one sample from D to D', then put it back to D. After m times, we use the D' as the training set. Then we can use D\D' as validation set.

   $$\lim_{m\to \infty}(1-\frac{1}{m})^{m} \to \frac{1}{e} \approx 0.268$$

   We have nearly 1/3 of D to validate the model. (*out-of-bag estimate*)

4. **parameter tuning**

   Parameter tuning is also very important.


### 2. Linear Model

**Basic Form**

$$f(x) = w^{T}x + b, \; w=(w_1 ; w_2 ; … ; w_d)$$

We need to learn $$w$$ and $$d$$ to get the model.

#### Linear Regression

#### Logistic Regression

#### LDA

*Linear Discriminant Analysis*

#### Multi-class Classification

- Split

  *One vs. One (OvO)*

  *One vs. Rest (OvR)(OvA)*

  *Many vs. Many (MvM)* : Error Corercting Outputs Codes (*ECOC*)

#### **Class-imbalance**

*(take linear model as example:)* When we use $$y = w^{T}x + b$$ to classify new sample $$x$$, if we use $$\frac{y}{1-y} > 1$$ to judge, $$\frac{y}{1-y} > \frac{m^+}{m^-}$$.

$$m^{+}$$ is the number of positive samples.

**rescaling:** 

$$\frac{y'}{1-y'} = \frac{y}{1-y} \times \frac{m^{-}}{m^{+}}$$

* undersampling
* oversampling
* threshold moving

Rescaling is also the base of cost-sensitive learning.

### 3. Decision Tree

We can use **information entropy** to divide (to make the purity of nodes as high as enough).

$$Ent(D) = - \sum\limits_{k=1}^{|y|}p_klog_2p_k$$ 

$$p_k = \frac{m^k}{M}$$ 

Attribute $$a$$ has $$V$$ possible values $${a^1, a^2, …, a^V}$$

**information gain**

$$Gain(D, a) = Ent(D) - \sum\limits_{v=1}^{V}\frac{|D^v|}{|D|}Ent(D^v)$$ 

The basic idea is to make **information gain** as large as possible. In other words, we aim at making the node as pure as possible. 

$$a_* =  \underset{a \in A}{arg\,max} Gain(D,a)$$ - *ID3[Quinlan, 1986]*

**gain ratio**

$$Gain\_ratio(D, a) = \frac{Gain(D,a)}{IV(a)}$$ - *C4.5[Quinlan, 1993]*

$$IV(a)=- \sum\limits_{v=1}^{V}\frac{|D^v|}{|D|}log_2\frac{|D^v|}{|D|}$$

*the intrinsic value of a*

**Gini index**

The basic idea is to randomly pick two samples from D.

$$Gini(D) = \sum\limits_{k=1}^{|Y|}\sum\limits_{k'\neq k}p_kp_{k'} = 1-\sum\limits_{k=1}^{|Y|}p_k^2$$

$$Gini\_index(D,a) = \sum\limits_{v=1}^{V}\frac{|D^v|}{|D|}Gini(D^v)$$

$$a_* = \underset{a \in A}{arg\,min}Gini\_index(D,a)$$ - *CART[Breiman et al., 1984]*

**pruning**

*Pruning can be used to deal with overfitting.*

1. prepruning
2. postpruning

**continuous value**

bi-partition

**missing value**

* $$\hat D$$ is the subset of $$D$$, whose values are not missing. We can use $$\hat D$$ to decide $$a_*$$.

  $$\rho = \frac{\Sigma_{x \in \hat D}w_x}{\Sigma_{x \in D}w_x}$$

  $$Gain(D, a) = \rho \times Gain(\hat D, a)$$

* if sample $$x​$$'s value of $$a_*​$$ is missing, we let $$x​$$ come into all nodes. 

  $$\hat r_v = \frac{\Sigma_{x \in \hat{D}^v}w_x}{\Sigma_{x \in D}w_x}\;\; (1\leq v \leq V)$$

  adjust weight of sample $$x$$ from $$w_x$$ to $$\hat{r}_v \cdot w_x$$ in $$a^v$$.

**multivariate decision tree**

We use $$\sum\limits_{i=1}^{d}w_ia_i=t$$ to divide the samples, in which the $$w_i$$ is the wight of $$a_i$$.

In other words, we use the combination of several attributes to divide the nodes, not a single attribute. 

* OC1
* Preception tree (embed neural network in decision tree)

**incremental learning**

### 4. Neural Networks

To deal with things outside the linearly separable problems, we introduced multi-layer nueral networks.

* **multi-layer feedforward neural networks**
  1. only full connection between adjacent layers
  2. no connection inside a layer

#### Backpropagation

It is based on **gradient descent** strategy.

* **standard error backpropagation**

  updating connection weight $$w$$ and threshold $$ \theta $$ after reading each sample.

* **accumulated error backpropagation**

  updating $$w$$ and $$\theta$$ after reading the whole traning set $$D$$. ***(one round / one epoch)***

To overcome overfitting, there are several ways:

* **early stopping**

  If it occurs that the **validation set's error** is becoming larger, we stop the training.

* **regularization**

  The basic idea is to add a part to describe the complexity of the network:

  $$E_k$$ is the error on $$k^{th}$$ sample.

  $$E = \lambda \frac{1}{m} \sum\limits_{k=1}^{m}E_k + (1-\lambda)\sum\limits_iw_i^2$$

  $$\lambda \in (0, 1)$$ can be estimated using cross validation.

#### Local Minimum

to jump out the local minimum:

* set several **different initial values** to train the network in parallel.

* use **simulated annealing**.

  ![](https://www.researchgate.net/profile/Reza_Teimouri2/publication/257337529/figure/fig7/AS:297539975827461@1447950466303/Fig-8-Flowchart-of-the-simulated-annealing-algorithm.png)

* use **stochatistic gradient descent**.

* use **genetic algorithms**

  ![](https://cdn-images-1.medium.com/max/1600/1*BYDJpa6M2rzWNSurvspf8Q.png)

#### Commen Neural Networks

* **RBF**

  *Radial Basis Function - [Broomhead and Lowe, 1988]*

  ![](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7d/Radial_funktion_network.svg/500px-Radial_funktion_network.svg.png)

  $$\varphi(x) = \sum\limits_{i=1}^{q}w_i\rho(x,c_i)$$

  activation funtion: $$\rho(x,c_i)=e^{-\beta_i\|x-c_i\|^2}$$

  1. to decide the center of the $$i^{th}$$ neuron $$c_i$$:

     **random sampling**, **clustering**, etc.

  2. using **BP** to decide $$w_i$$ and $$\beta_i$$.

* **ART**

  *Adaptive Resonance Theory [Carpenter and Grossberg, 1987] is the representative of* ***competive learning***.

  ***competive learning*** is a common **unsupervised learning** strategy.

  ![](https://upload.wikimedia.org/wikipedia/commons/f/f1/ART.png)

  It typically consists of a **comparison field** and a **recognition field** composed of neurons, a **vigilance parameter** (threshold of recognition), and a **reset module**.

  **winner-take-all:** 

  >  The comparison field takes an input vector (a one-dimensional array of values) and transfers it to its best match in the recognition field. Its best match is the single neuron whose set of weights (weight vector) most closely matches the input vector. Each recognition field neuron outputs a negative signal (proportional to that neuron’s quality of match to the input vector) to each of the other recognition field neurons and thus inhibits their output. In this way the recognition field exhibits lateral inhibition, allowing each neuron in it to represent a category to which input vectors are classified.
  >
  >  If no committed recognition neuron’s match overcomes the vigilance parameter, then an uncommitted neuron is committed and its weights are adjusted towards matching the input vector. The vigilance parameter has considerable influence on the system: higher vigilance produces highly detailed memories (many, fine-grained categories), while lower vigilance results in more general memories (fewer, more-general categories).

  The Adaptive Resonance Theory can be used to deal with **stability plasticity dilemma**. It has **incremental learning** ability.

* **SOM**

  *Self-Organizing Map*

  ![](https://image.slidesharecdn.com/kohonenselforganizingmaps-150702121514-lva1-app6892/95/kohonen-self-organizing-maps-7-638.jpg?cb=1435839352)

  > A **self-organizing map** (**SOM**) or **self-organizing feature map** (**SOFM**) is a type of artificial neural network (ANN) that is trained using **unsupervised learning** to produce a low-dimensional (typically two-dimensional), discretized representation of the input space of the training samples, called a **map**, and is therefore a method to do dimensionality reduction. Self-organizing maps differ from other artificial neural networks as they apply **competitive learning** as opposed to error-correction learning (such as backpropagation with gradient descent), and in the sense that they use a neighborhood function to preserve the **topological** properties of the input space.
  >
  > This makes SOMs useful for visualizing low-dimensional views of high-dimensional data, akin to multidimensional scaling. 

* **Cascade-Correlation**

  *Cascade-Correlation network is a representation of **constructive** neural network.*

  ![](http://blog.scirp.org/wp-content/uploads/2015/06/IJCNS_2015062411185749.bmp)

  It only has input layer and output layer at the beginning of training.

  When a new hidden neuron is added, its input weight is fixed. We maximize the correlation between its output and network error.

* **Elman**

  *Elman network is a common **recurrent  neural network**.*

  ![](http://mnemstudio.org/ai/nn/images/Elman1.gif)

* **Boltzman machine**

  *Boltzman Machine [Ackley et al., 1985] is an **energy-based** model.*

  ![](https://upload.wikimedia.org/wikipedia/commons/f/fd/Boltzmannexamplev2.png)

  *A Boltzmann machine is also a type of **stochastic recurrent neural network** (and **Markov Random Field**).*

  $$s \in \{0,1\}^n$$ represent the states of $$n$$ neurons. *(1 means activated, 0 means inhibited)*

  $$E(s)= (\sum\limits_{i<j}w_{ij}s_{i}s_{j}+\sum\limits_{i}\theta _{i}s_{i})$$

  > The units in the Boltzmann Machine are divided into **'visible' units V**, and **'hidden' units H**. The visible units are those that receive information from the 'environment', i.e. the training set is a set of binary vectors over the set $$V$$. The distribution over the training set is denoted $$P^{+}(V)$$.
  >
  > As is discussed above, the distribution over global states **converges** as the Boltzmann machine reaches **thermal equilibrium**. We denote this distribution, after we marginalize it over the hidden units, as $$P^{-}(V)$$.
  >
  > Our goal is to approximate the **"real" distribution** $$P^{+}(V)$$ using the $$P^{-}(V)$$ produced (eventually) by the machine. To measure how similar the two distributions are, $$G = \sum\limits_{v}{P^{+}(v)\ln\left({\frac{P^{+}(v)}{P^{-}(v)}}\right)}$$, where the sum is over all the possible states of $$V$$.  
  >
  > $$G$$ is a function of the weights, since they determine the energy of a state, and the energy determines $$P^{-}(v)$$, as promised by the Boltzmann distribution. Hence, we can use a gradient descent algorithm over $$G$$, so a given weight, $$w_{ij}$$ is changed by subtracting the partial derivative of $$G$$ with respect to the weight.

  **Restricted Boltzmann machine**

  ![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Restricted_Boltzmann_machine.svg/310px-Restricted_Boltzmann_machine.svg.png)

  > Although learning is impractical in general Boltzmann machines, it can be made quite efficient in an architecture called the **"restricted Boltzmann machine" or "RBM"** which does not allow intralayer connections between hidden units. After training one RBM, the activities of its hidden units can be treated as data for training a higher-level RBM. This method of stacking RBMs makes it possible to train many layers of hidden units efficiently and is one of the most common deep learning strategies. As each new layer is added the overall generative model gets better.

  It often uses the **Contrastive Divergence Algorithm** to train its parameters. 

#### Deep Learning

* **unsupervised layer-wise training:**

  train one hidden layer at a time

  this layer's output as next layer's input

  **pre-training** + **fine-tuning**

* **weight sharing:**

  common in **CNN**

  every plane's neurons share same weight.

I will go deeper into deep learning area in my following blogs, espacially CNN, RNN, GAN and other popular structures.

> Some basic idea of the following two section are aleardy included in [another blog of mine](http://www.chengzhan.me/2017/09/15/Text-Classification/). You can search online if you need something more.

### 5. SVM

[Supported Vector Machines vs Logistic Regression](http://www.cs.toronto.edu/~kswersky/wp-content/uploads/svm_vs_lr.pdf)

### 6. Bayes Classifier

### 7. Ensemble Learning

#### Introduction

*In statistics and machine learning, ensemble methods use multiple learning algorithms to obtain better predictive performance than could be obtained from any of the constituent learning algorithms alone.*

![](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAAmPAAAAJDRiNTBhNjE3LWM0NWYtNGMzNC04YmRkLTUzZTA3NDA0OTgzNw.png)

It can be **homogeneous** or **heterogeneous**.

> Ensembles combine multiple hypotheses to form a (hopefully) better hypothesis. The term **ensemble** is usually reserved for methods that generate multiple hypotheses using the same base learner. The broader term of **multiple classifier systems** also covers hybridization of hypotheses that are not induced by the same base learner.
>
> Evaluating the prediction of an ensemble typically requires more computation than evaluating the prediction of a single model, so ensembles may be thought of as a way to compensate for poor learning algorithms by performing a lot of extra computation. Fast algorithms such as **decision trees** are commonly used in ensemble methods (for example **Random Forest**), although slower algorithms can benefit from ensemble techniques as well.

*An ensemble is itself a **supervised learning** algorithm, because it can be trained and then used to make predictions. The trained ensemble, therefore, represents a single hypothesis. This hypothesis, however, is not necessarily contained within the hypothesis space of the models from which it is built. Thus, ensembles can be shown to have **more flexibility** in the functions they can represent. This flexibility can, in theory, enable them to over-fit the training data more than a single model would, but in practice, some ensemble techniques (especially **bagging**) tend to **reduce problems related to over-fitting** of the training data.*

*Empirically, ensembles tend to yield better results when there is **a significant diversity** among the models. Many ensemble methods, therefore, seek to promote diversity among the models they combine. Although perhaps non-intuitive, more random algorithms (like random decision trees) can be used to produce a stronger ensemble than very deliberate algorithms (like entropy-reducing decision trees). **Using a variety of strong learning algorithms, however, has been shown to be more effective than using techniques that attempt to dumb-down the models in order to promote diversity**.*

Ensemble learning can be divided into two classes according to the way how component learners are generated:

1. Component learners have **strong dependencies**, which can only be generated **in series**. *(Eg. Boosting)*
2. Component learners have **no strong dependencies**, which can be generated **in parallel**. *(Eg. Bagging, Random Forest)*

#### Boosting

> While boosting is not algorithmically constrained, most boosting algorithms consist of iteratively learning weak classifiers with respect to a distribution and adding them to a final strong classifier. When they are added, they are typically weighted in some way that is usually related to **the weak learners' accuracy**. After a weak learner is added, the data are reweighted: examples that are misclassified gain weight and examples that are classified correctly lose weight (some boosting algorithms actually decrease the weight of repeatedly misclassified examples, e.g., boost by majority and BrownBoost). Thus, future weak learners **focus more on the examples that previous weak learners misclassified**

There are many boosting algorithms. A very popular one is **AdaBoost**. It can be deduced in several different ways, one of which is **additive model**.

It uses $$H(x) = \sum\limits_{t=1}^{T} \alpha_t h_t(x)$$ to minimize **exponential loss function**:

$$\ell_{exp}(H|D) = E_{x \sim D}[e^{-f(x)H(x)}]$$

Suppose we have a data set $$\{(x_1,y_1),…,(x_m,y_m)\}$$ where each item $$x_{i}$$ has an associated class $$y_{i}\in \{-1,1\}$$, and and a set of weak classifiers$$\{h_{1},\ldots,h_{T}\}$$ each of which outputs a classification $$h_{j}(x_{i})\in \{-1,1\}$$ for each item. After the $$(t−1)^{th}$$ iteration our boosted classifier is a linear combination of the weak classifiers of the form:

$$H_{t-1}(x_i)=\alpha_1h_1(x_i)+\cdots +\alpha _{t-1}h_{t-1}(x_i)$$

So it remains to determine which weak classifier is the best choice for $$h_t$$, and what its weight $$\alpha_{t}$$ should be.

$$\epsilon_t = \frac{\sum\limits_{y_i \neq h_t(x_i)}w_i^{(t)}}{\sum\limits_{i=1}^mw_i^{(t)}} = P_{x \sim D}(h_t(x_i) \neq y_i)$$

New $$\alpha_th_t$$ must minimize

$$\ell_{exp}({\alpha_th_t}|{D_t}) = \mathbb{E}_{x \sim D_t}[e^{-f(x)\alpha_th_t(x)}]$$

According to partial derivative:

$$\alpha_m = \frac{1}{2}ln(\frac{1-\epsilon_m}{\epsilon_m})$$

At each iteration, we choose the classifier $$h_t$$ which minimizes the total weighted error:

$$\sum\limits_{h_t(x_i) \neq y_i}w_i^{(m)}$$

We use this to calculate the error rate $$\epsilon_m$$, then we use $$\epsilon_m$$ to get the weight $$\alpha_m$$. Fiinally, we improve the boosted classifier $$H_{t-1}$$ to $$H_t=H_{t-1}+\alpha_th_t$$

#### **Bagging and Random Forest**

1. **Bagging**

   It is kind of based on **bootstrap sampling**, which means each component learner only uses $$63.2\%$$ of the samples, leaving the remaining $$36.8\%$$ to do the **out-of-bag estimate** of the **diversity**.

   Not like **standard Adaboost** which can only be used for binary classification, it can be used for **multi-class classification** and **regression**. And it's **efficient**.

2. **Random Forest**

   Random Forest combined **Bagging** and **random selection of features**. 

   Choose a subset consisting of $$k$$ features from a node which have $$d$$ features. *(k<d)*

   We often use $$k = log_2d.$$

#### Ensemble Strategy

1. **averaging**

   simple averaging: 

   $$H(x)=\frac{1}{T}\sum\limits_{i=1}^{T}h_i(x)$$

   weighted averaging:

   $$H(x)=\sum\limits_{i=1}^{T}w_ih_i(x)$$

2. **voting**

   majority voting:

   $$H(x)=\begin{cases} c_j, & \mbox{if  }\sum\limits_{i=1}^{T}h_i^j(x)>0.5\sum\limits_{k=1}^{N}\sum\limits_{i=1}^{T}h_i^k(x)\mbox{;} \\reject, & \mbox{otherwise.} \end{cases}$$

   plurality voting:

   $$H(x) = c_{\underset{j}{arg\,max}\sum\limits_{i=1}^{T}h_i^j(x)}$$

   weighted voting:

   $$H(x) = c_{\underset{j}{arg\,max}\sum\limits_{i=1}^{T}w_ih_i^j(x)}$$

   hard voting: $$h_i^j \in \{0,1\}$$

   soft voting: $$h_i^j \in [0,1]$$

3. **learning**

   Learning strategy is usually more powerful. 

   **Stacking** is a representative learning method of ensemble strategy.

   > Stacking (sometimes called stacked generalization) involves **training a learning algorithm** to combine the predictions of several other learning algorithms. First, all of the other algorithms are trained using the available data, then a **combiner algorithm** is trained to make a final prediction using all the predictions of the other algorithms as additional inputs. If an arbitrary combiner algorithm is used, then stacking can theoretically represent any of the ensemble techniques described in this article, although in practice, **a single-layer logistic regression model** is often used as the combiner.
   >
   > Stacking typically yields performance better than any single one of the trained models. It has been successfully used on both supervised learning tasks (**regression, classification and distance learning**) and unsupervised learning (**density estimation**). It has also been used to **estimate bagging's error rate**. It has been reported to **out-perform Bayesian model-averaging**. The two top-performers in the Netflix competition utilized blending, which may be considered to be a form of stacking.



> I will jump over some of the following sections and come back to complete them when I have more time.

### 8. Clustering

### 9. Dimension Reduction & Metric Learning 

* **curse of dimentionality**

  1. sparse sample
  2. hard to compute distence

* **dimention reduction**

  benefits: more **dense samples** + easier to **compute distence**

  why we can do this: Most of the time, it is feature space's **low dimentional embedding** that is truly related to the training task.

#### MDS

*multiple dimensional scaling is a classical dimention reduction method*

m samples, the distance matrix is $$D\in\mathbb{R}^{m\times m}$$, $$d_{ij} = dist_{ij}$$ is the distance between $$x_i$$ and $$x_j$$. 

our goal is to gain $$Z\in\mathbb{R}^{d'\times m},\, d'\leq d$$, in which $$\|z_i - z_j \|=dist_{ij}$$.

$$B=Z^TZ \in \mathbb{R}^{m\times m}, \; b_{ij}=z_i^Tz_j$$

$$B=V\Lambda V^T, \Lambda = diag(\lambda_1, \ldots,\lambda_d)$$

there are $d^*$ nonzero eigenvalues in $$\lambda_1\geq \ldots \geq \lambda_d$$

$$Z = \Lambda_*^{1/2}V_*^T \in \mathbb{R}^{d^*\times m}$$

we only need the distances being close not strictly the same.

so we use $d^* \ll d$ largest eigenvalues:

$$\tilde{\Lambda} = diag(\lambda_1, \ldots,\lambda_{d^*})$$

$$Z = \tilde{\Lambda}_*^{1/2}\tilde{V}^T \in \mathbb{R}^{d'\times m}$$

#### PCA

*principle component analysis is a very common dimention reduction method*

> PCA is mathematically defined as an orthogonal **linear transformation** that transforms the data to a new coordinate system such that the greatest variance by some projection of the data comes to lie on the first coordinate (called the first principal component), the second greatest variance on the second coordinate, and so on.

if we has a **hyperplane** which can describe all samples.

it should have following two properties:

* all samples should be **close enough** to this hyperplane.
* samples' **projection** on this hyperplane should be **clearly seperated**.

we can deduce from any of the properties above and get the optimize goal of PCA:

Eg. we know that sample $$x_i$$'s projection on hyperplane is $$W^Tx_i$$.

If we want samples' projection on this hyperplane to be clearly seperated, we should maximize the **variance**, $$\sum_iW^Tx_ix_i^TW$$.

$$\underset{W}{max}\;tr(W^TXX^TW)$$

$$s.t. W^TW=I$$

$$XX^TW = \lambda W$$

thus we only need to do eigenvalues decomposition of the matrix $$X^TX$$, get

$$\lambda_1 \geq \lambda_2 \geq \ldots \geq \lambda_d$$

the we fetch $$W=(w_1,\ldots,w_{d'})$$ corresponding to largest $$d'$$ eigenvalues.

1. **centering**

   $$x_i \leftarrow x_i - \frac{1}{m}\sum\limits_{i=1}^{m}x_i$$

2. **covariance matrix**

   compute the samples' covariance matrix $$XX^T$$

3. **eigenvalues decomposition**

4. **get projection matrix**

we can use **cross-validation** to get a better $$d'$$.

or we can set a **reconstruction threshold**: eg. $$t=95\%$$

and pick the minimum of $$d'$$ which satisfy $$\frac{\sum_{i=1}^{d'}\lambda_i}{\sum_{i=1}^{d}\lambda_i} \geq t$$.

#### KPCA

#### manifold learning

#### metric learning

### 10. Feature Selection & sparse coding

### 11. Computational learning theory

### 12. Semi-supervised Learning

### 13. Proabilistic Graphical Model

#### HMM

*Hidden Markov Model is a simple **dynamic Bayesian network**, which is a very famous **directed-graph** model.*

![](http://upload.wikimedia.org/wikipedia/commons/thumb/2/2e/HiddenMarkovModel.png/300px-HiddenMarkovModel.png)

$$A = [a_{ij}]_{N\times N}$$

$$B = [b_{ij}]_{N\times M}$$

$$\pi = (\pi_1, \ldots,\pi_N),\; \pi_i=P(x_1=s_1)$$

people always focus on three basic problems of HMM:

* given $$\lambda = [A,B,\pi]$$, how to compute $$P(y \mid \lambda)$$. 

  observed sequence: $$y=\{y_1,y_2,\ldots,y_n\}$$

* given $$\lambda = [A,B,\pi]$$ and $$y=\{y_1,y_2,\ldots,y_n\}$$, how to infer $$x=\{x_1,x_2,\ldots,x_n\}$$.

* given $$y=\{y_1,y_2,\ldots,y_n\}$$, how to adjust $$\lambda = [A,B,\pi]$$ to maximize $$P(x \mid \lambda)$$.

#### MRF

*Markov Random Field, a famous **undirected-graph** model, is a typical **Markov network**.* 

![](http://slideplayer.com/3172441/11/images/5/Markov+Random+Field+%28Markov+Network%29.jpg)

* **clique**: the subset of all nodes in which each two nodes have connection
* **maximal clique**

For $$x=\{x_1,\ldots,x_n\}$$, all **cliques** form $$\mathcal{C}$$

$$P(x)=\frac{1}{Z}\prod\limits_{Q\in\mathcal{C}} \psi_Q(x_Q)$$

$$\psi_Q$$ is the **potential function** of $$Q$$

$$Z=\sum\limits_x \prod\limits_{Q \in \mathcal{C}} \psi_Q(x_Q)$$

if $$Q$$ is not a maximal clique, it must be included in a maximal clique $$Q^*$$

$$P(x)=\frac{1}{Z^*}\prod\limits_{Q\in\mathcal{C}^*} \psi_Q(x_Q)$$

**Global Markov property**: Any two subsets of variables are **conditionally independent** given a separating subset:

$$X_A \perp X_B \mid X_S$$

where every path from a node in A to a node in B passes through S.

**Local Markov property**: A variable is conditionally independent of all other variables given its neighbors: 

$$x_v \perp x_{V\setminus n^*(v)} \mid x_{n(v)}$$ 

where $$n(v)$$ is the set of neighbors of $$v$$, and $$n^*(v)=n(v)\cup {v}$$ is the closed neighbourhood of v.

**Pairwise Markov property**: Any two **non-adjacent variables** are conditionally independent given all other variables:

$$x_u \perp x_v \mid x_{V \setminus <u,v>}$$

where $$<x,v> \notin E$$

potential function $$\psi_Q$$ is used to describe the **two-variable correlation** inside $$x_Q$$.

$$log(\psi_Q)$$ has a direct interpretation as the potential energy of a configuration $$Q$$.

#### CRF

*CRFs are a type of discriminative undirected graphical model.*

![](https://www.codeproject.com/KB/recipes/559535/gerative-discriminative.png)

The following picture is a **chain-structured CRF**, often used to deal with **sequences**.

![](https://image.slidesharecdn.com/conditionalrandomfields-091116015256-phpapp02/95/conditional-random-fields-8-728.jpg?cb=1258336451)

$$P(y \mid x) = \frac{1}{Z}exp\left(\sum\limits_j\sum\limits_{i=1}^{n-1}\lambda_jt_j(y_{i+1},y_i,x,i) + \sum\limits_k\sum\limits_{i=1}^{n} \mu_ks_k(y_i,x,i) \right)$$

**transition feature function**: $$t_j(y_{i+1},y_i,x,i)$$

**status feature function**: $$s_k(y_i,x,i)$$

**parameters**: $$\lambda_j,\,\mu_k$$

Eg.  tagging

$$x = $$ The boy knocked at the watermelon.

$$y = $$ \[D\] \[N\] \[V\] \[P\] \[D\] \[N\]

$$t_j(y_{i+1},y_i,x,i) = \begin{cases} 1, & \mbox{if  } y_{i+1}=[P], y_i=[V] \mbox{ and } x_i= \mbox{"knock",} \\ 0, & \mbox{otherwise.} \end{cases}$$

$$s_k(y_i,x,i) = \begin{cases} 1, & \mbox{if  } y_{i}=[V] \mbox{ and } x_i= \mbox{"knock",} \\ 0, & \mbox{otherwise.} \end{cases}$$

#### Learning & Inference

* to calculate the **marginal distribution** or **conditional distribution**. *(very hard)*

  1. **variable elimination**

  2. **belief propagation**

     ![](http://img.blog.csdn.net/20141207200307843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYXNwaXJpbnZhZ3JhbnQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* to get **approximate solution** of the original question. *(practical)*

  1. **sampling**
  2. **variational inference**

#### Topic Model

### 14. Rule Learning

### 15. Reinforecement Learning

> *Reinforecement Learning will be included in one of my following blogs, so I will not write it here.*