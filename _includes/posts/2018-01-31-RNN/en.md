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

$$s_t = \tanh(Ux_t + Ws_{t-1})$$

$$\hat{y}_t = \mathrm{softmax}(Vs_t)$$

$$E_t(y_t, \hat{y}_t) = - y_{t} \log \hat{y}_{t} $$

$$\begin{aligned}  \frac{\partial E_3}{\partial V} &=\frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial V}\\  &=\frac{\partial E_3}{\partial \hat{y}_3}\frac{\partial\hat{y}_3}{\partial z_3}\frac{\partial z_3}{\partial V}\\  &=(\hat{y}_3 - y_3) \otimes s_3 \\  \end{aligned}$$



## RNN Extensions

Over the years researchers have developed more sophisticated types of RNNs to deal with some of the shortcomings of the vanilla RNN model. 

**Bidirectional RNNs** are based on the idea that the output at time $$t$$ may not only depend on the previous elements in the sequence, but also future elements. For example, to predict a missing word in a sequence you want to look at both the left and the right context. Bidirectional RNNs are quite simple. They are just two RNNs stacked on top of each other. The output is then computed based on the hidden state of both RNNs.

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/bidirectional-rnn.png)

**Deep (Bidirectional) RNNs** are similar to Bidirectional RNNs, only that we now have multiple layers per time step. In practice this gives us a higher learning capacity (but we also need a lot of training data).

![](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/Screen-Shot-2015-09-16-at-2.21.51-PM.png)

**LSTM networks** are quite popular these days and we briefly talked about them above. LSTMs don’t have a fundamentally different architecture from RNNs, but they use a different function to compute the hidden state. The memory in LSTMs are called *cells* and you can think of them as black boxes that take as input the previous state $$h_{t-1}$$ and current input $$x_t$$. Internally these cells decide what to keep in (and what to erase from) memory. They then combine the previous state, the current memory, and the input. It turns out that these types of units are very efficient at capturing long-term dependencies. LSTMs can be quite confusing in the beginning but if you’re interested in learning more [this post has an excellent explanation](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

