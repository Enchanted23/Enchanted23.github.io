*In this blog, I will list some basic concepts and analysis of recurrent neural networks.*

## Activation Function

> To allow Neural Networks to learn complex decision boundaries, we apply a nonlinear activation function to some of its layers. Commonly used functions include [sigmoid](https://en.wikipedia.org/wiki/Sigmoid_function), [tanh](http://mathworld.wolfram.com/HyperbolicTangent.html), [ReLU (Rectified Linear Unit)](http://www.wildml.com/deep-learning-glossary/#relu) and variants of these.

### sigmoid

Often, *sigmoid function* refers to the special case of the [logistic function](https://en.wikipedia.org/wiki/Logistic_function) shown in the first figure and defined by the formula: 

$$S(x)={\frac {1}{1+e^{-x}}}={\frac {e^{x}}{e^{x}+1}}$$

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/Sigmoid-function.svg/500px-Sigmoid-function.svg.png)

**Definition:** A sigmoid function is a bounded differentiable real function that is defined for all real input values and has a non-negative derivative at each point.

**Properties:** In general, a sigmoid function is real-valued, monotonic, and differentiable having a non-negative first derivative which is bell shaped. A sigmoid function is constrained by a pair of horizontal asymptotes as $$x\rightarrow \pm \infty $$.

**Examples:**

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Gjl-t%28x%29.svg/640px-Gjl-t%28x%29.svg.png)

Some sigmoid functions compared. In the drawing all functions are normalized in such a way that their slope at the origin is 1.

### tanh

$$\tanh x={\frac {\sinh x}{\cosh x}}={\frac {e^{x}-e^{-x}}{e^{x}+e^{-x}}}$$

