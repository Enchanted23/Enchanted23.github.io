> In this blog, I will review basic algorithms I had learned and expolre some questions on *LeetCode*, preparing for some interviews and further graduate study.

Firstly, I will write some important concepts and facts I learned from the course *Introduction to Algorithms*. Then I will look at some problems on *LeetCode*.

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
        return TREE_SUCCESSOR(x.right)
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
        return TREE_PREDECESSOR(x.left)
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

