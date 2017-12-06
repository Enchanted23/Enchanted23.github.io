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

Every Variable has two flags:`requires_grad` and  `volatile` , which help to exclude subgraphs where backward computation is never performed.

If there’s a single input to an operation that requires gradient, its output will also require gradient. 

```
z = Variable(torch.randn(5, 5), requires_grad=True)
```

If there’s even a single volatile input to an operation, its output is also going to be volatile.

```
volatile_input = Variable(torch.randn(1, 3, 227, 227), volatile=True)
```

When computing the forwards pass, autograd simultaneously performs the requested computations and builds up a graph representing the function that computes the gradient (the `.grad_fn` attribute of each `Variable` is an entry point into this graph). When the forwards pass is completed, we evaluate this graph in the backwards pass to compute the gradients.

**An important thing to note is that the graph is recreated from scratch at every iteration.**

> Every variable keeps a version counter, that is incremented every time it’s marked dirty in any operation. When a Function saves any tensors for backward, a version counter of their containing Variable is saved as well. Once you access `self.saved_tensors` it is checked, and if it’s greater than the saved value an error is raised.

### Broadcasting semantics

Two tensors are “broadcastable” if the following rules hold:

- Each tensor has at least one dimension.
- When iterating over the dimension sizes, starting at the trailing dimension, the dimension sizes must either be equal, one of them is 1, or one of them does not exist.

```Python
>>> x=torch.FloatTensor(5,7,3)
>>> y=torch.FloatTensor(5,7,3)
# same shapes are always broadcastable (i.e. the above rules always hold)

>>> x=torch.FloatTensor()
>>> y=torch.FloatTensor(2,2)
# x and y are not broadcastable, because x does not have at least 1 dimension

# can line up trailing dimensions
>>> x=torch.FloatTensor(5,3,4,1)
>>> y=torch.FloatTensor(  3,1,1)
# x and y are broadcastable.
# 1st trailing dimension: both have size 1
# 2nd trailing dimension: y has size 1
# 3rd trailing dimension: x size == y size
# 4th trailing dimension: y dimension doesn't exist

# but:
>>> x=torch.FloatTensor(5,2,4,1)
>>> y=torch.FloatTensor(  3,1,1)
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

