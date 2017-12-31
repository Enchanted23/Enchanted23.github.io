> Yesterday, I decided to review what I had learned about Machine learning, enhancing my knowledge of the fied and preparing for some interviews.

*I will begin with reviewing some of the books I have read. Then I will go deeper into some problems in specific projects or tasks.*

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
  > If no committed recognition neuron’s match overcomes the vigilance parameter, then an uncommitted neuron is committed and its weights are adjusted towards matching the input vector. The vigilance parameter has considerable influence on the system: higher vigilance produces highly detailed memories (many, fine-grained categories), while lower vigilance results in more general memories (fewer, more-general categories).

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

  $$E(s)=-\left(\sum\limits_{{i<j}}w_{{ij}}\,s_{i}\,s_{j}+\sum\limits_{i}\theta _{i}\,s_{i}\right)$$

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



## Some Specific Problems