![](http://mathworld.wolfram.com/images/interactive/TanhReal.gif)

### ReLU

Short for **Rectified Linear Unit(s)**. ReLUs are often used as activation functions in Deep Neural Networks. They are defined by **f(x) = max(0, x)**. 

![](https://i.stack.imgur.com/1BX7l.png)

The **advantages** of ReLUs over functions like tanh include that they tend to be **sparse** (their activation easily be set to 0), and that they **suffer less from the vanishing gradient problem**. ReLUs are the most commonly used activation function in Convolutional Neural Networks. There exist several variations of ReLUs, such as Leaky ReLUs, Parametric ReLU (PReLU) or a smoother softplus approximation.

A smooth approximation to the rectifier is the analytic function $$f(x)=\log(1+\exp x)$$, which is called the softplus function. 

![](https://upload.wikimedia.org/wikipedia/en/thumb/6/6c/Rectifier_and_softplus_functions.svg/440px-Rectifier_and_softplus_functions.svg.png)

The derivative of softplus is $$f'(x)=\exp x/(1+\exp x)=1/(1+\exp(-x))$$, i.e. the logistic function.

## RNN Structure

The idea behind RNNs is to **make use of sequential information**. In a traditional neural network we assume that all inputs (and outputs) are independent of each other. But for many tasks that’s a very bad idea. If you want to predict the next word in a sentence you better know which words came before it. 

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/rnn.jpg)

The above diagram shows a **RNN being *unrolled* (or unfolded) into a full network**. By unrolling we simply mean that we write out the network for the complete sequence. For example, if the sequence we care about is a sentence of 5 words, the network would be unrolled into a 5-layer neural network, one layer for each word. The formulas that govern the computation happening in a RNN are as follows:

- $$x_t$$ is the input at time step $$t$$. For example, $$x_1$$ could be a one-hot vector corresponding to the second word of a sentence.
- $$s_t$$ is the hidden state at time step $$t$$. It’s the “memory” of the network.  $$s_t$$ is calculated based on the previous hidden state and the input at the current step: $$s_t=f(Ux_t + Ws_{t-1})$$. The function $$f$$ usually is a nonlinearity such as [tanh](https://reference.wolfram.com/language/ref/Tanh.html) or [ReLU](https://en.wikipedia.org/wiki/Rectifier_(neural_networks)).  $$s_{-1}$$, which is required to calculate the first hidden state, is typically initialized to all zeroes.
- $$o_t$$ is the output at step $$t$$. For example, if we wanted to predict the next word in a sentence it would be a vector of probabilities across our vocabulary. $$o_t = \mathrm{softmax}(Vs_t)$$.

*Unlike a traditional deep neural network, which uses different parameters at each layer, **a RNN shares the same parameters (U, V, W above) across all steps**. This reflects the fact that we are performing the same task at each step, just with different inputs. This greatly reduces the total number of parameters we need to learn.*

## BPTT 

Training a RNN is similar to training a traditional Neural Network. We also use the backpropagation algorithm, but with a little twist. Because the parameters are shared by all time steps in the network, the gradient at each output depends not only on the calculations of the current time step, but also the previous time steps. For example, in order to calculate the gradient at $$t=4$$ we would need to backpropagate 3 steps and sum up the gradients. This is called **Backpropagation Through Time (BPTT)**. Be aware of the fact that vanilla RNNs trained with BPTT[ have difficulties](http://www.jmlr.org/proceedings/papers/v28/pascanu13.pdf) learning long-term dependencies (e.g. dependencies between steps that are far apart) due to what is called the **vanishing/exploding gradient problem**. There exists some machinery to deal with these problems, and certain types of RNNs (like LSTMs) were specifically designed to get around them.

To fully understand this part of the tutorial I recommend being familiar with how partial differentiation and basic backpropagation works. If you are not, you can find excellent tutorials [here](http://cs231n.github.io/optimization-2/) and [here](http://colah.github.io/posts/2015-08-Backprop/) and [here](http://neuralnetworksanddeeplearning.com/chap2.html), in order of increasing difficulty.

$$s_t = \tanh(Ux_t + Ws_{t-1})$$

$$\hat{y}_t = \mathrm{softmax}(Vs_t)$$

We also defined our *loss*, or error, to be the cross entropy loss, given by:

$$E_t(y_t, \hat{y}_t) = - y_{t} \log \hat{y}_{t} $$

$$E(y, \hat{y}) =\sum\limits_{t} E_t(y_t,\hat{y}_t) = -\sum\limits_{t} y_{t} \log \hat{y}_{t}$$

Here, $$y_t$$ is the correct word at time step $$t$$, and $$\hat{y}_t$$ is our prediction. We typically treat the full sequence (sentence) as one training example, so the total error is just the sum of the errors at each time step (word).

Remember that our goal is to calculate the gradients of the error with respect to our parameters $$U, V, W$$ and then learn good parameters using Stochastic Gradient Descent. Just like we sum up the errors, we also sum up the gradients at each time step for one training example:

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/10/rnn-bptt1.png)

$$\frac{\partial E}{\partial W} = \sum\limits_{t} \frac{\partial E_t}{\partial W}$$

To calculate these gradients we use the chain rule of differentiation. I'll use $$E_3$$ as an example, just to have concrete numbers to work with.

$$s_3 = \tanh(Ux_3 + Ws_{2})$$

$$\begin{aligned} \hat{y}_3 &= \mathrm{softmax}(Vs_3) \\&=\mathrm{softmax}(z_3) \end{aligned}$$

$$\hat{y}_{3j} = \frac{e^{z_{3j}}}{\sum\limits_{k=1}^{m}e^{z_{3k}}}$$

$$E_3(y_3, \hat{y}_3) = - y_{3} \log \hat{y}_{3} $$

$$\begin{aligned}  \frac{\partial E_3}{\partial V} &=\frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial V}\\  &=\frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial z_3}\frac{\partial z_3}{\partial V}\\ &=(\hat{y}_3 - y_3) \otimes s_3 \\  \end{aligned}$$

In the above, $$z_3 =Vs_3$$, and $$\otimes$$ is the outer product of two vectors. The derivation rule of matrix can be found at [here](http://files.cnblogs.com/files/leoleo/matrix_rules.pdf). The point I’m trying to get across is that $$\frac{\partial E_3}{\partial V} $$ only depends on the values at the current time step, $$\hat{y}_3, y_3, s_3 $$. If you have these, calculating the gradient for $$V$$ a simple matrix multiplication.

But the story is different for $$\frac{\partial E_3}{\partial W}$$ (and for $$U$$. To see why, we write out the chain rule, just as above:

$$\begin{aligned}  \frac{\partial E_3}{\partial W} &= \frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial s_3}\frac{\partial s_3}{\partial W}\\  \end{aligned}$$

Now, note that $$s_3 = \tanh(Ux_t + Ws_2)$$ depends on $$s_2$$, which depends on $$W$$ and $$s_1$$, and so on. So if we take the derivative with respect to $$W$$ we can’t simply treat $$s_2$$ as a constant! We need to apply the chain rule again and what we really have is this:

$$\begin{aligned}  \frac{\partial E_3}{\partial W} &= \sum\limits_{k=0}^{3} \frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial s_3}\frac{\partial s_3}{\partial s_k}\frac{\partial s_k}{\partial W}\\  \end{aligned}$$

We sum up the contributions of each time step to the gradient. In other words, because $$W$$ is used in every step up to the output we care about, we need to backpropagate gradients from $$t=3$$ through the network all the way to $$t=0$$:

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/10/rnn-bptt-with-gradients.png)

Note that this is exactly the same as the standard backpropagation algorithm that we use in deep [Feedforward Neural Networks](http://www.wildml.com/2015/09/implementing-a-neural-network-from-scratch/). The key difference is that we sum up the gradients for $$W$$ at each time step. In a traditional NN we don’t share parameters across layers, so we don’t need to sum anything. But in my opinion BPTT is just a fancy name for standard backpropagation on an unrolled RNN. Just like with Backpropagation you could define a delta vector that you pass backwards, e.g.: $$\delta_2^{(3)} = \frac{\partial E_3}{\partial z_2} =\frac{\partial E_3}{\partial s_3}\frac{\partial s_3}{\partial s_2}\frac{\partial s_2}{\partial z_2}$$ with $$z_2 = Ux_2+ Ws_1$$. Then the same equations will apply.

## The Vanishing Gradient Problem

I mentioned that RNNs have difficulties learning long-range dependencies – interactions between words that are several steps apart. That’s problematic because the meaning of an English sentence is often determined by words that aren’t very close:“The man who wore a wig on his head went inside”. The sentence is really about a man going inside, not about the wig. But it’s unlikely that a plain RNN would be able capture such information. To understand why, let’s take a closer look at the gradient we calculated above:

$$\begin{aligned}  \frac{\partial E_3}{\partial W} &= \sum\limits_{k=0}^{3} \frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial s_3}\frac{\partial s_3}{\partial s_k}\frac{\partial s_k}{\partial W}\\  \end{aligned}$$

Note that $$\frac{\partial s_3}{\partial s_k}$$ is a chain rule in itself! For example, $$\frac{\partial s_3}{\partial s_1} =\frac{\partial s_3}{\partial s_2}\frac{\partial s_2}{\partial s_1}$$). Also note that because we are taking the derivative of a vector function with respect to a vector, the result is a matrix (called the [Jacobian matrix](https://en.wikipedia.org/wiki/Jacobian_matrix_and_determinant)) whose elements are all the pointwise derivatives. We can rewrite the above gradient:

$$\begin{aligned}  \frac{\partial E_3}{\partial W} &= \sum\limits_{k=0}^{3} \frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial s_3}  \left(\prod\limits_{j=k+1}^{3}  \frac{\partial s_j}{\partial s_{j-1}}\right)  \frac{\partial s_k}{\partial W}\\  \end{aligned}$$

It turns out (I won’t prove it here but [this paper](http://www.jmlr.org/proceedings/papers/v28/pascanu13.pdf) goes into detail) that the 2-norm, which you can think of it as an absolute value, of **the above Jacobian matrix has an upper bound of 1**. This makes intuitive sense because our $$tanh$$ (or sigmoid) activation function maps all values into a range between -1 and 1, and the derivative is bounded by 1: $$1-tanh^2(x)$$ (1/4 in the case of sigmoid: $$s(x)(1-s(x))$$) as well:

You can see that the $$tanh$$ and sigmoid functions have derivatives of 0 at both ends. They approach a flat line. **When this happens we say the corresponding neurons are saturated.** They have a zero gradient and drive other gradients in previous layers towards 0. Thus, with small values in the matrix and multiple matrix multiplications ($$t-k$$ in particular) the gradient values are shrinking exponentially fast, eventually vanishing completely after a few time steps. **Gradient contributions from “far away” steps become zero, and the state at those steps doesn’t contribute to what you are learning: You end up not learning long-range dependencies.** Vanishing gradients aren’t exclusive to RNNs. They also happen in deep Feedforward Neural Networks. It’s just that RNNs tend to be very deep (as deep as the sentence length in our case), which makes the problem a lot more common.

It is easy to imagine that, depending on our activation functions and network parameters, we could get exploding instead of vanishing gradients if the values of the Jacobian matrix are large. Indeed, that’s called the ***exploding gradient problem***. The reason that vanishing gradients have received more attention than exploding gradients is two-fold. For one, exploding gradients are obvious. **Your gradients will become NaN (not a number) and your program will crash**. Secondly, **clipping the gradients at a pre-defined threshold (as discussed in [this paper](http://www.jmlr.org/proceedings/papers/v28/pascanu13.pdf)) is a very simple and effective solution to exploding gradients.** Vanishing gradients are more problematic because it’s not obvious when they occur or how to deal with them.

More things about the difficulty of training recurrent neural networks can be found [here](the difficulty of training recurrent neural networks).

Fortunately, there are a few ways to combat the vanishing gradient problem. Proper initialization of the $$W$$ matrix can reduce the effect of vanishing gradients. So can regularization. **A more preferred solution is to use [ReLU](https://en.wikipedia.org/wiki/Rectifier_(neural_networks)) instead of $$tanh$$ or sigmoid activation functions.** The ReLU derivative is a constant of either 0 or 1, so it isn’t as likely to suffer from vanishing gradients. **An even more popular solution is to use Long Short-Term Memory (LSTM) or Gated Recurrent Unit (GRU) architectures**. LSTMs were [first proposed in 1997](http://deeplearning.cs.cmu.edu/pdfs/Hochreiter97_lstm.pdf) and are the perhaps most widely used models in NLP today. GRUs, [first proposed in 2014](http://arxiv.org/pdf/1406.1078v3.pdf), are simplified versions of LSTMs. Both of these RNN architectures were explicitly designed to deal with vanishing gradients and efficiently learn long-range dependencies. We’ll cover them in the next part of this tutorial.

## GRU/LSTM



## RNN Extensions

Over the years researchers have developed more sophisticated types of RNNs to deal with some of the shortcomings of the vanilla RNN model. 

**Bidirectional RNNs** are based on the idea that the output at time $$t$$ may not only depend on the previous elements in the sequence, but also future elements. For example, to predict a missing word in a sequence you want to look at both the left and the right context. Bidirectional RNNs are quite simple. They are just two RNNs stacked on top of each other. The output is then computed based on the hidden state of both RNNs.

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/bidirectional-rnn.png)

**Deep (Bidirectional) RNNs** are similar to Bidirectional RNNs, only that we now have multiple layers per time step. In practice this gives us a higher learning capacity (but we also need a lot of training data).

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/Screen-Shot-2015-09-16-at-2.21.51-PM.png)

**LSTM networks** are quite popular these days and we briefly talked about them above. LSTMs don’t have a fundamentally different architecture from RNNs, but they use a different function to compute the hidden state. The memory in LSTMs are called *cells* and you can think of them as black boxes that take as input the previous state $$h_{t-1}$$ and current input $$x_t$$. Internally these cells decide what to keep in (and what to erase from) memory. They then combine the previous state, the current memory, and the input. It turns out that these types of units are very efficient at capturing long-term dependencies. LSTMs can be quite confusing in the beginning but if you’re interested in learning more [this post has an excellent explanation](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

