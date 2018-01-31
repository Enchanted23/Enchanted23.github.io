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

