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

   ```
   source my-virtualenv-name/bin/activate  # activate your virtualenv
   pip3 install ipykernel
   ```

2. Now run the kernel "self-install" script:

   ```
   python3 -m ipykernel install --user --name=my-virtualenv-name
   ```

   Replacing the `--name` parameter as appropriate.

Then, you can run`jupyter notebook` , you will see your kernel in the Jupyter Notebook menu and be able so switch to it.

If you encounter some`import torch`problems , you can try to update your python3 to the latest version. As for me, I update my Python 3.6.0 to 3.6.1, then recreate my virtualenv with `virtualenv my-virtualenv-name -p 'which python3'` .

you can save your environment setting by running:

```
 pip3 freeze > requirements.txt
 rm -rf my-virtualenv-name
 virtualenv my-new-virtualenv-name -p 'which python3'
 source my-new-virtualenv-name/bin/activate
 pip3 install -r requirements.txt
```

However, it may run into some problem. So I suggest you to just reinstall all your enviroment, especially your PyTorch.

## Let's begin our learning

