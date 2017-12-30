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

   $$D = D_1 \cup D_2 \cup …D_k , \;\; D_i \cap D_j = \varnothing \; (i \neq j)$$

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

We need to learn $w$ and $d$ to get the model.

#### Linear Regression

####Logistic Regression

#### LDA

*Linear Discriminant Analysis*

#### Multi-class Classification

- Split

  *One vs. One (OvO)*

  *One vs. Rest (OvR)(OvA)*

  *Many vs. Many (MvM)* : Error Corercting Outputs Codes (*ECOC*)

#### **Class-imbalance**

*take linear model as example:*

When we use $y = w^{T}x + b$ to classify new sample $x$, if we use $\frac{y}{1-y} > 1$ to judge, $\frac{y}{1-y} > \frac{m^+}{m^-}$.

$m^{+}$ is the number of positive samples.

**rescaling:** 

$$\frac{y'}{1-y'} = \frac{y}{1-y} \times \frac{m^{-}}{m^{+}}$$

* undersampling
* oversampling
* threshold moving

Rescaling is also the base of cost-sensitive learning.

### 3. Decision Tree



## Some Specific Problems

