> In this blog, I will review basic algorithms I had learned and expolre some questions on *LeetCode*, preparing for some interviews and further graduate study.
>
> Firstly, I will write some important concepts and facts I learned from the course *Introduction to Algorithms*. Then I will look at some problems on *LeetCode*.

[TOC]

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

##### - Substitution Method

1. guess a solution $$G(n)$$
2. replace $$T(n)$$ with $$G(n)$$ to prove the correctness

we usually select $$c>T(1)+1$$

**some tricks:**

1. we can prove a **stronger inequation**.

   eg. we can prove $$c_1G(\frac{n+1}{2})<T(n)<c_2G(\frac{n-1}{2})$$ instead.

2. pay attention to that sometimes we need to **prove explicitly**.

3. we can **change the variable** to what we are familiar with. 

   eg. $$log(n) = t,\;T(n)=T'(e^t)$$	*(keep the monotony)*

##### - Recursion Tree Method

$$\sum \mbox{row sum}$$

![](https://www.cs.cornell.edu/courses/cs3110/2012sp/lectures/lec20-master/images/lec19-diagram2.png)

*we often use **Recursion Tree Method** to get $$G(n)$$, then use **Substitution Method** to prove it.*

##### - Master Method

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

![](https://lh3.googleusercontent.com/-bsSMvXPukXo/VJ_6LdoaufI/AAAAAAAAC1Q/xOoEpiK-n4I/w955-h499-no/selectionSor2.png)

- **loop invariant**: At the start of each iteration of the for loop, the subarray $$A[1…j]$$ consists of the elements originally in $$A[1…j]$$, but in sorted order.

- $$T(n) : \Theta (n^2)$$

- $$S(n): O(1)$$

- **stability**: unstable

- **illustration**:

  ![](https://lh3.googleusercontent.com/-F4ZeTNi1W_I/VJ_6K20wVEI/AAAAAAAAC1M/bvwhPX63MCg/w894-h316-no/selectionSort1.png)



#### Bubble sort

![](https://lh4.googleusercontent.com/-IoTOqR5baXg/VJ_1sqHt1TI/AAAAAAAAC00/05jfBWtyphA/w776-h383-no/bubbleSort2.png)

- **loop invariant**: At the start of each iteration of the for loop, the subarray $$A[1…i]$$ consists of the elements originally in $$A[1…i]$$, but in sorted order.

- $$T(n) : \Theta (n^2)$$

- $$S(n): O(1)$$

- **stability**: stable

- **illustration**:

  ![](https://lh4.googleusercontent.com/-oG6PwAsInf0/VJ_1s1dFhBI/AAAAAAAAC04/ln9PMPs9_tI/w834-h587-no/bubbleSort1.png)

#### Heap sort

![](https://lionelliu.com/static/images/2014/heap-sort-demo.png)

![](http://draperg.cis.byuh.edu/archive/winter2010/cs301/hw/heap/heap_sort.png)

- $$T(n) : O (nlgn)$$

- $$S(n): O(1)$$

- **stability**: unstable

- **illustration**:

  ![](https://cdn-images-1.medium.com/max/1600/1*nbN4DNPdFcTrD3BTGGuUKQ.png)

#### Quick sort

![](http://www.myassignmenthelp.net/images/quick-sort-pseudo-code.png)

- **loop invariant**: The four regions maintained by the procedure PARTITION on a subarray $$A[p..r]$$. The values in  $$A[p..i]$$are all less than or equal to $$x$$, the values in $$A[i+1..j-1]$$  are all greater than $$x$$, and $$A[r]=x$$. The subarray $$A[j..r-1]$$ can take on any values.

- $$T(n) : \Theta (nlgn)$$ (worst case: $$\Theta (n^2)$$)

- $$S(n): O(1)$$

- **stability**: unstable

- **illustration**:

  ![](http://img.my.csdn.net/uploads/201302/18/1361175605_3156.jpg)

#### Sorting in Linear Time

##### - Counting sort

![](https://i.stack.imgur.com/JVGpq.png)

- $$T(n) : \Theta (k + n)$$

- $$S(n): O(k)$$

- **stability**: stable

- **illustration**:

  ![](http://staff.ustc.edu.cn/~csli/graduate/algorithms/book6/176_a.gif)

##### - Radix sort

```python
def RADIX_SORT(A, d):
    for i in range(d):
        # use a stable sort (eg. counting sort) to sort array A on digit i
```

- $$T(n) : \Theta (d(n+k))$$

- $$S(n): depends$$

- **stability**: stable

- **illustration**:

  ![](http://www.includehelp.com/ds/Images/radix-sort.jpg)

##### - Bucket sort

![](http://www2.hawaii.edu/~nodari/teaching/s15/Notes/Topic-10/pseudocode-bucket-sort.jpg)

- $$T(n) : \Theta (n)$$ (worst case: $$\Theta (n^2)$$)

- $$S(n): O(n)$$ 

- **stability**: stable

- **illustration**:

  ![](https://upload.wikimedia.org/wikipedia/fa/a/a6/BucketSort3.gif)

#### Supplement

[An useful website](https://www.geeksforgeeks.org/time-complexities-of-all-sorting-algorithms/)

##### - Algorithm Comparison

![](https://3.bp.blogspot.com/-HtI9w4m1MLE/V0mfhnq7CkI/AAAAAAAAANk/aI5DZEsHbgAAnt-nP-6UxCwf0BAgzNijgCLcB/s1600/sorting.png)

##### - Shell sort

```python
def shell_sort(target_list):
    n = len(target_list)
    # 初始步长
    gap = n // 2
    while gap > 0:
        for i in range(gap, n):
            # 每个步长进行插入排序
            temp = target_list[i]
            j = i
            # 插入排序
            while j >= gap and target_list[j - gap] > temp:
                target_list[j] = target_list[j - gap]
                j -= gap
            target_list[j] = temp
        # 得到新的步长
        gap = gap // 2
    return target_list
```

- $$T(n) :$$

  | gap list   | worst case time complexity |
  | ---------- | -------------------------- |
  | $$2^i3^j$$ | $$O(nlog^2n)$$             |
  | $$2^k-1$$  | $$O(n^{3/2})$$             |
  | $$n/2^i$$  | $$O(n^2)$$                 |

- $$S(n): O(1)$$ 

- **stability**: unstable

- **illustration**:

  ![](https://raw.githubusercontent.com/heray1990/AlgorithmVisualization/master/images/shell_sort.png)

  ![](http://images.slideplayer.com/32/10050174/slides/slide_42.jpg)

#### Medians and Order Statistics

##### - Simultaneous minimum and maximum

![](https://www.researchgate.net/profile/Cyril_Nicaud/publication/282679313/figure/fig1/AS:391500828561417@1470352479893/Naive-and-optimized-implementations-of-simultaneous-maximum-and-minimum-finding.png)

##### - Selection in expected linear time

The following code for **RANDOMIZED-SELECT** returns the $$i_{th}$$ smallest element of the array $$A[p..r]$$ .

![](https://web.cs.dal.ca/~arc/teaching/CSci6702/2014/Assignment2/Select.jpg)

##### - Selection in worst-case linear time

The **SELECT** algorithm determines the $$i_{th}$$ smallest of an input array of $$n > 1$$ distinct elements by executing the following steps. (If $$n = 1$$, then **SELECT** merely returns its only input value as the ith smallest.)

1. Divide the $$n$$ elements of the input array into $$\lfloor n/5 \rfloor$$ groups of $$5$$ elements each and at most one group made up of the remaining $$n$$ mod $$5$$ elements.
2. Find the median of each of the $$\lceil n/5 \rceil$$ groups by first insertion-sorting the elements of each group (of which there are at most $$5$$) and then picking the median from the sorted list of group elements.
3. Use **SELECT**  recursively to find the median $$x$$ of the $$\lceil n/5 \rceil$$ medians found instep 2. (If there are an even number of medians, then by our convention, x is the lower median.)
4. Partition the input array around the median-of-medians $$x$$ using the modified version of PARTITION. Let $$k$$ be one more than the number of elements on the low side of the partition, so that $$x$$ is the $$k_{th}$$ smallest element and there are $$n$$ kelements on the high side of the partition.
5. If $$i = k$$, then return $$x$$. Otherwise, use **SELECT** recursively to find the $$i_{th}$$ smallest element on the low side if $$i < k$$, or the $$(i-k)_{th}$$ smallest element on the high side if $$i > k$$.

![](https://i.stack.imgur.com/IVK74.png)

We can now develop a recurrence for the worst-case running time $$T(n)$$ of the algorithm **SELECT**. Steps 1, 2, and 4 take $$O(n)$$ time. (Step 2 consists of $$O(n)$$ calls of insertion sort on sets of size $$O(1)$$.) Step 3 takes time $$T(\lceil n/5 \rceil)$$, and step 5 takes time at most $$T(7n/10+6)$$, assuming that $$T$$ is monotonically increasing. We make the assumption, which seems unmotivated at first, that any input of fewer than 140 elements requires $$T(1)$$ time; the origin of the magic constant 140 will be clear shortly.

### Data Structures

**Stacks**, **Queues**, **Linked lists**, **Implementing pointers and objects**, and **Representing rooted trees** *will be ignored. If you encounter problems in the following sections, you can read the book, Introduction to Algorithms.*

#### Hash Tables

##### - Hash functions

**Interpreting keys as natural numbers**:

Most hash functions assume that the universe of keys is the set $$\mathbb{N}=\{0,1,2,...\}$$ of natural numbers. Thus, if the keys are not natural numbers, we find a way to interpret them as natural numbers.

* The division method

  $$h(k) = k\,\bmod\,m$$

* The multiplication method

  $$h(k) = \lfloor m(kA\,\bmod\,1) \rfloor\;$$ 

  where $$0 < A < 1$$ and “$$kA\,\bmod\,1$$” means the fractional part of $$kA$$, that is, $$kA-\lfloor kA \rfloor$$.

  ***An advantage of the multiplication method is that the value of m is not critical.***

* Universal hashing

  Choosing the hash function randomly in a way that is independent of the keys that are actually going to be stored to avoid $$n$$ keys that all hash to the same slot.

##### - Open addressing

In open addressing, all elements occupy the hash table itself. That is, ***each table entry contains either an element of the dynamic set or NIL***. When searching for an element, we systematically examine table slots until either we find the desired element or we have ascertained that the element is not in the table. ***No lists and no elements are stored outside the table***, unlike in chaining.

* Linear probing

  $$h(k, i) = (h'(k)+i)\,\bmod\,m$$

* Quadratic probing

  $$h(k, i) = (h'(k)+c_1i+c_2i^2)\,\bmod\,m$$

* Double hashing

  $$h(k, i) = (h_1(k)+ih_2(k))\,\bmod\,m$$

Given an open-address hash table with load factor $$\alpha=n/m<1$$, the expected number of probes in an **unsuccessful search** is at most $$1/(1-\alpha)$$, assuming uniform hashing.

Given an open-address hash table with load factor $$\alpha < 1$$, the expected number of probes in a **successful search** is at most

$$\frac{1}{\alpha}\ln \frac{1}{1-\alpha}$$, 

assuming uniform hashing and assuming that each key in the table is equally likely to be searched for.

##### - Perfect hashing 

Although hashing is often a good choice for its excellent average-case performance, hashing can also provide excellent worst-case performance when the set of keys is ***static***: once the keys are stored in the table, the set of keys never changes.

We call a hashing technique ***perfect hashing*** if $$O(1)$$ memory accesses are required to perform a search in the worst case.

Instead of making a linked list of the keys hashing to slot $$j$$ , however, we use a small ***secondary hash table*** $$S_j$$ with an associated hash function $$h_j$$. By choosing the hash function $$h_j$$ carefully, we can guarantee that there are no collisions at the secondary level.

* Suppose that we store n keys in a hash table of size $$m = n^2$$ using a hash function h randomly chosen from a universal class of hash functions. Then, the probability is less than $$1/2$$ that there are any collisions.

* Suppose that we store n keys in a hash table of size $$m = n$$ using a hash function $$h$$ randomly chosen from a universal class of hash functions. Then, we have 

  $$E(\sum\limits_{j=0}^{m-1}n_j^2) < 2n$$ 

  where $$n_j$$ is the number of keys hashing to slot $$j$$.

#### Binary Search Trees

The keys in a binary search tree are always stored in such a way as to satisfy the ***binary-search-tree property***: 

Let $$x$$ be a node in a binary search tree. If $$y$$ is a node in the left subtree of $$x$$, then $$y.key \le x.key$$. If $$y$$ is a node in the right subtree of $$x$$, then $$y.key \ge x.key$$.

```python
def INORDER_TREE_WALK(x):
    if x <> NIL:
        INORDER_TREE_WALK(x.left)
        print(x.key)
        INORDER_TREE_WALK(x.right)
```

```python
# recursion version
def TREE_SEARCH(x, k):
    if x == NIL or k == x.key:
        return x
    if k < x.key:
        return TREE_SEARCH(x.left, k)
    else:
        return TREE_SEARCH(x.right, k)
    
# iterative version
def ITERATIVE_TREE_SEARCH(x):
    while x <> NIL and k <> x.key:
        if k < x.key:
            x = x.left
        else:
            x = x.right
    return x
```

```python
def TREE_MINIMUM(x):
    while x.left <> NIL:
        x = x.left
    return x

def TREE_MAXIMUM(x):
    while x.right <> NIL:
        x = x.right
    return x
```

```python
def TREE_SUCCESSOR(x):
    if x.right <> NIL:
        return TREE_MINIMUM(x.right)
    '''
    if the right subtree of node x is empty and x has a successor y, then y is the lowest        
    ancestor of x whose left child is also an ancestor of x.
    '''
    y = x.p
    while y <> NIL and x == y.right:
        x = y
        y = y.p
    return y

def TREE_PREDECESSOR(x):
    if x.left <> NIL:
        return TREE_MAXIMUM(x.left)
    '''
    if the left subtree of node x is empty and x has a predecessor y, then y is the lowest        
    ancestor of x whose right child is also an ancestor of x.
    '''
    y = x.p
    while y <> NIL and x == y.left:
        x = y
        y = y.p
    return y
```

```python
def TREE_INSERT(T, z):
    y = NIL
    x = T.root
    while x <> NIL:
        y = x
        if z.key < x.key:
            x = x.left
        else:
            x = x.right
    z.p = y
    if y == NIL:
        T.root = z	# tree T was empty
    elif z.key < y.key:
        y.left = z
    else:
        y.right = z
```

```python
def TREE_DELETE(T, z):
    if z.left == NIL:
        TRANSPLANT(T, z, z.right)
    elif z.right == NIL:
        TRANSPLANT(T, z, z.left)
    else:
        y = TREE_MINIMUM(Z.right)
        if y.p <> z:
            TRANSPLANT(T, y, y.right)
            y.right = z.right
            y.right.p = y
        TRANSPLANT(T, z, y)
        y.left = z.left
        y.left.p = y

# turn u's parent into v's parent
def TRANSPLANT(T, u, v):
    if u.p == NIL:
        T.root = v
    elif u == u.p.left:
        u.p.left = v
    else:
        u.p.right = v
    if v <> NIL:
        v.p = u.p
```

***The expected height of a randomly built binary search tree on n distinct keys is $$O(\lg n)$$.***

#### Red-Black Trees

By constraining the node colors on any simple path from the root to a leaf, red-black trees ensure that no such path is more than twice as long as any other, so that the tree is approximately ***balanced***. Each node of the tree now contains the attributes $$color, key, left, right,$$ and $$p$$.

A red-black tree is a binary tree that satisfies the following ***red-black properties***:

1. Every node is either red or black.
2. The root is black.
3. Every leaf (NIL) is black.
4. If a node is red, then both its children are black.
5. For each node, all simple paths from the node to descendant leaves contain the same number of black nodes.

***A red-black tree with $$n$$ internal nodes has height at most $$2\lg(n+1)$$.***

**Rotation:**

We change the pointer structure through ***rotation***, which is a local operation in a search tree that preserves the binary-search-tree property.

When we do a left rotation on a node $$x$$, we assume that its right child $$y$$ is not $$T.nil$$; $$x$$ may be any node in the tree whose right child is not $$T.nil$$.

![](https://images0.cnblogs.com/blog2015/779368/201507/270939323913500.png)

```python
def LEFT_ROTATE(T, x):
    y = x.right				# set y
    x.right = y.left			# turn y's left subtree into x's right tree
    if y.left <> T.nil:
        y.left.p = x
    y.p = x.p
    if x.p == T.nil:			# turn x's parent into y's parent
        T.root = y
    elif x == x.p.left:
        x.p.left = y
    else:
        x.p.right = y
    y.left = x				# put x on y's left
    x.p = y
```

**Insertion:**

```python
def RB_INSERT(T, z):
    y = T.nil
    x = T.root
    while x <> T.nil:
        y = x
        if z.key < x.key:
            x = x.left
        else:
            x = x.right
    z.p = y
    if y == T.nil:
        T.root = z
    elif z.key < y.key:
        y.left = z
    else:
        z.right = z
    z.left = T.nil
    z.right = T.nil
    '''
    All simple paths from the node to descendant leaves contain the same number of black nodes.
    '''
    z.color = 'red'			# initialize z's color to RED
    RB_INSERT_FIXUP(T, z) 		# added a fix-up step

def RB_INSERT_FIXUP(T, z):
    while z.p.color == 'red':	# RED node's child must be black
        if z.p == z.p.p.left:
            y = z.p.p.right
            if y.color == 'red':
                z.p.color = 'black'		# Case 1
                y.color = 'black'		# Case 1
                z.p.p.color = 'red'		# Case 1
                z = z.p.p			# Case 1
            else:
                if z == z.p.right:
                    z = z.p			# Case 2
                    LEFT_ROTATE(T, z)		# Case 2
                z.p.color = 'black'		# Case 3
            	z.p.p.color = 'red'		# Case 3
            	RIGHT_ROTATE(T, z.p.p)		# Case 3
        else:
            '''
            same as then clause with "right" and "left" exchanged
            '''
   T.root.color = 'black'		# The root must be black.
```

![](http://ww1.sinaimg.cn/mw690/76f286d1jw1e5znpv7uifj20i60lmt9z.jpg)



**Deletion:**

```python
def RB_TRANSPLANT(T, u, v):
    if u.p == T.nil:
        T.root = v
    elif u == u.p.left:
        u.p.left = v
    else:
        u.p.right = v
    v.p = u.p
```

```python
def RB_DELETE(T, z):
    y = z
    y_original_color = y.color		# store y's original color
    if z.left == T.nil:
        x = z.right
        RB_TRANSPLANT(T, z, z.right)
    elif z.right == T.nil:
        x = z.left
        RB_TRANSPLANT(T, z, z.left)
    else:
        y = TREE_MINIMUM(z.right)	# find z's successor
        y_original_color = y.color
        x = y.right			# z's successor only has right child			
        if y.p == z:
            x.p = y			# z will be deleted
        else:
            RB_TRANSPLANT(T, y, y.right)
            y.right = z.right
            y.right.p = y
        RB_TRANSPLANT(T, z, y)
        y.left = z.left
        y.left.p = y
        y.color = z.color
    if y_original_color == 'black':	# if cause violations of the red-black properties 
        RB_DELETE_FIXUP(T, x)

def RB_DELETE_FIXUP(T, x):
    while x <> T.root and x.color == 'black':
        if x == x.p.left:
            w = x.p.right
            if w.color == 'red':				# Case 1
                w.color = 'black'				# Case 1
                x.p.color = 'red'				# Case 1
                LEFT_ROTATE(T, x.p)				# Case 1
                w = x.p.right
            if w.left.color == 'black' and w.right.color == 'black':
                w.color = 'red'						# Case 2
                x = x.p							# Case 2
            else:
                if w.right.color == 'black':
                    w.left.color = 'black'			# Case 3
                    w.color = 'red'				# Case 3
                    RIGHT_ROTATE(T, w)				# Case 3
                    w = x.p.right				# Case 3
                w.color = x.p.color			# Case 4
                x.p.color = 'black'			# Case 4
                w.right.color = 'black'			# Case 4
                LEFT_ROTATE(T, x.p)			# Case 4
                x = T.root				# Case 4
        else:
            '''
            same as then clause with "right" and "left" exchanged
            '''
    x.color = 'black'
```

![](https://okl2aaa54.qnssl.com/delete-fixup.gif)

#### Augmenting Data Structures

We now discusses two data structures that we construct by ***augmenting red-black trees***. 

1. We describes a data structure that supports general ***order-statistic operations*** on a dynamic set. We can then quickly find the ith smallest number in a set or the rank of a given element in the total ordering of the set.
2. We abstracts the process of augmenting a data structure and provides a theorem that can simplify the process of augmenting red-black trees. Then we uses this theorem to help design a data structure for ***maintaining a dynamic set of intervals***, such as time intervals. Given a query interval, we can then quickly find an interval in the set that overlaps it.

##### - Dynamic order statistics

![](http://staff.ustc.edu.cn/~csli/graduate/algorithms/book6/282_a.gif)



An order-statistic tree T is simply a red-black tree with additional information stored in each node. Besides the usual red-black tree attributes $$x.key, x.color, x.p, x.left$$ and $$x.right$$ in a node $$x$$, we have another attribute, $$x.size$$. This attribute contains the number of (internal) nodes in the subtree rooted at x (including x itself), that is, the size of the subtree. If we define the sentinel’s size to be $$0$$—that is, we set $$T.nil.size$$ to be $$0$$—then we have the identity:

$$x.size = x.left.size + x.right.size + 1$$

**Retrieving an element with a given rank:**

```python
def OS_SELECT(x, i):
    r = x.left.size + 1
    if i == r:
        return x
    elif i < r:
        return OS_SELECT(x.left, i)
    else:
        return OS_SELECT(x.right, i-r)
```

**Determining the rank of an element:**

```python
def OS_RANK(T, x):
    r = x.left.size + 1
    y = x
    while y <> T.root:
        if y == y.p.right:
            r = r + y.p.left.size + 1
        y = y.p
    return r
```

**Maintaining subtree sizes:**

* Insertion

  Insertion into a red-black tree consists of two phases. The first phase goes down the tree from the root, inserting the new node as a child of an existing node. The second phase goes up the tree, changing colors and performing rotations to maintain the red-black properties.

  1. To maintain the subtree sizes in the first phase, we simply ***increment*** $$x.size$$ for each node x on the simple path traversed from the root down toward the leaves.

  2. Referring to the code for ***LEFT_ROTATE(T, x)***, we add the following lines:

     $$y.size = x.size$$

     $$x.size = x.left.size + x.right.size + 1$$

* Deletion

  Deletion from a red-black tree also consists of two phases: the first operates on the underlying search tree, and the second causes at most three rotations and otherwise performs no structural changes.

  1. The first phase either removes one node y from the tree or moves upward it within the tree. To update the subtree sizes, we simply traverse a simple path from node y (starting from its original position within the tree) up to the root, ***decrementing*** the size attribute of each node on the path.
  2. We handle the $$O(1)$$ rotations in the second phase of deletion in the same manner as for insertion.

##### - How to augment a data structure

1. Choose an underlying data structure.
2. Determine additional information to maintain in the underlying datastructure.
3. Verify that we can maintain the additional information for the basic modifying operations on the underlying data structure.
4. Develop new operations.

##### - Interval trees

***Step 1: Underlying data structure***

We choose a red-black tree in which each node $$x$$ contains an interval $$x.int$$ and the key of $$x$$ is the low endpoint, $$x.int.low$$, of the interval. Thus, an inorder tree walk of the data structure lists the intervals in sorted order by low endpoint.

***Step 2: Additional information***

In addition to the intervals themselves, each node x contains a value $$x.max$$, which is the maximum value of any interval endpoint stored in the subtree rooted at $$x$$.

***Step 3: Maintaining the information***

We must verify that insertion and deletion take $$O(\lg n)$$ time on an interval tree of $$n$$ nodes. We can determine $$x.max$$ given interval $$x.int$$ and the max values ofnode $$x$$’s children:

![](http://www.euroinformatica.ro/documentation/programming/!!!Algorithms_CORMEN!!!/images/fig335_01_0.jpg)

$$x.max = \max(x.int.high,\;x.left.max,\;x.right.max)$$

Insertion and deletion run in $$O(\lg n)$$ time. In fact, we can update the max attributes after a rotation in $$O(1)$$ time.

***Step 4: Developing new operations***

The only new operation we need is ***INTERVAL_SEARCH(T, i)***, which finds a node in tree T whose interval overlaps interval $$i$$. If there is no interval that overlaps $$i$$ in the tree, the procedure returns a pointer to the sentinel $$T.nil$$.

```python
# to see whether x overlaps y
def OVERLAP(x, y):
    if x.high < y.low or x.low > y.high:
        return false
    else:
        return true

def INTERVAL_SEARCH(T, i):
     x = T.root
     while x <> T.nil and (not OVERLAP(i, x.int)):
        if x.left <> T.nil and x.left.max >= i.low:
            x = x.left
        else:
            x = x.right
     return x
```

### Advanced Design and Analysis Techniques

***Dynamic programming*** is effective when a given subproblem may arise from more than one partial set of choices; the key technique is to ***store the solution to each such subproblem*** in case it should reappear.

The idea of a ***greedy algorithm*** is to make each choice in a ***locally optimal manner***. A simple example is coin-changing: to minimize the number of U.S. coins needed to make change for a given amount, we can repeatedly select the largest-denomination coin that is not larger than the amount that remains. A greedy approach provides an optimal solution for many such problems much more quickly than would a dynamic-programming approach. We cannot always easily tell whether a greedy approach will be effective, however.

We use ***amortized analysis*** to analyze certain algorithms that perform ***a sequence of similar operations***. Instead of bounding the cost of the sequence of operations by bounding the actual cost of each operation separately, an amortized analysis provides a bound on the actual cost of the entire sequence. One advantage of this approach is that although some operations might be expensive, many others might be cheap.

#### Dynamic Programming

Dynamic programming, like the divide-and-conquer method, solves problems by combining the solutions to subproblems.

When developing a dynamic-programming algorithm, we follow a sequence of ***four steps***:

1. Characterize the structure of an optimal solution.
2. Recursively define the value of an optimal solution.
3. Compute the value of an optimal solution,typically in a bottom-up fashion.
4. Construct an optimal solution from computed information.

##### - Rod cutting

The rod-cutting problem is the following. Given a rod of length $$n$$ inches and a table of prices $$p_i$$ for $$i = 1, 2,…,n$$, determine the maximum revenue $$r_n$$ obtainable by cutting up the rod and selling the pieces.

```python
# top-down with memoization
def MEMOIZED_CUT_ROD(p, n):
    r = [minus_infinity for _ in range(n+1)]
    return MEMOIZED_CUT_ROD_AUX(p, n, r)

def MEMOIZED_CUT_ROD_AUX(p, n, r):
    if r[n] >= 0:
        return r[n]
    if n == 0:
        q == 0
    else:
        q == minus_infinity
        for i in range(1, n+1):
            q = max(q, p[i]+MEMOIZED_CUT_ROD_AUX(p, n-i, r))
    r[n] = q
    return q

# bottom-up method
def BOTTOM_UP_CUT_ROD(p, n):
    r = [0 for _ in range(n+1)]
    for j in range(1, n+1):
        q = minus_infinity
        for i in range(1, j+1):
            q = max(q, p[i] + r[j - i])
        r[j] = q
    return r[n]

# reconstructing a solution
def EXTENDED_BOTTOM_UP_CUT_ROD(p, n):
    r = [0 for _ in range(n+1)]
    s = [0 for _ in range(n+1)]
    for j in range(1, n+1):
        q = minus_infinity
        for i in range(1, j+1):
            if q < p[i] + r[j - i]:
                q = p[i] + r[j - i]
                s[j] = i	# moptimal first-piece size of cutting a rod of length j
        r[j] = q
    return r, s

def PRINT_CUT_ROD_SOLUTION(p, n):
    r, s = EXTENDED_BOTTOM_UP_CUT_ROD(p, n)
    while n > 0:
        print(s[n])
        n = n - s[n]
```

##### - Matrix-chain multiplication

We state the ***matrix-chain multiplication problem*** as follows: given a chain $$ \langle A_1, A_2,…, A_n  \rangle $$ of $$n$$ matrices,where for $$i = 1,2,\ldots,n$$, matrix $$A_i$$ has dimension $$p_{i-1} \times p_i$$, fully parenthesize the product $$A_1A_2 \cdots A_n$$ in a way that minimizes the number of scalar multiplications.

**Counting the number of parenthesizations:**

$$p(n) = \begin{cases} 1,  & \mbox{if }n = 1 \\  \sum\limits_{k=1}^{n-1}P(k)P(n-k) & \mbox{if }n \ge 2 \end{cases}$$

The solution to the recurrence is $$ \Omega(2^n)$$.

**Applying dynamic programming:**

***Step 1: The structure of an optimal parenthesization***

For our first step in the dynamic-programming paradigm, we find the optimal substructure and then use it to construct an optimal solution to the problem from optimal solutions to subproblems.

**Step 2: A recursive solution**

Let $$m[i,j]$$  be the minimum number of scalar multiplications needed to compute the matrix $$A_{i\ldots j}$$; for the full problem, the lowest-cost way to compute $$A_{1\ldots n}$$ would thus be $$m[1,n]$$.

$$m[i,j] = \begin{cases} 0,  & \mbox{if }i=j \\  \min\limits_{i \le k <j} \{m[i,k] + m[k+1,j]+p_{i-1}p_kp_j\} & \mbox{if }i<j \end{cases}$$

Then we define $$s[i,j]$$  to be a value of $$k$$ at which we split the product $$A_iA_{i+1} \cdots A_j$$ in an optimal parenthesization.

**Step 3: Computing the optimal costs**

```python
def MATRIX_CHAIN_ORDER(p):
    n = len(p) - 1
    m = [[0 for _ in range(n+1)] for _ in range(n+1)]
    s = [[0 for _ in range(n+1)] for _ in range(n+1)]
    for l in range(2, n+1):		# l is the chain length
        for i in range(1, n-l+2):
            j = i + l - 1
            m[i][j] = infinity
            for k in range(i, j):
                q = m[i][k] + m[k+1][j] + p[i-1]*p[k]*p[j]
                if q < m[i][j]:
                    m[i][j] = q
                    s[i][j] = k
	return m, s
```

**Step 4: Constructing an optimal solution:**

```python
def PRINT_OPTIMAL_PARENS(s, i , j):
    if i == j:
        print("A"+str(i))
    else:
        print("(")
        PRINT_OPTIMAL_PARENS(s, i , s[i][j])
        PRINT_OPTIMAL_PARENS(s, s[i][j]+1 , j)
        print(")")
```

##### - Elements of dynamic programming

In this section, we examine the two key ingredients that an optimization problem must have in order for dynamic programming to apply: ***optimal substructure*** and ***overlapping subproblems***. We also revisit and discuss more fully how ***memoization*** might help us take advantage of the overlapping-subproblems property in a top-down recursive approach.

* **Optimal substructure**

  A problem exhibits optimal substructure if an optimal solution to the problem contains within it optimal solutions to subproblems.

  Optimal substructure varies across problem domains in two ways:

  1. how many subproblems an optimal solution to the original problem uses
  2. how many choices we have in determining which subproblem(s) to use in anoptimal solution

  Informally, the running time of a dynamic-programming algorithm depends on the product of two factors: ***the number of subproblems overall*** and ***how many choices we look at for each subproblem***.

  * **Subtleties**

    You should be careful not to assume that optimal substructure applies when it does not. Consider the following two problems in which we are given a directed graph $$G=(V,E)$$ and vertices $$u,v \in V$$.

    **Unweighted shortest path** $$\surd$$

    Find a path from $$u$$ to $$v$$ consisting of the fewest edges. Such a path must be simple, since removing a cycle from a path produces a path with fewer edges.

    **Unweighted longest simple path** $$\large{\times}$$

    Find a simple path from $$u$$ to $$v$$  consisting of the most edges. We need to include the requirement of simplicity because otherwise we can traverse a cycle as many times as we like to create paths with an arbitrarily large number of edges.

* **Overlapping subproblems**

  The second ingredient that an optimization problem must have for dynamic programming to apply is that ***the space of subproblems must be “small”*** in the sense that a recursive algorithm for the problem solves the same subproblems over and over, rather than always generating new subproblems. Typically, the total number of distinct subproblems is a polynomial in the input size. When a recursive algorithm revisits the same problem repeatedly, we say that the optimization problem has ***overlapping subproblems***.

  ![](http://www.cs.fsu.edu/~burmeste/slideshow/images_content/figure16_2.gif)

  Matrix-chain multiplication has only $$\Theta(n^2)$$ distinct subproblems, and the dynamic-programming algorithm solves each exactly once.

##### - Longest common subsequence

In the longest-common-subsequence problem, we are given two sequences $$X = \langle x_1,x_2,\ldots,x_m \rangle$$ and $$Y = \langle y_1,y_2,\ldots,y_n \rangle$$ and wish to find a maximum-length common subsequence of $$X$$ and $$Y$$ .

***Optimal substructure of an LCS***

Let $$Z = \langle z_1,z_2,\ldots,z_k \rangle$$ be any LCS of $$X$$ and $$Y$$.

1. If $$x_m = y_n$$, then $$ z_k = x_m = y_n$$ and $$Z_{k-1}$$ is an LCS of $$X_{m-1}$$ and $$Y_{n-1}$$.
2. If $$x_m \neq y_n$$, then $$ z_k \neq x_m$$ implies that $$Z_{k-1}$$ is an LCS of $$X_{m-1}$$ and $$Y$$.
3. If $$x_m \neq y_n$$, then $$ z_k \neq y_n$$ implies that $$Z_{k-1}$$ is an LCS of $$X$$ and $$Y_{n-1}$$.

***A recursive solution***

$$c[i,j] = \begin{cases} 0,  & \mbox{if }i=0\mbox{ or }j=0,\\ c[i-1,j-1]+1, & \mbox{if }i,j>0\mbox{ and }x_i=y_j,  \\  \max\{c[i,j-1], c[i-1,j]\} & \mbox{if }i,j>0\mbox{ and }x_i \neq y_j, \end{cases}$$

***Computing the length of an LCS***

```python
def LCS_LENGTH(X, Y):
    m = len(X)
    n = len(Y)
    b = [[0 for _ in range(n+1)] for _ in range(m+1)]
    c = [[0 for _ in range(n+1)] for _ in range(m+1)]
    for i in range(1, m+1):
        for j in range(1, n+1):
            if X[i] == Y[j]:
                c[i][j] = c[i-1][j-1] + 1
                b[i][j] = 'upleft'
            elif c[i-1][j] >= c[i][j-1]:
                c[i][j] = c[i-1][j]
                b[i][j] = 'up'
            else:
                c[i][j] = c[i-1][j]
                b[i][j] = 'left'
     return c, b
```

![](http://dovgalecs.com/blog/wp-content/uploads/2012/12/fig376_01.jpg)

***Constructing an LCS***

```python
def PRINT_LCS(b, X, i, j):
    if i == 0 and j == 0:
        return
    if b[i][j] == 'upleft':
        PRINT_LCS(b, X, i-1, j-1)
        print X[i]
    elif b[i][j] == 'up':
        PRINT_LCS(b, X, i-1, j)
    else:
        PRINT_LCS(b, X, i, j-1)
```

##### - Optimal binary search trees

Formally, we are given a sequence $$K = \langle k_1,k_2,\ldots,k_m \rangle$$  of n distinct keys in sorted order (so that $$k_1 < k_2 <     < k_n$$), and we wish to build a binary search tree from these keys. For each key $$k_i$$ , we have a probability $$p_i$$ that a search will be for $$ki$$. Some searches may be for values not in $$K$$, and so we also have $$n + 1$$ “dummy keys” $$d_0 , d_1 , d2 , \ldots ,d_n$$ representing values not in $$K$$. For each dummy key $$d_i$$, we have a probability $$q_i$$ that a search will correspond to $$d_i$$.

Every search is either successful (finding some key $$k_i$$ ) or unsuccessful (finding some dummy key $$d_i$$ ), and so we have $$\sum\limits_{i=1}^{n}p_i + \sum\limits_{i=0}^{n}q_i = 1$$.

$$E[\text{search cost in }T] = 1 + \sum\limits_{i=1}^{n}\text{depth}_T(k_i) \cdot p_i + \sum\limits_{i=0}^{n}\text{depth}_T(d_i) \cdot q_i$$

***Step 1: The structure of an optimal binary search tree***

If an optimal binary search tree $$T$$ has a subtree $$T'$$ containing $$k_i,\ldots,k_j$$, then this subtree $$T'$$ must be optimal as well for the subproblem with keys $$k_i,\ldots,k_j$$ and dummy keys $$d_{i-1},\ldots,d_j$$.

***Step 2: A recursive solution***

$$w(i, j) = \sum\limits_{l=i}^{j}p_l + \sum\limits_{l=i-1}^{j}q_l$$

If $$k_r$$ is the root of an optimal subtree containing keys $$k_i,\ldots,k_j$$, let us denote this sum of probabilities as

$$e[i ,j] = p_r + (e[i, r-1] + w(i, r-1)) + (e[r+1, j] + w(r+1,j))$$

$$e[i ,j] = e[i,r-1] + e[r+1,j] + w(i,j)$$

$$e[i ,j] = \begin{cases} q_{i-1}  & \mbox{if }j=i-1, \\ \min\limits_{i \le r \le j}\{ e[i,r-1] + e[r+1,j] + w(i,j) \} & \mbox{if }i\leq j. \end{cases}$$

***Step 3: Computing the expected search cost of an optimal binary search tree***

$$w(i ,j) = w(i, j-1) + p_j + q_j$$

```python
def OPTIMAL_BST(p, q, n):
    e = [[0 for _ in range(n+1)] for _ in range(n+2)]
    w = [[0 for _ in range(n+1)] for _ in range(n+2)]
    root = [[0 for _ in range(n+1)] for _ in range(n+1)]
    for i in range(1, n+2):
        e[i][i-1] = q[i-1]
        w[i][i-1] = q[i-1]
    for l in range(1, n+1):
        for i in range(l, n-l+2):
            j = i+l-1
            e[i][j] = 'infinity'
            w[i][j] = w[i][j-1] + p[j] + q[j]
            for i in range(i, j+1):
                t = e[i][r-1] + e[r+1][j] + w[i][j]
                if t < e[i][j]:
                    e[i][j] = t
                    root[i][j] = r
    return e, root
```

#### Greedy Algorithms

Algorithms for optimization problems typically go through a sequence of steps, with a set of choices at each step. For many optimization problems, using dynamic programming to determine the best choices is overkill; simpler, more efficient algorithms will do.

A ***greedy algorithm*** always makes the choice that looks best at the moment. That is, it makes ***a locally optimal choice*** in the hope that this choice will lead to a globally optimal solution. This chapter explores optimization prob- lems for which greedy algorithms provide optimal solutions.

##### - An activity-selection problem

```python
def RECURSIVE_ACTIVITY_SELECTOR(s, f, k, n):
    m = k+1
    while m <= n and s[m] < f[k]:
        m = m + 1
    if m <= n:
        return [a[m]] + RECURSIVE_ACTIVITY_SELECTOR(s, f, m, n)
    return []

def GREEDY_ACTIVTY_SELECTOR(s, f):
    n = len(s)
    A = [a[1]]
    k = 1
    for m in range(2, n+1):
        if s[m] >= f[k]:
            A = A + [a[m]]
            k = m
    return A
```

##### - Elements of the greedy strategy

We went through the following steps:

1. Determine the optimal substructure of the problem.
2. Develop a recursive solution. (For the activity-selection problem, we formulated recurrence, but we bypassed developing a recursive algorithm based on this recurrence.)
3. Show that if we make the greedy choice, then only one sub problem remains.
4. Prove that it is always safe to make the greedy choice.
5. Develop a recursive algorithm that implements the greedy strategy.
6. Convert the recursive algorithm to an iterative algorithm.

More generally, we design greedy algorithms according to the following sequenceof steps:

1. Cast the optimization problem as one in which we make a choice and are leftwith one subproblem to solve.
2. Prove that there is always an optimal solution to the original problem that makes the greedy choice, so that the greedy choice is always safe.
3. Demonstrate optimal substructure by showing that, having made the greedy choice, what remains is a subproblem with the property that if we combine an optimal solution to the subproblem with the greedy choice we have made, we arrive at an optimal solution to the original problem.

***Greedy-choice property***

***Optimal substructure***

***Greedy versus dynamic programming***

##### - Huffman codes

```python
def HUFFMAN(C):
    n = len(C)
    Q = C
    for i in range(1, n):
        z = {}
        z['left'] = x = EXTRACT_MIN(Q)
        z['right'] = y = EXTRACT_MIN(Q)
        z['freq'] = x.freq + y.freq
        Q.append(z)
    return EXTRACT_MIN(Q)
```

##### - Matroids and greedy methods

A ***matroid*** is an ordered pair $$M = (S, \mathcal{I})$$ satisfying the following conditions.

1. $$S$$ is a finite set.
2. $$\mathcal{I}$$ is a nonempty family of subsets of $$S$$, called the independent subsets of $$S$$, such that if $$B\in \mathcal{I}$$  and $$A\subseteq B$$, then $$A \in \mathcal{I}$$. We say that $$\mathcal{I}$$ is ***hereditary*** if it satisfies this property. Note that the empty set $$\emptyset$$ is necessarily a member of $$\mathcal{I}$$.
3. If $$A \in \mathcal{I}$$, $$B \in \mathcal{I}$$, and $$\mid  A \mid < \mid B \mid$$, then there exists some element $$ x \in B - A$$ such that $$A \cup \{x\} \in \mathcal{I}$$ . We say that $$M$$ satisfies the ***exchange property***.

Theorems:

* If $$G = (V, E)$$ is an undirected graph, then $$M = (S_G, \mathcal{I}_G)$$ is a matroid.
* All maximal independent subsets in a matroid have the same size.

**Greedy algorithms on a weighted matroid**

We say that a matroid $$M = (S, \mathcal{I})$$ is weighted if it is associated with a weight function $$w$$ that assigns a strictly positive weight $$w(x)$$ to each element $$x \in S$$. The weight function $$w$$ extends to subsets of $$S$$ by summation:

$$w(A) = \sum\limits_{x \in A}w(x)$$ for any $$A \subseteq S$$.

Many problems for which a greedy approach provides optimal solutions can be formulated in terms of finding a maximum-weight independent subset in a weighted matroid. That is, we are given a weighted matroid $$M = (S, \mathcal{I})$$, and we wish to find an independent set $$A \in \mathcal{I}$$ such that $$w(A)$$ is maximized. We call such a subset that is independent and has maximum possible weight an optimal subset of the matroid. Because the weight $$w(x)$$ of any element $$x \in S$$ is positive, an optimal subset is always a maximal independent subset—it always helps to make $$A$$ as large as possible.

eg. ***minimum-spanning-tree problem***

##### - A task-scheduling problem as a matroid

An interesting problem that we can solve using matroids is the problem of optimally scheduling unit-time tasks on a single processor, where each task has a deadline, along with a penalty paid if the task misses its deadline

A ***unit-time task*** is a job, such as a program to be run on a computer, that requires exactly one unit of time to complete. Given a finite set $$S$$ of unit-time tasks, a ***schedule*** for $$S$$ is a permutation of $$S$$ specifying the order in which to perform these tasks. The first task in the schedule begins at time 0 and finishes at time 1, the second task begins at time 1 and finishes at time 2, and so on.

#### Amortized Analysis

##### - Aggregate analysis

In ***aggregate analysis***, we show that for all $$n$$, a sequence of $$n$$ operations takes worst-case time $$T(n)$$ in total. In the worst case, the average cost, or amortized cost, per operation is therefore $$T(n)/n$$. Note that this amortized cost applies to each operation, even when there are several types of operations in the sequence. The other two methods we shall study in this chapter, the accounting method and the potential method, may assign different amortized costs to different types of operations.

***1. Stack operations***

For any value of $$n$$, any sequence of n **PUSH**, **POP**, and **MULTIPOP** operations takes a total of O(n) time.

We can pop each object from the stack at most once for each time we have pushed it onto the stack.

***2. Incrementing a binary counter***

As another example of aggregate analysis, consider the problem of implementing a $$k$$-bit binary counter that counts upward from $$0$$. The cost of each **INCREMENT** operation is linear in the number of bits flipped.

In general, for $$i = 0,1,\ldots,k - 1$$, bit $$A[i]$$  flips $$\lfloor n/2^i \rfloor$$ times in a sequence of $$n$$ INCREMENT operations on an initially zero counter. For $$i \geq  k$$, bit $$A[i]$$ does not exist, and so it cannot flip. The total number of flips in the sequence is thus $$2n$$. The average cost of each operation, and therefore the amortized cost per operation, is $$O(n) / n = O(1)$$.

##### - The accounting method

In the ***accounting method*** of amortized analysis, we assign differing charges to different operations, with some operations charged more or less than they actually cost. We call the amount we charge an operation its ***amortized cost***. When an operation’s amortized cost exceeds its actual cost, we assign the difference to specific objects in the data structure as ***credit***.

***1. Stack operations***

***2. Incrementing a binary counter***

##### - The potential method

Instead of representing prepaid work as credit stored with specific objects in the data structure, the ***potential method*** of amortized analysis represents the prepaid work as “potential energy,” or just “potential,” which can be released to pay for future operations.

##### - Dynamic tables

A vivid example of applying amortized analysis to a more complex data structure.

### Advanced Data Structures

#### B-Trees

B-trees are balanced search trees designed to work well on disks or other direct-access  secondary  storage  devices.   B-trees  are  similar  to  red-black  trees, but they are better at minimizing disk I/O operations.  Many database systems use B-trees, or variants of B-trees, to store information.

B-trees differ from red-black trees in that ***B-tree nodes may have many children***, from a few to thousands. 

A ***B-tree*** $$T$$ is a rooted tree (whose root is $$T.root$$) having the following properties:

1. Every node x hass the following attributes:

   a. $$x.n$$ , the number of keys currently stored in node $$x$$,

   b. the $$x.n$$ keys themslves, $$x.key_1, x.key_2,\ldots , x.key_{x.n}$$, stored in nondecreasing order, so that $$x.key_1 \leq x.key_2 \leq \cdots \leq x.key_{x.n}$$,

   c. $$x.leaf$$, a boolean value that is TRUE if $$x$$ is a leaf and FALSE if x is internal node.

2. Each internal node $$x$$ also contains $$x.n+1$$ pointers $$x.c_1, x.c_2, \ldots , x.c_{x.n+1}$$ to
   its children. Leaf nodes have no children, and so their $$c_i$$ attributes are undefined.

3. The keys $$x.key_i$$ separate the ranges of keys stored in each subtree: if $$k_i$$ is any
   key stored in the subtree with root $$x.c_i$$, then $$k_1 \leq x.key_1 \leq k_2 \leq x.key_2 \leq \cdots \leq x.key_{x.n} \leq k_{x.{n+1}}$$.

4. All leaves have the same depth, which is the tree’s height $$h$$.

5. Nodes have lower and upper bounds on the number of keys they can contain.
  We express these bounds in terms of a fixed integer $$t \geq 2$$ called the minimum
  degree of the B-tree:

  a. Every node other than the root must have at least $$t - 1$$ keys. Every internal
  node other than the root thus has at least $$t$$ children. If the tree is nonempty,
  the root must have at least one key.

  b. Every node may contain at most $$2t - 1$$ keys. Therefore, an internal node
  may have at most $$2$$ children. We say that a node is full if it contains exactly
  $$2t - 1$$ keys.

The number of disk accesses required for most operations on a B-tree is proportional
to the height of the B-tree. We now analyze the worst-case height of a B-tree.

If $$n \geq 1$$, then for any $$n$$-key B-tree $$T$$ of height $$h$$ and minimum degree $$t \geq 2$$,

$$h \leq log_t \frac{n+1}{2}$$.

##### - Basic operations on B-trees

```python
# Searching a B-tree
def B_TREE_SEARCH(x, k):
    i = 1
    while i <= x.n and k > x.key[i]:
        i += 1
    if i <= x.n and k == x.key[i]:
        return x, i
    elif x.leaf == True:
        return NIL
    else:
        DISK_READ(x.c[i])
        return B_TREE_SEARCH(x.c[i], k)

# Creating an empty B-tree
def B_TREE_DCREATE(T):
    x = ALLOCATE_NODE()
    x.leaf = True
    x.n = 0
    DISK_WRITE(x)
    T.root = x
```

![](http://og43lpuu1.bkt.clouddn.com/b_tree_summary/img/02_B-Tree-split-child.png)

```python
# Splitting a node in a B-tree
# x.c[i] is a full child of x -> x.c[i].n = 2t - 1
def B_TREE_SPLIT_CHILD(x, i):
    z = ALLOCATE_NODE()
    y = x.c[i]
    z.leaf = y.leaf
    z.n = t-1
    for j in range(1, t):
        z.key[j] = y.key[j+t]
    if not y.leaf:
        for j in range(1, t+1):
            z.c[j] = y.c[j+t]
    y.n = t-1
    for j in range(x.n + 1, i, -1):
        x.c[j+1] = x.c[j]
    x.c[i+1] = z
    for j in range(x.n, i-1, -1):
        x.key[j+1] = x.key[j]
    x.key[i] = y.key[t]
    x.n += 1
    DISK_WRITE(y)
    DISK_WRITE(z)
    DISK_WRITE(x)
```

![](http://staff.ustc.edu.cn/~csli/graduate/algorithms/book6/391_a.gif)

```python
def B_TREE_INSERT(T, k):
    r = T.root
    if r.n == 2t-1:
        s = ALLOCATE_NODE()
        T.root = s
        s.leaf = False
        s.n = 0
        s.c[1] = r
        B_TREE_SPLIT_CHILD(s, 1)
        B_TREE_INSERT_NONFULL(s, k)
    else:
        B_TREE_INSERT_NONFULL(r, k)

def B_TREE_INSERT_NONFULL(x, k):
    i = x.n
    if x.leaf:
        while i >= 1 and k < x.key[i]:
            x.key[i+1] = x.key[i]
            i -= 1
        x.key[i+1] = k
        x.n = x.n + 1
    else:
        while i >= 1 and k < x.key[i]:
            i = i - 1
        i += 1
        DISK_READ(x.c[i])
        if x.c[i].n == 2t-1:
            B_TREE_SPLIT_CHILD(X, i)
            if k > x.key[i]:
                i += 1
        B_TREE_INSERT_NONFULL(x.c[i], k)
```

For a B-tree of height h, B-TREE-INSERT performs $$O(h)$$ disk accesses, since only $$O(1)$$ DISK-READ and DISK-WRITE operations occur between calls to B-TREE-INSERT-NONFULL. The total CPU time used is $$O(th) = O(t\log_tn)$$.
Since B-TREE-INSERT-NONFULL is tail-recursive, we can alternatively implement it as a while loop, thereby demonstrating that the number of pages that need to be in main memory at any time is $$O(1)$$.

***Deleting a key from a B-tree***

![](http://www.euroinformatica.ro/documentation/programming/!!!Algorithms_CORMEN!!!/images/fig472_01_0.jpg)



![](http://www.euroinformatica.ro/documentation/programming/!!!Algorithms_CORMEN!!!/images/fig473_01_0.jpg)



#### Fibonacci Heaps

##### - Mergeable-heap operations

##### - Decreasing a key and deleting a node

##### - Bounding the maximum degree

#### van Emde Boas Trees

#### Data Structures for Disjoint Sets

### Graph Algorithms

#### Elementary Graph Algorithms

##### - Representations of graphs

##### - Breadth-first search

##### - Depth-first search

##### - Topological sort

##### - Strongly connected components

#### Minimum Spanning Trees

##### - Growing a minimum spanning tree

##### - The algorithms of Kruskal and Prim

#### Single-Source Shortest Paths

##### - The Bellman-Ford algorithm

##### - Single-source shortest paths in directed acyclic graphs

##### - Dijkstra’s algorithm

##### - Difference constraints and shortest paths

##### - Proofs of shortest-paths properties

#### All-Pairs Shortest Paths

##### - Shortest paths and matrix multiplication

##### - The Floyd-Warshall algorithm

##### - Johnson’s algorithm for sparse graphs

#### Maximum Flow

##### - Flow networks

##### - The Ford-Fulkerson method

##### - Maximum bipartite matching

##### - Push-relabel algorithms

##### - The relabel-to-front algorithm

### Selected Topics

#### Multithreaded Algorithms

##### - The basics of dynamic multithreading

##### - Multithreaded matrix multiplication

##### - Multithreaded merge sort

#### Matrix Operations

##### - Solving systems of linear equations

##### - Inverting matrices

##### - Symmetric positive-definite matrices and least-squares approximation

#### Linear Programming

##### - Standard and slack forms

##### - Formulating problems as linear programs

##### - The simplex algorithm

##### - Duality

##### - The initial basic feasible solution

#### Polynomials and the FFT

##### - Representing polynomials

##### - The DFT and FFT

##### - Efficient FFT implementations

#### Number-Theoretic Algorithms

##### - Elementary number-theoretic notions

##### - Greatest common divisor

##### - Modular arithmetic

##### - Solving modular linear equations

##### - The Chinese remainder theorem

##### - Powers of an element

##### - The RSA public-key cryptosystem

##### - Primality testing

##### - Integer factorization

#### String Matching

##### - The naive string-matching algorithm

##### - The Rabin-Karp algorithm

##### - String matching with finite automata

##### - The Knuth-Morris-Pratt algorithm

```python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        # using KMP algorithm
        n = len(haystack)
        m = len(needle)
        
        # compute prefix
        prefx = [0] * m
        k = -1
        for q in range(1, m):
            while k > -1 and needle[k+1] <> needle[q]:
                k = prefx[k]
            if needle[k+1] == needle[q]:
                k += 1
            prefx[q] = k
        
        print(prefx)
        
        # matching
        q = -1
        for i in range(n):
            while q > -1 and needle[q+1] <> haystack[i]:
                q = prefx[q]
            if needle[q+1] == haystack[i]:
                q += 1
            if q+1 == m:
                return i-m
        
        return -1
```

#### Computational Geometry

##### - Line-segment properties

##### - Determining whether any pair of segments intersects

##### - Finding the convex hull

##### - Finding the closest pair of points

#### NP-Completeness

##### - Polynomial time

##### - Polynomial-time verification

##### - NP-completeness and reducibility

##### - NP-completeness proofs

##### - NP-complete problems

#### Approximation Algorithms

##### - The vertex-cover problem

##### - The traveling-salesman problem

##### - The set-covering problem

##### - Randomization and linear programming

##### - The subset-sum problem

