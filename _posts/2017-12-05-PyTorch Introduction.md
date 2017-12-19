---
layout:     post
title:      "PyTorch Introduction"
subtitle:   " \"Let's explore PyTorch!\""
date:       2017-12-05 10:24:00
author:     "Zhan"
header-img: "img/pytorch-logo.jpg"
header-mask:  0.3
catalog: true
tags:
	- PyTorch
	- Deep Learning

---

**1** [**Before Learning**](#section1) 

**2** [**Let's begin**](#section2)

​	**2.1** [**Autograd mechanics**](#section2.1)

​	**2.2 [Broadcasting semantics](#section2.2)**

​	**2.3 [CUDA semantics](#section2.3)**

​	**2.4 [Extending PyTorch](#section2.4)**

​	**2.5 [Multiprocessing](#section2.5)**



**This blog is a basic introduction of PyTorch, also my learning process of it.**

> **PyTorch is an optimized tensor library for deep learning using GPUs and CPUs.**
>
> Usually one uses PyTorch either as:
>
> - A replacement for numpy to use the power of GPUs.
> - a deep learning research platform that provides maximum flexibility and speed

<p id = "section1"></p>
---

## Before Learning

While learning something new, it is always good to use Jupyter Notebook to write something simple to understand the simple ideas. As it was done in deeplearning.ai.

In consideration of the fact that starting an ipython / jupyter notebook inside of a specific virtualenv is not convenient enough, I suggest you to install your PyTorch directly to python3. However, you can still start your jupyter notebook inside of a specific virtualenv after doing the following work.

1. Install the ipython kernel module into your virtualenv.

   ```shell
   source my-virtualenv-name/bin/activate  # activate your virtualenv
   pip3 install ipykernel
   ```

2. Now run the kernel "self-install" script:

   ```shell
   python3 -m ipykernel install --user --name=my-virtualenv-name
   ```

   Replacing the `--name` parameter as appropriate.

Then, you can run`jupyter notebook` , you will see your kernel in the Jupyter Notebook menu and be able so switch to it.

If you encounter some`import torch`problems , you can try to update your python3 to the latest version. As for me, I update my Python 3.6.0 to 3.6.1, then recreate my virtualenv with `virtualenv my-virtualenv-name -p 'which python3'` .

you can save your environment setting by running:

```shell
 pip3 freeze > requirements.txt
 rm -rf my-virtualenv-name
 virtualenv my-new-virtualenv-name -p 'which python3'
 source my-new-virtualenv-name/bin/activate
 pip3 install -r requirements.txt
```

However, it may run into some problem. So I suggest you to just reinstall all your enviroment, especially your PyTorch.

<p id = "section2"></p>
---

## Let's begin

**My suggestion is that you can look at its whole documentation at first. Then you can look at its tutorial and find it much easier to understand.**

<p id = "section2.1"></p>
---

### Autograd mechanics

Every Variable has two flags: `requires_grad` and `volatile` , which help to exclude subgraphs where backward computation is never performed.

If there’s a single input to an operation that requires gradient, its output will also require gradient. 

```python
z = Variable(torch.randn(5, 5), requires_grad=True)
```

If there’s even a single volatile input to an operation, its output is also going to be volatile.

```python
volatile_input = Variable(torch.randn(1, 3, 227, 227), volatile=True)
```

When computing the forwards pass, autograd simultaneously performs the requested computations and builds up a graph representing the function that computes the gradient (the `.grad_fn` attribute of each `Variable` is an entry point into this graph). When the forwards pass is completed, we evaluate this graph in the backwards pass to compute the gradients.

**An important thing to note is that the graph is recreated from scratch at every iteration.**

> Every variable keeps a version counter, that is incremented every time it’s marked dirty in any operation. When a Function saves any tensors for backward, a version counter of their containing Variable is saved as well. Once you access `self.saved_tensors` it is checked, and if it’s greater than the saved value an error is raised.

<p id = "section2.2"></p>
---

### Broadcasting semantics

#### General semantics

Two tensors are “broadcastable” if the following rules hold:

- Each tensor has at least one dimension.
- When iterating over the dimension sizes, starting at the trailing dimension, the dimension sizes must either be equal, one of them is 1, or one of them does not exist.

```python
x=torch.FloatTensor(5,7,3)
y=torch.FloatTensor(5,7,3)
# same shapes are always broadcastable (i.e. the above rules always hold)

x=torch.FloatTensor()
y=torch.FloatTensor(2,2)
# x and y are not broadcastable, because x does not have at least 1 dimension

# can line up trailing dimensions
x=torch.FloatTensor(5,3,4,1)
y=torch.FloatTensor(  3,1,1)
# x and y are broadcastable.
# 1st trailing dimension: both have size 1
# 2nd trailing dimension: y has size 1
# 3rd trailing dimension: x size == y size
# 4th trailing dimension: y dimension doesn't exist

# but:
x=torch.FloatTensor(5,2,4,1)
y=torch.FloatTensor(  3,1,1)
# x and y are not broadcastable, because in the 3rd trailing dimension 2 != 3
```

If two tensors `x`, `y` are “broadcastable”, the resulting tensor size is calculated as follows:

- If the number of dimensions of `x` and `y` are not equal, prepend 1 to the dimensions of the tensor with fewer dimensions to make them equal length.
- Then, for each dimension size, the resulting dimension size is the max of the sizes of `x` and `y`along that dimension.

```python
x = Variable(torch.ones(5,1))
y = Variable(torch.ones(  3))
print(x+y)
Variable containing:
 2  2  2
 2  2  2
 2  2  2
 2  2  2
 2  2  2
[torch.FloatTensor of size 5x3]
```

#### In-place semantics

One complication is that in-place operations do not allow the in-place tensor to change shape as a result of the broadcast.

```python
x=torch.FloatTensor(5,3,4,1)
y=torch.FloatTensor(3,1,1)
>>> (x.add_(y)).size()
torch.Size([5, 3, 4, 1])

# but:
x=torch.FloatTensor(1,3,1)
y=torch.FloatTensor(3,1,7)
(x.add_(y)).size()
RuntimeError: The expanded size of the tensor (1) must match the 
existing size (7) at non-singleton dimension 2.
```

#### Backwards compatibility

Note that the introduction of broadcasting can cause backwards incompatible changes in the case where two tensors do not have the same shape, but are broadcastable and have the same number of elements. For Example:

```python
torch.add(torch.ones(4,1), torch.randn(4))
# would previously produce a Tensor with size: torch.Size([4,1]), 
# but now produces a Tensor with size: torch.Size([4,4]).
```

In order to help identify cases in your code where backwards incompatibilities introduced by broadcasting may exist, you may set torch.utils.backcompat.broadcast_warning.enabled to True, which will generate a python warning in such cases.

```python
torch.utils.backcompat.broadcast_warning.enabled=True
torch.add(torch.ones(4,1), torch.ones(4))
__main__:1: UserWarning: self and other do not have the same shape, 
but are broadcastable, and have the same number of elements.
Changing behavior in a backwards incompatible manner to broadcasting 
rather than viewing as 1-dimensional.
```

**My training machine is still in application periods, so there will be just some simple introductions about GPU stuff. I will come back to write more words about tranning on GPU after my GPU is ready.**

<p id = "section2.3"></p>
---

### CUDA semantics

[`torch.cuda`](http://pytorch.org/docs/0.3.0/cuda.html#module-torch.cuda) is used to set up and run CUDA operations. It keeps track of the currently selected GPU, and all CUDA tensors you allocate will by default be created on that device. The selected device can be changed with a [`torch.cuda.device`](http://pytorch.org/docs/0.3.0/cuda.html#torch.cuda.device) context manager.

```python
x = torch.cuda.FloatTensor(1)
# x.get_device() == 0
y = torch.FloatTensor(1).cuda()
# y.get_device() == 0

with torch.cuda.device(1):
    # allocates a tensor on GPU 1
    a = torch.cuda.FloatTensor(1)

    # transfers a tensor from CPU to GPU 1
    b = torch.FloatTensor(1).cuda()
    # a.get_device() == b.get_device() == 1

    c = a + b
    # c.get_device() == 1

    z = x + y
    # z.get_device() == 0

    # even within a context, you can give a GPU id to the .cuda call
    d = torch.randn(2).cuda(2)
    # d.get_device() == 2
```

Calling [`empty_cache()`](http://pytorch.org/docs/0.3.0/cuda.html#torch.cuda.empty_cache) can release all unused cached memory from PyTorch so that those can be used by other GPU applications.

#### Device-agnostic code

> Due to the structure of PyTorch, you may need to explicitly write device-agnostic (CPU or GPU) code; an example may be creating a new tensor as the initial hidden state of a recurrent neural network.

The first step is to determine whether the GPU should be used or not. A common pattern is to use Python’s `argparse` module to read in user arguments, and have a flag that can be used to disable CUDA, in combination with [`is_available()`](http://pytorch.org/docs/0.3.0/cuda.html#torch.cuda.is_available). In the following, `args.cuda` results in a flag that can be used to cast tensors and modules to CUDA if desired:

```python
import argparse
import torch

parser = argparse.ArgumentParser(description='PyTorch Example')
parser.add_argument('--disable-cuda', action='store_true',
                    help='Disable CUDA')
args = parser.parse_args()
args.cuda = not args.disable_cuda and torch.cuda.is_available()
```

If modules or tensors need to be sent to the GPU, `args.cuda` can be used as follows:

```Python
x = torch.Tensor(8, 42)
net = Network()
if args.cuda:
  x = x.cuda()
  net.cuda()
```

When creating tensors, an alternative to the if statement is to have a default datatype defined, and cast all tensors using that. An example when using a dataloader would be as follows:

```
dtype = torch.cuda.FloatTensor
for i, x in enumerate(train_loader):
    x = Variable(x.type(dtype))
```

#### Use nn.DataParallel instead of multiprocessing

Most use cases involving batched inputs and multiple GPUs should default to using [`DataParallel`](http://pytorch.org/docs/0.3.0/nn.html#torch.nn.DataParallel) to utilize more than one GPU. Even with the GIL, a single Python process can saturate multiple GPUs.

As of version 0.1.9, large numbers of GPUs (8+) might not be fully utilized. However, this is a known issue that is under active development. As always, test your use case.

<p id = "section2.5"></p>
---

### Extending PyTorch

#### Extending [`torch.autograd`](http://pytorch.org/docs/0.3.0/autograd.html#module-torch.autograd)

Adding operations to [`autograd`](http://pytorch.org/docs/0.3.0/autograd.html#module-torch.autograd) requires implementing a new [`Function`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function) subclass for each operation. Recall that [`Function`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function) s are what [`autograd`](http://pytorch.org/docs/0.3.0/autograd.html#module-torch.autograd) uses to compute the results and gradients, and encode the operation history. Every new function requires you to implement 2 methods:

- [`forward()`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function.forward) - the code that performs the operation. It can take as many arguments as you want, with some of them being optional, if you specify the default values. All kinds of Python objects are accepted here. [`Variable`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Variable) arguments will be converted to `Tensor` s before the call, and their use will be registered in the graph. Note that this logic won’t traverse lists/dicts/any other data structures and will only consider Variables that are direct arguments to the call. You can return either a single `Tensor` output, or a `tuple` of `Tensor` s if there are multiple outputs. Also, please refer to the docs of [`Function`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function) to find descriptions of useful methods that can be called only from [`forward()`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function.forward).
- [`backward()`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function.backward) - gradient formula. It will be given as many [`Variable`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Variable) arguments as there were outputs, with each of them representing gradient w.r.t. that output. It should return as many[`Variable`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Variable) s as there were inputs, with each of them containing the gradient w.r.t. its corresponding input. If your inputs didn’t require gradient (see `needs_input_grad`), or were non-[`Variable`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Variable) objects, you can return `None`. Also, if you have optional arguments to `forward()`you can return more gradients than there were inputs, as long as they’re all [`None`](https://docs.python.org/2/library/constants.html#None).

Below you can find code for a `Linear` function from [`torch.nn`](http://pytorch.org/docs/0.3.0/nn.html#module-torch.nn), with additional comments:

```python
# Inherit from Function
class LinearFunction(Function):

    # Note that both forward and backward are @staticmethods
    @staticmethod
    # bias is an optional argument
    def forward(ctx, input, weight, bias=None):
        ctx.save_for_backward(input, weight, bias)
        # .mm -> multiply ; .t -> trans
        output = input.mm(weight.t())
        if bias is not None:
          	# expand the bias
            output += bias.unsqueeze(0).expand_as(output)
        return output

    # This function has only a single output, so it gets only one gradient
    @staticmethod
    def backward(ctx, grad_output):
        # This is a pattern that is very convenient - at the top of backward
        # unpack saved_tensors and initialize all gradients w.r.t. inputs to
        # None. Thanks to the fact that additional trailing Nones are
        # ignored, the return statement is simple even when the function has
        # optional inputs.
        input, weight, bias = ctx.saved_variables
        grad_input = grad_weight = grad_bias = None

        # These needs_input_grad checks are optional and there only to
        # improve efficiency. If you want to make your code simpler, you can
        # skip them. Returning gradients for inputs that don't require it is
        # not an error.
        if ctx.needs_input_grad[0]:
            grad_input = grad_output.mm(weight)
        if ctx.needs_input_grad[1]:
            grad_weight = grad_output.t().mm(input)
        if bias is not None and ctx.needs_input_grad[2]:
            grad_bias = grad_output.sum(0).squeeze(0)

        return grad_input, grad_weight, grad_bias
```

#### Extending [`torch.nn`](http://pytorch.org/docs/0.3.0/nn.html#module-torch.nn)

##### Adding a [`Module`](http://pytorch.org/docs/0.3.0/nn.html#torch.nn.Module)

Since [`nn`](http://pytorch.org/docs/0.3.0/nn.html#module-torch.nn) heavily utilizes [`autograd`](http://pytorch.org/docs/0.3.0/autograd.html#module-torch.autograd), adding a new [`Module`](http://pytorch.org/docs/0.3.0/nn.html#torch.nn.Module) requires implementing a [`Function`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function) that performs the operation and can compute the gradient. From now on let’s assume that we want to implement a `Linear` module and we have the function implementated as in the listing above. There’s very little code required to add this. Now, there are two functions that need to be implemented:

- `__init__` (*optional*) - takes in arguments such as kernel sizes, numbers of features, etc. and initializes parameters and buffers.
  - [`forward()`](http://pytorch.org/docs/0.3.0/nn.html#torch.nn.Module.forward) - instantiates a [`Function`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Function) and uses it to perform the operation. It’s very similar to a functional wrapper shown above.

This is how a `Linear` module can be implemented:

```python
class Linear(nn.Module):
    def __init__(self, input_features, output_features, bias=True):
        super(Linear, self).__init__()
        self.input_features = input_features
        self.output_features = output_features

        # nn.Parameter is a special kind of Variable, that will get
        # automatically registered as Module's parameter once it's assigned
        # as an attribute. Parameters and buffers need to be registered, or
        # they won't appear in .parameters() (doesn't apply to buffers), and
        # won't be converted when e.g. .cuda() is called. You can use
        # .register_buffer() to register buffers.
        # nn.Parameters can never be volatile and, different than Variables,
        # they require gradients by default.
        self.weight = nn.Parameter(torch.Tensor(output_features, input_features))
        if bias:
            self.bias = nn.Parameter(torch.Tensor(output_features))
        else:
            # You should always register all possible parameters, but the
            # optional ones can be None if you want.
            self.register_parameter('bias', None)

        # Not a very smart way to initialize weights
        self.weight.data.uniform_(-0.1, 0.1)
        if bias is not None:
            self.bias.data.uniform_(-0.1, 0.1)

    def forward(self, input):
        # See the autograd section for explanation of what happens here.
        return LinearFunction.apply(input, self.weight, self.bias)
```

<p id = "section2.5"></p>
---

### Multiprocessing best practices

[`torch.multiprocessing`](http://pytorch.org/docs/0.3.0/multiprocessing.html#module-torch.multiprocessing) is a drop in replacement for Python’s [`multiprocessing`](https://docs.python.org/2/library/multiprocessing.html#module-multiprocessing) module. It supports the exact same operations, but extends it, so that all tensors sent through a [`multiprocessing.Queue`](https://docs.python.org/2/library/multiprocessing.html#multiprocessing.Queue), will have their data moved into shared memory and will only send a handle to another process.

> When a [`Variable`](http://pytorch.org/docs/0.3.0/autograd.html#torch.autograd.Variable) is sent to another process, both the `Variable.data` and `Variable.grad.data` are going to be shared.

This allows to implement various training methods, like Hogwild, A3C, or any others that require asynchronous operation.



*Updating*



### Autograd: automatic differentiation

The `autograd` package provides automatic differentiation for all operations on Tensors.

`autograd.Variable` wraps a Tensor, and supports nearly all of operations defined on it. Once you finish your computation you can call `.backward()` and have all the gradients computed automatically.

`Variable` and `Function` are interconnected and build up an acyclic graph, that encodes a complete history of computation. Each variable has a `.grad_fn` attribute that references a `Function` that has created the `Variable` (except for Variables created by the user - their `grad_fn is None`).

If you want to compute the derivatives, you can call `.backward()` on a `Variable`. If `Variable` is a scalar (i.e. it holds a one element data), you don’t need to specify any arguments to `backward()`, however if it has more elements, you need to specify a `grad_output` argument that is a tensor of matching shape.

### Neural Networks

Neural networks can be constructed using the `torch.nn` package.

A typical training procedure for a neural network is as follows:

- Define the neural network that has some learnable parameters (or weights)
- Iterate over a dataset of inputs
- Process input through the network
- Compute the loss (how far is the output from being correct)
- Propagate gradients back into the network’s parameters
- Update the weights of the network, typically using a simple update rule:`weight = weight - learning_rate * gradient`

