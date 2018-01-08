*I will begin with reviewing some of the books I have read or courses I have taken. Then I will go deeper into some problems in specific projects or tasks.*

## Specific Problems

### Gradient Descent

#### Gradient descent variants

> *There are three variants of gradient descent, which differ in **how much data we use to compute the gradient of the objective function**. Depending on the amount of data, we make a **trade-off between the accuracy of the parameter update and the time it takes to perform an update**.*

##### (1) Batch gradient descent

Vanilla gradient descent, aka batch gradient descent, computes the gradient of the cost function w.r.t. to the parameters $$θ$$ for the entire training dataset:

$$θ=θ−η⋅∇_θJ(θ)$$

```python
for i in range(nb_epochs):
  params_grad = evaluate_gradient(loss_function, data, params)
  params = params - learning_rate * params_grad
```

##### (2) Stochastic gradient descent

Stochastic gradient descent (SGD) in contrast performs a parameter update for *each* training example $$x^{(i)}$$ and label $$y^{(i)}$$:

$$θ=θ−η⋅∇_θJ(θ;x^{(i)};y^{(i)})$$

> *Batch gradient descent performs **redundant computations** for large datasets, as it recomputes gradients for similar examples before each parameter update. SGD does away with this redundancy by performing one update at a time. It is therefore usually **much faster** and can also be used to learn online.*

```python
for i in range(nb_epochs):
  np.random.shuffle(data)
  for example in data:
    params_grad = evaluate_gradient(loss_function, example, params)
    params = params - learning_rate * params_grad
```

##### (3) Mini-batch gradient descent

Mini-batch gradient descent finally takes the best of both worlds and performs an update for every mini-batch of $$n$$ training examples:

$$θ=θ−η⋅∇_θJ(θ;x^{(i:i+n)};y^{(i:i+n))}$$

This way, it *a)* **reduces the variance of the parameter updates, which can lead to more stable convergence**; and *b)* **can make use of highly optimized matrix optimizations common to state-of-the-art deep learning libraries that make computing the gradient w.r.t. a mini-batch very efficient**. Common mini-batch sizes range between 50 and 256, but can vary for different applications. 

```python
for i in range(nb_epochs):
  np.random.shuffle(data)
  for batch in get_batches(data, batch_size=50):
    params_grad = evaluate_gradient(loss_function, batch, params)
    params = params - learning_rate * params_grad
```

#### Gradient descent optimization algorithms

##### (1) Momentum

SGD has trouble navigating ravines, i.e. areas where the surface curves much more steeply in one dimension than in another, which are common around local optima. In these scenarios, SGD oscillates across the slopes of the ravine while only making hesitant progress along the bottom towards the local optimum as in Image 2.

