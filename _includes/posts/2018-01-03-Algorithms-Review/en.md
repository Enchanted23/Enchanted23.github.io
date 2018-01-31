> In this blog, I will review basic algorithms I had learned and expolre some questions on *LeetCode*, preparing for some interviews and further graduate study.

Firstly, I will write some important concepts and facts I learned from the course *Introduction to Algorithms*. Then I will look at some problems on *LeetCode*.

## Introduction to Algorithms [Third Edition]

### Getting Started

#### Insertion sort

![](https://i.stack.imgur.com/HmuHD.png)

* **loop invariant**: At the start of each iteration of the for loop of lines 1–8, the subarray $$A[1…j-1]$$ consists of the elements originally in $$A[1…j-1]$$, but in sorted order.

* $$T(n) : \Theta (n^2)$$

* $$S(n): O(1)$$

* **stability**: stable

* **illustration**:

  ![](http://www.java2novice.com/images/insertion-sort.png)

We use **loop invariants** to help us understand why an algorithm is **correct**. We must show three things about a loop invariant:

1. Initialization
2. Maintenance
3. Termination

#### Designing algorithms

**The divide-and-conquer approach**: merge sort

##### Merge sort

![](http://d2vlcm61l7u1fs.cloudfront.net/media%2F6bf%2F6bfd8c4b-6189-4a01-870e-1c6840ac81eb%2Fphp6TBlBK.png)

- **loop invariant**: At the start of each iteration of the for loop of lines 12–18, the subarray $$A[p…k-1]$$ contains the $$k-p$$ smallest elements of $$L[1…n_1+1]$$ and $$R[1…n_2+1]$$, in sorted order. Moreover, $$L[i]$$ and $$R[j]$$ are the smallest elements of their arrays that have not been copied back into A.

- $$T(n) : \Theta (n\,lgn)$$

- $$S(n): O(n)$$

- **stability**: stable

- **illustration**:

  ![](https://qph.ec.quoracdn.net/main-qimg-4a91d2f279f746539ef1a17e6abae79c)



### Growth of Functions

#### Asymptotic notation

* $$\Theta$$ - notation

  $$\exists\,C$$ and $$n_0$$ when $$n \geq n_0$$:

  $$c_1g(n) \leq f(n) \leq c_2g(n),\;f(n) = \Theta(g(n))$$

* $$O$$ - notation

  $$\exists\,C$$ and $$n_0$$ when $$n \geq n_0$$:

  $$0 \leq f(n) \leq cg(n),\;f(n) = O(g(n))$$

* $$\Omega$$ - notation

  $$\exists\,C$$ and $$n_0$$ when $$n \geq n_0$$:

  $$0 \leq cg(n) \leq f(n),\; f(n) = \Omega(g(n))$$

* $$o$$ - notation

  $$\forall\,C$$, $$\exists\,n_0$$ when $$n \geq n_0$$:

  $$0 \leq f(n) \leq cg(n),\; f(n) = o(g(n))$$

* $$\omega$$ - notation

  $$\forall\,C$$, $$\exists\,n_0$$ when $$n \geq n_0$$:

  $$0 \leq cg(n) \leq f(n),\; f(n) = \omega(g(n))$$

#### Comparing functions

* Transitivity

  $$f(n)=O(g(n)),\;g(n)=O(h(n))\implies f(n)=O(h(n))$$

  *apply to all*

* Reflexivity

  $$f(n)=\Theta (f(n))$$

  $$f(n)=O(f(n))$$

  $$f(n)=\Omega (f(n))$$

* Symmetry

  $$f(n) = \Theta(g(n)) \iff g(n) = \Theta(f(n))$$

* Transpose symmetry

  $$f(n) = O(g(n)) \iff g(n) = \Omega(f(n))$$

  $$f(n) = o(g(n)) \iff g(n) = \omega(f(n))$$

#### Common functions

* **Floors and ceilings**

  $$\forall R:\; x-1 < \lfloor x \rfloor \leq x \leq \lceil x \rceil < x+1 $$

  $$\forall Z:\; \lceil \frac{n}{2} \rceil + \lfloor \frac{n}{2} \rfloor = n$$

  $$\forall n\geq 0 ,\; a,b>0$$

  $$\begin{cases} \left \lceil \frac{\lceil n/a \rceil}{b} \right \rceil = \lceil \frac{n}{ab} \rceil \; \left \lfloor \frac{\lfloor n/a \rfloor}{b} \right \rfloor = \lfloor \frac{n}{ab} \rfloor \\  \lceil \frac{a}{b} \rceil \leq \frac{a+(b-1)}{b} \\ \lfloor \frac{a}{b} \rfloor \geq \frac{a-(b-1)}{b} \end{cases}$$

* **Modular arithmetic**

  $$a\;mod \; n = a - n \lfloor a/n \rfloor$$

  $$0 \leq a \; mod\,n \; <n$$

  $$a\equiv b\;(mod\,n)$$

* **Exponentials**

  $$n^b=o(a^n)$$

  $$e^x=1+x+\Theta(x^2)$$

  $$\underset{n\to \infty}{lim}(1+\frac{x}{n})^n=e^x$$

* **Logarithms**

  *default in this book*：

  $$log\,n = lg\,n = log_2\,n$$

  $$loglog\,n=log^{(2)}n$$

  $$log^kn=(log\,n)^k$$

* **Factorials**

  **Stirling’s approximation:** 

  $$n!=\sqrt{2\pi n} \left(  \frac{n}{e}\right)^n [1+\Theta(\frac{1}{n})]$$

  $$\begin{cases} n!=o(n^n) \\ n!=\omega(2^n) \\ lg(n!)=\Theta(n\,log\,n) \end{cases}$$

* **Functional iteration**

  $$log^*n=min\{i\geq0: lg^{(i)}n\leq1 \}$$

* **Fibonacci numbers**

#### T(n) - Recursion form

eg. 

$$T(n) = \begin{cases} \Theta(1) & \mbox{if }n=1 \\ 2T(n/2)+\Theta(n) & \mbox{if }n>1 \end{cases}$$

$$\implies T(n)=\Theta(n\,lg\,n)$$

##### Substitution Method

1. guess a solution $$G(n)$$
2. replace $$T(n)$$ with $$G(n)$$ to prove the correctness

we usually select $$c>T(1)+1$$

**some tricks:**

1. we can prove a **stronger inequation**.

   eg. we can prove $$c_1G(\frac{n+1}{2})<T(n)<c_2G(\frac{n-1}{2})$$ instead.

2. pay attention to that sometimes we need to **prove explicitly**.

3. we can **change the variable** to what we are familiar with. 

   eg. $$log(n) = t,\;T(n)=T'(e^t)$$	*(keep the monotony)*

##### Recursion Tree Method

$$\sum \mbox{row sum}$$

![](https://www.cs.cornell.edu/courses/cs3110/2012sp/lectures/lec20-master/images/lec19-diagram2.png)

*we often use **Recursion Tree Method** to get $$G(n)$$, then use **Substitution Method** to prove it.*

##### Master Method

$$T(n)=aT(n/b)+f(n)\quad a\geq1,b>1$$ 

*you can replace $$n/b$$ with $$\lfloor n/b \rfloor$$ or $$\lceil n/b \rceil$$.*

1. if $$f(n)=O(n^{(log_ba)-\varepsilon}),\quad \varepsilon>0$$:

   $$T(n)=\Theta(n^{log_ba})$$

2. if $$f(n)=\Theta(n^{log_ba}\cdot log^kn)$$:

   $$T(n)=\Theta(n^{log_ba}\cdot log^{k+1}n)$$

3. if $$f(n)=\Omega(n^{(log_ba)+\varepsilon}),\quad \varepsilon>0$$

   and $$af(n/b) \leq cf(n),\;c<1$$ when $$n$$ is large enough:

   $$T(n)=\Theta(f(n))$$



### Sorting and Order Statistics

#### Selection sort

![](https://image.slidesharecdn.com/insertionsortbubblesortselectionsort-170516055539/95/insertion-sort-bubble-sort-selection-sort-31-638.jpg?cb=1494914307)

- **loop invariant**: At the start of each iteration of the for loop of lines 1–8, the subarray $$A[1…j-1]$$ consists of the elements originally in $$A[1…j-1]$$, but in sorted order.
- $$T(n) : \Theta (n^2)$$
- $$S(n): O(1)$$
- **stability**: stable
- **illustration**:



#### Bubble sort



