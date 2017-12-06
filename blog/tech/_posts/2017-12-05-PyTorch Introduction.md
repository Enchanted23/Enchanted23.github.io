---
layout: blog_post
title: PyTorch Introduction
date: 2017-12-05 10:24:00 +0300
description: Learning PyTorch
img: pytorch-logo-dark.svg
tags: [PyTorch, Tensor computation, deep learning research platform, GPUs]
---

**This blog is a basic introduction of PyTorch, also my learning process of it.**

> **PyTorch is an optimized tensor library for deep learning using GPUs and CPUs.**
>
> Usually one uses PyTorch either as:
>
> - A replacement for numpy to use the power of GPUs.
> - a deep learning research platform that provides maximum flexibility and speed

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

## Let's begin

**My suggestion is that you can look at its whole documentation at first. Then you can look at its tutorial and find it much easier to understand.**

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
RuntimeError: The expanded size of the tensor (1) must match the existing size (7) at non-singleton dimension 2.
```

#### Backwards compatibility

Note that the introduction of broadcasting can cause backwards incompatible changes in the case where two tensors do not have the same shape, but are broadcastable and have the same number of elements. For Example:

```python
torch.add(torch.ones(4,1), torch.randn(4))
# would previously produce a Tensor with size: torch.Size([4,1]), but now produces a Tensor with size: torch.Size([4,4]).
```

In order to help identify cases in your code where backwards incompatibilities introduced by broadcasting may exist, you may set torch.utils.backcompat.broadcast_warning.enabled to True, which will generate a python warning in such cases.

```python
torch.utils.backcompat.broadcast_warning.enabled=True
torch.add(torch.ones(4,1), torch.ones(4))
__main__:1: UserWarning: self and other do not have the same shape, but are broadcastable, and have the same number of elements.
Changing behavior in a backwards incompatible manner to broadcasting rather than viewing as 1-dimensional.
```

**My training machine is still in application periods, so there will be just some simple introductions about GPU stuff. I will come back to write more words about tranning on GPU after my GPU is ready.**

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

