> In this blog, I will review basic algorithms I had learned and expolre some questions on *LeetCode*, preparing for some interviews and further graduate study.

Firstly, I will write some important concepts and facts I learned from the course *Introduction to Algorithms*. Then I will look at some problems on *LeetCode*.

## Introduction to Algorithms [Third Edition]

### Getting Started

#### Insertion sort

![](https://i.stack.imgur.com/HmuHD.png)

![](http://www.java2novice.com/images/insertion-sort.png)

1. **loop invariant**: At the start of each iteration of the for loop of lines 1–8, the subarray $$A[1…j-1]$$ consists of the elements originally in $$A[1…j-1]$$, but in sorted order.
2. $$T(n) : (n^2)$$
3. $$S(n): O(1)$$
4. **stability**: stable

We use **loop invariants** to help us understand why an algorithm is **correct**. We must show three things about a loop invariant:

1. Initialization
2. Maintenance
3. Termination

#### Designing algorithms

**The divide-and-conquer approach**: merge sort

##### Merge sort