![](https://www.jiqizhixin.com/data/upload/ueditor/20161121/58329acf49465.png)

Momentum is a method that helps accelerate SGD in the relevant direction and dampens oscillations as can be seen in Image 3. It does this by adding **a fraction $$γ$$ **of the update vector of the past time step to the current update vector:

$$v_t=γv_{t−1}+η∇_θJ(θ)$$

$$θ=θ−v_t$$

Note: Some implementations exchange the signs in the equations. The momentum term $$γ$$ is usually set to 0.9 or a similar value.

Essentially, when using momentum, we push a ball down a hill. The ball accumulates momentum as it rolls downhill, becoming faster and faster on the way (until it reaches its terminal velocity if there is air resistance, i.e. $$γ<1$$). The same thing happens to our parameter updates: **The momentum term increases for dimensions whose gradients point in the same directions and reduces updates for dimensions whose gradients change directions**. As a result, we gain faster convergence and reduced oscillation.

##### (2) Nesterov accelerated gradient

However, a ball that rolls down a hill, blindly following the slope, is highly unsatisfactory. We'd like to have **a smarter ball**, a ball that has a notion of where it is going so that it knows to slow down before the hill slopes up again.

$$v_t=γv_{t−1}+η∇_θJ(θ−γv_{t−1})$$

$$θ=θ−v_t$$

Again, we set the momentum term $$γ$$ to a value of around $$0.9$$. While Momentum first computes the current gradient (small blue vector in Image 4) and then takes a big jump in the direction of the updated accumulated gradient (big blue vector), NAG first makes a big jump in the direction of the previous accumulated gradient (brown vector), measures the gradient and then makes a correction (red vector), which results in the complete NAG update (green vector). This anticipatory update prevents us from going too fast and results in increased responsiveness, which has significantly increased the performance of RNNs on a number of tasks.

![](https://www.jiqizhixin.com/data/upload/ueditor/20161121/58329b0dc9624.png)

Image 4: Nesterov update (Source: [G. Hinton's lecture 6c](http://www.cs.toronto.edu/~tijmen/csc321/slides/lecture_slides_lec6.pdf))

##### (3) Adagrad

Adagrad is an algorithm for gradient-based optimization that does just this: It adapts the learning rate to the parameters, **performing larger updates for infrequent and smaller updates for frequent parameters**. For this reason, it is well-suited for dealing with **sparse data**.

 Dean et al. have found that Adagrad greatly improved the robustness of SGD and used it for training large-scale neural nets at Google, which -- among other things -- learned to [recognize cats in Youtube videos](http://www.wired.com/2012/06/google-x-neural-network/). Moreover, Pennington et al. used Adagrad to train GloVe word embeddings, as **infrequent words require much larger updates than frequent ones**.

As Adagrad uses a different learning rate for every parameter $$θ_i$$ at every time step $$t$$, we first show Adagrad's per-parameter update, which we then vectorize. For brevity, we set $$g_{t,i}$$ to be the gradient of the objective function w.r.t. to the parameter $$θ_i$$ at time step $$t$$:

$$g_{t,i}=∇_θJ(θ_{t,i})$$

$$θ_{t+1,i}=θ_{t,i}−η⋅g_{t,i} \to θ_{t+1,i}=θ_{t,i}−\frac{η}{\sqrt{G_{t,ii}+ϵ}}⋅g_{t,i}$$

$$G_t∈ℝ^{d×d}$$ here is a diagonal matrix where each diagonal element i,ii,i is the sum of the squares of the gradients w.r.t. $$θ_i$$ up to time step $$t$$, while $$ϵ$$ is a smoothing term that avoids division by zero (usually on the order of $$1e^{−8}$$). Interestingly, without the square root operation, the algorithm performs much worse.

vectorize our implementation by performing an element-wise matrix-vector multiplication $$⊙$$ between $$G_t$$ and $$g_t$$: 

$$θ_t+1=θ_t−\frac{η}{\sqrt{G_t+ϵ}}⊙g_t$$

##### (4) Adadelta

Adadelta is an extension of Adagrad that seeks to **reduce** its aggressive, monotonically decreasing **learning rate**. Instead of accumulating all past squared gradients, Adadelta restricts the window of accumulated past gradients to **some fixed size** $$w$$.

With Adadelta, we **do not even need to set a default learning rate**, as it has been eliminated from the update rule.

##### (5) RMSprop

RMSprop is an unpublished, adaptive learning rate method proposed by Geoff Hinton in [Lecture 6e of his Coursera Class](http://www.cs.toronto.edu/~tijmen/csc321/slides/lecture_slides_lec6.pdf).

RMSprop and Adadelta have both been developed independently around the same time stemming from the need to resolve Adagrad's radically diminishing learning rates.

##### (6) Adam

Adaptive Moment Estimation (Adam) is another method that **computes adaptive learning rates for each parameter**. In addition to storing an exponentially decaying average of past squared gradients $$v_t$$ like Adadelta and RMSprop, Adam also keeps an exponentially decaying average of past gradients $$m_t$$, similar to momentum:

$$m_t=β_1m_{t−1}+(1−β_1)g_t$$

$$v_t=β_2v_{t−1}+(1−β_2)g^2_t$$

$$m̂_t=\frac{m_t}{1−β^t_1}$$

$$v̂_t=\frac{v_t}{1−β^t_2}$$

$$θ_{t+1}=θ_t−\frac{η}{\sqrt{v̂_t}+ϵ}m̂$$

They show empirically that Adam works well in practice and compares favorably to other adaptive learning-method algorithms.

#### Algorithms Comparison

* **Visualization**

![](http://ruder.io/content/images/2016/09/contours_evaluation_optimizers.gif)

We see their behaviour on the contours of a loss surface ([the Beale function](https://www.sfu.ca/~ssurjano/beale.html)) over time. Note that Adagrad, Adadelta, and RMSprop almost immediately head off in the right direction and converge similarly fast, while Momentum and NAG are led off-track, evoking the image of a ball rolling down the hill. NAG, however, is quickly able to correct its course due to its increased responsiveness by looking ahead and heads to the minimum.

* **Which optimizer to use?**

If your input data is **sparse**, then you likely achieve the best results using one of the **adaptive learning-rate methods**. An additional benefit is that you won't need to tune the learning rate but likely achieve the best results with the default value.

> So, which optimizer should you now use? If your input data is sparse, then you likely achieve the best results using one of the adaptive learning-rate methods. An additional benefit is that you won't need to tune the learning rate but likely achieve the best results with the default value.
>
> In summary, RMSprop is an extension of Adagrad that deals with its radically diminishing learning rates. It is identical to Adadelta, except that Adadelta uses the RMS of parameter updates in the numinator update rule. Adam, finally, adds bias-correction and momentum to RMSprop. Insofar, RMSprop, Adadelta, and Adam are very similar algorithms that do well in similar circumstances. Kingma et al. [[15](http://ruder.io/optimizing-gradient-descent/index.html#fn:15)] show that its bias-correction helps Adam slightly outperform RMSprop towards the end of optimization as gradients become sparser. Insofar, Adam might be the best overall choice.
>
> Interestingly, many recent papers use vanilla SGD without momentum and a simple learning rate annealing schedule. As has been shown, SGD usually achieves to find a minimum, but it might take significantly longer than with some of the optimizers, is much more reliant on a robust initialization and annealing schedule, and may get stuck in saddle points rather than local minima. Consequently, if you care about fast convergence and train a deep or complex neural network, you should choose one of the adaptive learning rate methods.

Note:

*Parallelizing and distributing SGD & Additional strategies for optimizing SGD can be seen in the first paper I list in the References section, which will not be included in my blog.*

### Exploding and Vanishing Gradients

##### (1) Why Gradients Explode or Vanish 



### RNN 

### References

1. [*Sebastian Ruder (2016). An overview of gradient descent optimisation algorithms. arXiv preprint arXiv:1609.04747.*](https://arxiv.org/abs/1609.04747)
2. ​

