

## Introduction

### Web搜索的挑战

* 大规模
* 数据的分布性
* 数据的不稳定性
* 数据的质量
* 无结构信息
* 异构数据
* 高价值信息的发现

### Information Retrieval

**Information Retrieval：**

Given a query and a corpus, find relevant documents.

**Web Search: **

given a keyword and a web crawler, find relevant URLs.

| IR vs. DB | DB              | IR (information retrieval) |
| --------- | --------------- | -------------------------- |
| Data      | Structured      | Semi-structured            |
| Fields    | Clear Semantics | Free text                  |
| Queries   | Structured      | Free Text                  |
| Matching  | Exact           | Imprecise                  |
| Ranking   | None            | Important                  |

### IR的任务

* 随机搜索(Ad hoc Search)
* 信息过滤 (Information Filtering)
* 分类 (Classification)
* 问答 (Question Answering)

### IR的基础性问题

* 相关性计算 (Relevance)

  相关性是信息检索中的基本概念。相关文档包含用户把查询发给搜索引擎后他想要找的信息。

* 检索模型(Retrieval Model)

  是对查询与文档匹配过程的形式化表示，它是排序算法(ranking algorithm)的基础，搜索引擎利用排序算法生成文档的有序列表.

* 评价(Evaluation)

  评价指标:准确率(Precision)、召回率(Recall)、F值(F-measure)等

* 信息需求(Information Need)

  查询建议(query suggestion)、查询扩展 (query expansion)和相关性反馈(relevance feedback) 等

* 检索性能(Efficiency)

  如何快速响应用户的信息检索需求? 查询分词技术Indexing快速计算相关性得分。

## 网络爬虫基础

### 网络爬虫常用的搜索策略

* Depth First Search
* Width First Search

### 爬虫涉及的协议

* HTTP/HTML
* DNS/URL
* Robots Exclusion 
* Sitemap

**URL组成:**

协议://主机名\[:端口\]/ 路径/\[:参数\]\[?查询\]#Fragment

### 分布式爬虫 & 一致性Hash

**M个节点同时执行搜集**

问题: 如何有效的把N个网站的搜集任务分配
到M个机器上去?

目标: 任务分配得均匀( Balance )

**Consistent Hashing**

1. 使用一个巨大的hash key空间：
   * 232 bits
   * 组织成回路


2. URL和目标网站IP都hash映射到一个hash key 空间，每个hash key对应一台抓取计算机：
   * Node(url) or Successor(url)


3. 如果某台抓取计算机失效，则该机器中的URL 都迁移到顺时针方向的下一个node：
   * 保证最小的数据迁移 保证负载均衡，因为每个bucket都很小

### 问题

* 重复搜集?
* 遇到回路会无限循环?
* G如果不连通呢?
* G如果大到STACK容不下呢? 
* 如何控制搜集G的一部分呢?

## Text Processing

### 分词

**Segmentation / Tokenization**

* 分词歧义

  1. **交集型歧义(交叉歧义):**

     如果AB和BC都是词典中的词，那么如果待切分字串中包含 “ABC”这个子串，就必然会造成两种可能的切分:“AB/C/” 和“A/BC/”。这种类型的歧义就是交叉歧义。
     比如“网球场”就可能造成交叉歧义:
     网球 / 场 /	or	网 / 球场 /

  2. **组合型歧义(组合歧义):**

     如果AB和A、B都是词典中的词，那么如果待切分字串中包含 “AB”这个子串，就必然会造成两种可能的切分:“AB/”和 “A/ B/ ”。这种类型的歧义就是组合歧义。
     比如“个人”就可能造成组合歧义 

     三 / 个人
     三/个/人/

* 未登录词

  未登录词即未包括在分词词表中但必须切分出来的词， 包括各类专名(人名、地名、企业字号、商标号等)和 某些术语、缩略词、新词等等

* 常用的分词方法

  1. 基于字符串匹配的分词方法

     按照扫描方向的不同:	**正向匹配**和**逆向匹配**

     按照不同长度优先匹配的情况:	**最大匹配**和**最小匹配**

     * **正向最大匹配分词(FMM)：**

       > 从左至右尽可能查找最长的词，直到当前字符与已经处 理的字符串不构成词，输出已经识别的词，并从识别出 来的词后面接着查找词。

       例1:“使用户满意”

       ​使用 / 户 / 满意

     * **反向最大匹配分词(BMM，RMM)：**

       > 从右至左尽可能查找最长的词。

       例:“使用户满意”
       ​	使 / 用户 / 满意

     * **双向最大匹配分词(BM:FMM+RMM)：**

       >  比较FMM法与BMM法的切分结果，从而决定正确的切分.
       >
       >  可以识别出分词中的交叉歧义

       “南京市长江大桥” 

       ​	南京市 / 长江大桥 (BMM)
       ​	南京市长 / 江 / 大桥 (FMM)

     * **对于机械分词方法，可以建立一个一般的模型，形式地表示为:**

       **ASM(d, a, m)**，即Automatic Segmentation Model。

       d:	匹配方向，+1表示正向，-1表示逆向;
       a:	每次匹配失败后增加/减少字串长度(字符数)，+1为增字， -1为减字;
       m:	最大/最小匹配标识，+1为最大匹配，-1为最小匹配。
       例如:
       ASM(+，-， +)就是正向减字最大匹配(即FMM方法)。

       ASM(-， -， +)就是逆向减字最大匹配法(即BMM方法)。

       对于现代汉语来说，m=+1是实用的方法。

     * **最少切分分词：**

       > 使句子中切出的词数目最少。
       > 等价于在有向图中搜索最短路径问题。

  2. 基于理解的分词方法

     在分词的同时进行句法、语义分析，利用句法信息和语义信息来处理歧义现象。

  3. 基于统计的分词方法

     字与字相邻共现的频率或概率能够较好的反映成词的可信度。
     如果某两个词的组合，在概率统计上出现的几率非常大 ，那么我们就认为分词正确。

     **常用的统计语言模型：**

     * N元文法模型(N-gram)

       马尔可夫假设：
       当前状态出现的概率仅同过去有限的历史状态有关，而与其他状态无关。具体到分词任务，就是文本中第N个词出现的概率仅仅依赖于它前面的N-1个词，而与其他词无关。
       当前状态只跟(N-1)阶马尔可夫状态有关：

       | N-gram | (N-1)阶马尔可夫模型  |
       | ------ | ------------- |
       | N=1    | 一元文法模型，最大概率模型 |
       | N=2    | bigram        |
       | N=3    | trigram       |

       - Word sequences

         $$w=w_1\cdots w_n$$

       - Chain rule of probability

         $$P(w)=p(w_1)p(w_2|w_1)p(w_3|w_1w_2)\cdots p(w_n|w_1w_2\cdots w _{n-1})$$

       - Bigram approximation

         $$P(w)\approx p(w_1)p(w_2|w_1)p(w_3|w_2)\cdots p(w_n|w _{n-1})$$

       - Trigram approximation

       - Maximum Likelihood Estimate

     * 隐马尔可夫模型(HMM)



### 去除停用词

停用词-stopwords:

* 在文档中频繁出现的词语

* 与语料库特性有关

  例如，wikipedia语料库中“wiki”是停用词

为什么要去除stopwords? 

* 重复率很高，会造成索引中的posting list很长，影响查询性能
* 对最后结果的排序没什么贡献

一般可通过维护一个stopwords表来实现

### 规范化

* 词根化-Stemming

  例如:
  ran, running $$\to$$ run 
  universities $$\to$$ university

* Edit distance (编辑距离)

  > Given two strings, s and t, the edit distance, or Levenshtein Distance, between them is the minimum number of edit operations required to transform s into t.

  例如:
  Edit-Distance(“kitten”,“sitting”) = 3 

  kitten → sitten (substitution of "s" for "k")

  sitten → sittin (substitution of "i" for "e") 

  sittin → sitting (insertion of "g" at the end).

## Indexing

### 文档分析

#### 布尔检索

**最简单的检索方式**：指利用 AND, OR 或者 NOT 操作符将词项连接起来的查询

莎士比亚的哪部剧本包含Brutus及Caesar但是不包含Calpurnia?
布尔表达式为 **Brutus AND Caesar AND NOT Calpurnia**。

#### Term-Document 关联矩阵 

**(Incidence Matrix)**
关联矩阵的每一列都是 0/1向量，每个0/1 都对应一个词项

给定查询**Brutus AND Caesar AND NOT Calpurnia**，取出三个行向量 ，并对Calpurnia 的行向量求补，最后按位进行与操作：

​	**110100 AND 110111 AND 101111 = 100100**

如果文档集很大 $$\to$$ 高度稀疏(sparse)

#### 倒排索引

**IR中流行的基于词项的文本索引**

包含两部分结构: 

* Vocabulary (Dictionary) : a set of terms
* Postings List: doc list where the term appeared

倒排索引扩展:

* 扩展词项在文档中的位置 $$\to$$ 可以支持短语查询
* 搜索引擎往往在索引中加入更多的扩展项**TF(Term Freq.)、DF(Doc. Freq.)、Term Type**，以更好地支持ranking，提高查询效率

##### 倒排索引构建

**Algorithm:**

```
1. Scan each document, word by word
   Write <term, docID> pair for each word to TempIndex file
2. Sort TempIndex by terms
3. Iterate through sorted TempIndex:
   merge all entries for the same term into one postings list.
```

##### 倒排索引存储

* 一种方式:Dictionary与Postings List存储在一个文件中：

  文档规模大时导致索引过大，影响性能

* 另一种常用方式

  Dictionary与Postings List分别存储为不同的文件，通过页指针关联，Postings List文件也可以分布存储

  好处:性能

  Dictionary有可能可以常驻内存，至少可以常驻一部分 可以支持并行、分布查询

##### 词汇表存储结构

* 顺序存储
* Hash table
* B+-Tree
* Trie树

1. **词汇表的顺序排列**

   *把词汇按照字典顺序排列，词汇的查找采用二分查找*。

   优点: 实现简单
   缺点: 

   * 索引构建的效率一般
   * 对于插入的文档需要反复地调用排序和查找算法

   检索的效率一般

   * 二分查找logN的复杂度
   * 能不能变成和词汇数量无关的常数复杂度?

2. **哈希存储**

   *根据给定的词项，散列成一个整数用该整数作为词项的访问地址*。

   优点:

   * 实现简单
   * 检索速度快，理论时间O(1)

   缺点:

   * 当冲突过多时效率会下降

   * 关键在于找到一个好的散列函数

     词项的分布不均匀，且跟应用相关。

3. **语言学上的Zipf’ Law**

   *任意一个term，其频度(freq)的排名(rank)和freq的乘积大致是一个常数*。

   - Zipf’ Law具有普遍性

4. **B+-Tree存储**

   *多叉平衡有序树*

   优点:

   * 性能好而且稳定，查找时间=树高 
   * 适合外存索引

   缺点

   * 维护代价较高
   * 实现相对复杂

   **B+-Tree的效率:**

   * 设磁盘块大小8 KB，词项6 B，指针2 B，则一个块可放1024个索引项。

   | 层数   | 索引大小 (块数/大小)                 | 索引词项数             | 查找I/O |
   | ---- | ---------------------------- | ----------------- | ----- |
   | 1    | 1 / 8KB                      | 1024              | 1次    |
   | 2    | (1+1024) / 约8MB              | $$2^{20}$$ 约105万  | 2次    |
   | 3    | ($$1+2^{10}+2^{20}$$) / 约8GB | $$2^{20}$$ 约10.7亿 | 3次    |

5. **Trie树**

   *Trie树(from retrieval): 利用字符串的公共前缀来节约存储空间*。

   * Trie树是一种用于快速检索的多叉树结构。
   * Trie树把要查找的关键词看作一个字符序列。
   * 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
   * 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
   * 每个节点的所有子节点包含的字符都不相同。

   **查找时间只跟词的长度有关，而于词表中词的个数无关。当词表较大时才能体现出速度优势。**

   ![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Trie_example.svg/1200px-Trie_example.svg.png)

   优点:

   * 查找效率高，与词表长度无关。
   * 索引的插入，合并速度快。

   缺点:

   * 所需空间较大

     如果是完全m叉树，节点数指数级增长Trie树虽然不是完全m叉树，但所需空间仍然很大，尤其当词项公共前缀较少时。

   * 实现较复杂

## Queries

### 查询表达

问题: 如何准确、正确地表达用户查询?

1. A query can represent very different information needs.

  * table: furniture, data structure, ... 
  * office: a work place, software

2. A query can be a poor representation of the information need.

  * Query terms will not always appear in the index, 

    e.g., plane vs. aircraft

  * Some (new) queries are difficult to express.

**局部(Local)优化方法**: 对用户查询进行局部的分析

* 相关性反馈**relevance feedback**

**全局(Global)优化方法**: 进行一次性的全局分析(比如分析整个文档集)来产生同/近义词词典 (**thesaurus**)

* 查询扩展 query expansion

### 相关性反馈

**relevance feedback**: 用户在查询后标记相关/不相关文档，然后 (迭代)更新查询以获得更好的结果。

Motivation:

* You may not know what you’re looking for, but you’ll know when you see it.
* Query formulation may be difficult; simplify the problem through iteration.

1. User issues a (short, simple) query
2. The user marks returned documents as relevant or non-relevant.
3. The system computes a better representation of the information need based on feedback.
4. Relevance feedback can go through one or more iterations.

#### 相关性反馈如何工作?

* Let’s assume that there is an optimal query:

  The goal of relevance feedback is to bring the user query closer to the optimal query

* How does relevance feedback actually work?

  1. Use relevance information to update query
  2. Use query to retrieve new set of documents

* What exactly do we “feed back”?

  1. Boost weights of terms from relevant documents
  2. Add terms from relevant documents to the query

* Note that this is hidden from the user

#### 相关性反馈分类

* Explicit Feedback：用户显式参加交互过程。
* Implicit Feedback：系统跟踪用户的行为来推测返回文档的相关性，从而进行反馈。
* Pseudo Feedback：没有用户参与，系统直接假设返回文档的前k篇是相
  关的，然后进行反馈。**(Blind Feedback)**

#### Rocchio算法

**向量空间模型与质心: (Vector Space Model, VSM)**

每个文档表示为一个词项权重构成的向量，因此文档也可看成是多维空间中的一个点。每个查询也表示为一个词项权重构成的向量 查询处理时通过比较查询和文档向量之间的相似度进行匹配。

由于在VSM中文档表示成高维空间中的点 ，因此，我们可以采用如下方式计算文档的质心：

$$\overrightarrow{\mu}(C)=\frac{1}{|C|}\sum\limits_{\overrightarrow{d}\in C}\overrightarrow{d}$$

**Rocchio算法提供了一种将相关反馈信息融入到向量空间模型的方法：**

出发点是最大化$$sim (Q, C_r) - sim (Q, C_{nr})$$

$$\overrightarrow{Q}_{opt} = \frac{1}{|C_r|}\sum\limits_{\overrightarrow{d}_j\in C_r}\overrightarrow{d}_j - \frac{1}{N-|C_r|}\sum\limits_{\overrightarrow{d}_j\notin C_r}\overrightarrow{d}_j$$

$$Q_{opt}$$ = optimal query
$$C_r$$ = set of rel. doc vectors 
$$N$$ = collection size

**更具体一点：**

$$\overrightarrow{q}_m =\alpha\overrightarrow{q}_0 + \beta\frac{1}{|D_r|}\sum\limits_{\overrightarrow{d}_j\in D_r}\overrightarrow{d}_j - \gamma\frac{1}{|D_{nr}|}\sum\limits_{\overrightarrow{d}_j\in |D_{nr}|}\overrightarrow{d}_j$$

$$q_m$$: modified query vector

$$q_0$$: original query vector

$$α,β,γ$$: weights (hand-chosen or set empirically)

$$D_r$$: set of known relevant doc vectors

$$D_{nr}$$: set of known irrelevant doc vectors

* 新查询向相关文档靠拢而远离非相关文档。
* α vs. β/γ 设置中的折中: 如果判定的文档数目很多，那么 β/γ可以考虑设置得大一些。
* 计算过程中若出现负的权重一律设为0

> 正反馈 (positive) vs. 负反馈 (negative) 正反馈价值往往大于负反馈。
>
> 比如，可以通过设置 β = 0.75, γ = 0.25来给正反馈更大的权重。
>
> 很多系统甚至只允许正反馈，即γ = 0

**相关性反馈中的假设**
A1: 对于某初始查询，用户知道在文档集中使用哪些词项来表达：

不成立的情况：

* 用户的词汇表和文档集的词汇表不匹配

  例如: plane / aircraft;laptop / notebook

A2: 相关文档中出现的词项类似 (因此，可以基于相关反馈，从一篇相关文档到另一篇相关文档)。

不成立的情形：

* 文档子集之间使用不同的词汇表

  Burma / Myanmar(缅甸) 

* 查询的答案集合本身需要不同类的文档组成

  Pop stars that worked at Burger King

* 通用概念，通常以多个具体概念出现

  Felines 猫科

**相关性反馈存在的问题**

* 相关反馈开销很大
* 相关反馈生成的新查询往往很长
* 长查询的处理开销很大
* 用户不愿意提供显式的相关反馈
* 有时很难理解，为什么会返回(应用相关反馈之后)某篇特定文档

### 查询扩展

* In relevance feedback, users give additional input (relevant/non-relevant) on documents, which is used to reweight terms in the documents
* In query expansion, users give additional input (good/bad search term) on words or phrases

#### 方法

* Manual thesaurus

  人工构建同(近)义词词典 

  如 PubMed

* Automatically derived thesaurus 

  自动导出同(近)义词词典

  比如，基于词语的共现统计信息

* Refinements based on query log mining 

  基于查询日志挖掘出的查询等价类

  Web上很普遍

**通过分析文档集中的词项分布来自动生成thesaurus，基本的想法是计算词语之间的相似度：**

* Definition 1: Two words are similar if they co-occur with similar words.例如:“car” ≈ “motorcycle” ，因为它们都与 “road”、“gas” 及 “license”之类的词共现，因此它们相似


* Definition 2: Two words are similar if they occur in a given grammatical relation with the same words.

  例如:Entities that are grown, cooked, eaten, and digested are more likely to be food items, and thus are similar.

**query log是搜索引擎查询扩展的主要方式：**

例 1: 提交查询 [herbs] (草药)后，用户常常搜 索[herbal remedies] (草本疗法)

* “herbal remedies” 是 “herb”的潜在扩展查询

例 2: 用户搜索 [flower pix] 时常常点击URL photobucket.com/flower，而用户搜索 [flower clipart] 常常点击同样的URL

* “flower clipart”和“flower pix” 可能互为扩展查询

## Ranking

### Ranking的难点

传统IR方法有两个重要的内在假设:

* 被索引的信息本身有很高的、同等的质量，至少在信息的组织和内容上有着较高的质量
* 检索信息的用户有一定的相关技能和知识 E.g., 数字图书馆

但这些假设在Web Search上不再成立: 

* Web网页的质量参差不齐，大量的网页组织性、结构性比较差
* 大部分检索用户是没有任何经验的
* 用户的查询需求也存在着巨大差异

### IR检索模型与相关度计算

信息检索模型

* 是用来描述文档和用户查询的表示形式以及它们之间相关性的框架

 信息检索的实质问题 

* 对于所有文档，根据其与用户查询的相关程度由大到小进行排序。

 信息检索模型与搜索引擎排序算法关系

* 好的信息检索模型在相关性上产生和人类决策非常相关的结果，基于好的检索模型的排序算法能够在排序结果顶部返回相关的文档。

**信息检索模型的形式化表示: [D,Q,F,R(Di,q)] **
D:文档表达，通常表示为索引词项的集合
Q:查询表达 
F:查询与文档之间的匹配框架 
R:查询与文档之间的相关性度量函数

#### 信息检索模型的分类 

基于集合论的模型

* 布尔模型(Boolean Model) 

基于代数论的模型
* 向量空间模型(Vector Space Model)

基于概率论的模型

* 概率模型(Probabilistic Model) 
* 语言模型(Language Model) 
* 推理网络(Inference Network)

**相关性**

1.相关性
主题(topical)相关(一篇文档被判定和一个查询是同一主题) 
用户(user)相关 (考虑用户在判定相关性时涉及的所有因素) 

2.相关性
二元(binary)相关(简单判定一篇文档是相关还是非相关)
多元(multi-valued)相关 (从多个层次判断相关性)

**布尔模型**

布尔模型的前提假设: 

* 在检索到的集合中所有文档关于相关性是等价的——不考虑文档质量
* 相关性是二元的

特点

* 检索的结果只输出结果(TURE | FALSE)。 查询项被描述为布尔逻辑操作符(AND,OR,NOT)

**向量空间模型**

每篇文档表示成一个基于tf-idf权重的实值向量 D∈R|V|.
查询也同样表示为词项的tf-idf权重向量 通过文档-查询的相似度(邻近度)返回结果并排序

我们希望在同一查询下，文档集中相关度高的文档排名高于相关度低的文档：如何实现? 通常做法是对每个查询-文档对赋一个[0, 1]之间的分值，该分值度量了文档和查询的匹配程度

#### 第一种方法:Jaccard系数

$$JACCARD(A,B)=\frac{|A\cap B|}{|A\cup B|}$$

Jaccard系数的缺点

* 不考虑词项频率 ，即词项在文档中的出现次数 
* 没有仔细考虑文档的长度因素
* 罕见词比高频词的信息量更大，Jaccard系数没有考虑这个信息

####  tf、df、tf‐idf 的概念与计算

第二种方法:词项频率tf
词项t的词项频率tf(t,d)是指词项t在文档d中出现的次数
如果两者没有公共词项，则得分为0

$$\sum\limits_{t\in q\cap d}(1 + log\;tf_{t,d})$$

第三种方法:文档频率df
词项的文档频率:出现词项的文档数目

$$idf_t=log_{10}\frac{N}{df_t}$$

dft是出现词项t的文档数目
dft是和词项t的信息量成反比的一个值
于是可以定义词项t的idf权重(Inverse document frequency ):

idft 是反映词项t的信息量的一个指标
实际中往往计算[log N/ dft]而不是 [N/ dft ] ，这可以对idf的影响有所抑制
值得注意的是，对于tf 和idf我们都采用了对数计算方式

词项的tf-idf权重是tf权重和idf权重的乘积

$$w_{t,d}=(1 + log\;tf_{t,d})\cdot log\frac{N}{df_t}$$

* 随着词项频率的增大而增大
* 随着词项罕见度的增加而增大

其他叫法:tf.idf、tf *idf、tfidf等

### 向量空间模型

向量空间模型(Vector Space Model，VSM)是由G·Salton等人在1958年提出的。代表系统SMART D={D1, D2,...}，Di=(Wi1 , Wi2 , ..., Win )，Wij是词项的tf-idf权值
q= (Wq1 , Wq2 , ...,Wqn )，Wqi是查询词项的tf-idf值
F: 非完全匹配方式
R: 

* 用文档和查询两个向量相似度来估计文档和查询的相关性 
* 文档和查询之间的相关度具有较强的可计算性和可操作性，不再只有0和1两个值

前提假设 

* 在检索到的集合中所有文档关于相关性是不等价的。 
* 相关性是多元的。
* 查询关键字之间是相互独立的。

查询的表示

* 对于查询做同样的处理，即将查询表示成同一高维空间的tf-idf向量
* 按照文档对查询的邻近程度排序
* 通过邻近度来度量相似度，邻近度≈ 距离的反面

余弦相似度（向量夹角转换为夹角余弦）

$$cos(\overrightarrow{q},\overrightarrow{d})=\frac{\sum\limits_{i=1}^{|V|}q_id_i}{\sqrt{\sum\limits_{i=1}^{|V|}q_i^2}\sqrt{\sum\limits_{i=1}^{|V|}d_i^2}}$$

按照夹角从小到大排列文档 = 按照余弦从大到小排列文档

qi 是第i个词项在查询q中的tf-idf权重 
di是第i个词项在文档d中的tf-idf权重

向量空间模型总结 

* 将每篇文档表示成词项的tf-idf权重向量 
* 将查询表示成词项的tf-idf权重向量 
* 计算两个向量之间的某种相似度(如余弦相似度) 
* 按照相似度大小将文档排序
* 将Top-K(如K =10)篇文档返回给用户

### 概率模型

按照文档与给定查询的相关性概率大小进行文档排序
P(relevant | documenti, query)
通过概率的方法将查询和文档联系起来
定义3个随机变量R、Q、D:相关度R={0,1}，查询 Q={q1,q2,...}，文档D={d1,d2,...}，则可以通过计算条件概率 P(R=1|Q=q,D=d)来度量文档和查询的相关度

### PageRank

将网页或者文档视作一个点，网页之间的超链接视作有向边，则会构成一个巨大的有向图。
PageRank的核心公式为:

$$PR(a)=(1-d)+d(PR(T_1)/C(T_1)+\ldots+PR(T_n)/C(T_n))$$

* PR(a):页面a的PageRank值; 
* PR(T1):页面T1的PageRank值，页面T1链向a; 
* C(T1):页面T1链出的链接数量; d:阻尼系数，取值在0-1之间

PageRank 是基于「从许多优质的网页链接过来的网页，必定还是优质网页」的回归关系，来判定所有网页的重要性
递归结束标志:|Ranki+1 - Ranki| < 阈值

基于链接分析的全局网页排序算法
优点 

* 对互联网上的网页给出了一个全局的重要性排序，并且算法的计算过程是可以离线完成的，这样有利于迅速响应用户的请求 
* 独立于检索主题，因此也常被称为Query-independent算法

缺点 

* 主题无关性，没有区分页面内的导航链接、广告链接和功能链接等，容易对广告页面有过高评价
* 旧网页容易比新网页排名高，因为新网页通常不会有很多入链

    一般不能单独用于排序，需要跟内容排序方法相结合(例如:tf-idf)

### HITS

PageRank算法中对于向外链接的权值贡献是平均的，也就是不考虑不同链接的重要性。而
Web的链接具有以下特征: 

* 有些链接具有注释性，也有些链接是起导航或广告作用。有注释性的链接更具有权威性。
* 基于商业或竞争因素考虑，很少有Web网页指向其竞争领域的权威网页。
* 权威网页很少具有显式的描述，比如Google主页不会明确给出Web搜索引擎之类的描述信息。

总之，平均的分布权值不符合链接的实际情况

HITS ( Hyperlink - Induced Topic Search )

**权威(Authority)网页**:与某个领域或者某个话题相关的高质量网页。
比如搜索引擎领域，Google和百度首页即该领域的高质量网页 ，比如视频领域，优酷和土豆首页即该领域的高质量网页
**Hub网页**:包含了很多指向高质量“Authority”页面链 接的网页, hao123首页可以认为是一个典型的高质量“Hub”网页。

HITS算法的目的即是在海量网页中找到与用户查询主题相关的高质量  “Authority”页面和“Hub”页面，尤其是 “Authority”页面，因为这些页面代表了能够满足用户查询的高质量内容

基本假设1
一个好的“Authority”页面会被很多好的“Hub”页 面指向;
基本假设2: 
一个好的“Hub”页面会指向很多好的“Authority”页面;

基于权威度和中心度的网页排序算法
优点 

* 能更好地描述互联网的组织特点 
* 主题相关，Query-dependent 
* 可以单独用于网页排序

缺点

* 需要在线计算，时间代价较大
* 容易受到“链接作弊”的影响
  * 例如，你可以做一个网页链接许多领域内所有的权威网页，就成为了一个hub网页， 然后在该页面里放个链接指向自己的一个 普通网页，此网页authority值会大大提升
* 稳定性较差，容易被链接结构变化影响

## Evaluation

IR评价概述
最主要的两个方面
效率 (Efficiency)—可以采用通常的评价方法 

* 时间开销、空间开销、响应速度

效果 (Effectiveness) 
* 返回的文档中有多少相关文档 
* 所有相关文档中返回了多少 
* 返回得靠不靠前

其他指标 

* 覆盖率(Coverage)
* 访问量 
* 数据更新速度

评价指标:某个或某几个可衡量、可比较的值 
正确率
召回率
F-measure 
MAP
MRR 
NDCG

### Effectiveness metrics

对单个查询进行评估的指标

	基于集合的评价指标 
		正确率(Precision)、召回率(Recall)、F值
	基于序的评价指标
		P@N、R-Precision、AP等 
对多个查询进行评估的指标
* MAP、MRR

其它的评价指标
* NDCG

正确率(Precision) RR/(RR + RN)
返回的结果中真正相关结果的比率，也称为查准率，P∈ [0,1]

召回率(Recall) RR/(RR + NR)
返回的相关结果数占实际相关结果总数的比率，也称为查全率，R∈ [0,1]

F值(F-measure):召回率R和正确率P的调和平均值，
if P=0 or R=0, then F=0, else 采用下式计算:
更一般的情况——Fβ:参数β用于调节召回率和正确率的 相对重要程度。

$$F=\frac{2}{\frac{1}{P}+\frac{1}{R}}$$

$$F_\beta=\frac{(1+\beta^2)PR}{\beta^2{P}+{R}}$$

正确率和召回率的问题
两个指标都是基于(无序)集合进行计算，并没有考虑序的作用
举例:两个系统对某查询都返回20个文档，其中相关文档数都是10，但第一个系统是前10条结果，后一个系统是后10条结果。显然第一个系统优。
但两者的P和R一样。

解决方法:引入序的作用

**P@N**
指在第N个位置上的正确率(看前N个)

**R-Precision**
检索结果中，在所有相关文档总数(m)位置上的正确率
correct_m/m

**平均正确率(Average Precision, AP):**
对不同召回率点上的正确率进行平均
未插值的AP: 某个查询Q共有6个相关结果，某系统排序返回了5篇相关文档，其位置分别是第1，第2，第5，第10，第20位 ，则AP=(1/1+2/2+3/5+4/10+5/20+0)/6
插值的AP: 在召回率分别为0,0.1,0.2,...,1.0的十一个点上的正确率求平均，等价于11点平均
由于每个查询的召回率值不一定就是这11个标准召回率，因此需要 \对正确率进行插补。
对于t%，如果不存在该召回率点，则定义t%为从t%到 (t+10)%中最大的正确率值。
召回率100%点若不存在，正确率可近似为0
简化的AP: 只对返回的相关文档进行计算的AP, AP=(1/1+2/2+3/5+4/10+5/20)/5，倾向那些快速返回 结果的系统，没有考虑召回率

**MAP(Mean AP) **
对所有查询的AP求算术平均 反映在全部查询上的检索效果

**MRR(Mean Reciprocal Rank)**
对于某些IR系统(如问答系统或主页发现系统)，只关心第一个标准答案返回的位置(Rank)，越前越好， 这个位置的倒数称为RR，对问题集合求平均，则得 到MRR
两个问题，系统对第一个问题返回的标准答案Rank是2，对第二个问题返回的标准答案的Rank是4 则系统的MRR= (1/2+1/4)/2=3/8 意味着平均在第8/3个位置处找到相关文档

DCG 列的相关度加和的思路来衡量。
两个假设 
相关度级别越高的结果越多越好 
相关度级别越高的结果越靠前越好

CG(Cumulative Gain) :位于位置1 到p 的检索结果的相关度之和。

reli 表示第 i 个文档与查询的相关度 可以不仅仅只有相关1和不相关0两种情况，允许有多个相关度级别，比如0, 1, 2, 3
特点
CG得分高只能说明检索结果的总体质量比较高
但不能说明结果排序的好坏:CG未考虑相关结果的位置，
即前p项中两文档交换不影响计算结果

DCG(Discounted Cumulative Gain)


NDCG (Normalized DCG):对DCG进行规范化

$$CG_p = \sum_{i=1}^p rel_i$$

$$discounted\;gain = \frac{rel_i}{log_2{i}}$$

$$DCG = rel_1 + \sum discounted\;gain $$

理想结果

$$IDCG = rel_1^1 + \sum discounted\;gain\,^{1} $$

$$NDCG_p= \frac{DCG_p}{IDGC_p}$$

## Data

## Classification

Comments on kNN
Instance-based learning: kNN – a Nonparametric(无参数的) classifier
A nonparametric method does not rely on any assumption concerning the structure of the underlying density function.
Very little “learning” is involved in these methods
Sample size

* The more the better
* Need efficient search algorithm for NN

Good news:
Simple and powerful methods; Flexible and easy to apply to many problems.
Bad news:
High memory requirements
Very dependant on the scale factor for a specific problem.

### Classification Errors

* Training errors (apparent errors) — 训练误差 

  Errors committed on the training set

* Test errors — 测试误差

  Errors committed on the test set

* Generalization errors — 泛化误差

   Expected error of a model over random selection of records from same distribution(未知记录上的期望误差)

## Clustering

* Group the data objects into subsets or “clusters”:
* High similarity within clusters
* Low similarity between clusters

Hierarchical Clustering 层次聚类

**Bottom-Up agglomerative clustering(合并式聚类)**

* Starts with each obj in a separate cluster
* then repeatedly joins the closest pair of clusters, until there is only one cluster.
* The history of merging forms a binary tree or hierarchy.

**Top-Down divisive clustering(分裂式聚类)**

* Starting with all the data in a single cluster,
* Consider every possible way to divide the cluster into two. Choose the best division
* And recursively operate on both sides.

The distance between two clusters is defined as the distance between

* Single-Link
* Nearest Neighbor: their closest members
* Complete-Link
* Furthest Neighbor: their furthest members
* Centroid
* Centers of gravity
* Average-Link
* Average of all cross-cluster pairs

### K-Means Clustering

1. Decide on a value for k.
2. Initialize the k cluster centers (randomly, if necessary).
3. Decide the class memberships of the N objects by assigning them to the nearest cluster center.
4. Re-estimate the k cluster centers, by assuming the memberships found above are correct.
5. If none of the n objects changed membership in the last iteration, exit. Otherwise goto 3.

At each iteration,

* Computing distance between each of the n objects and the k cluster centers is O(knd), if d is the number of dimensions.
* Computing centroids: Each object gets added once to some centroid: O(nd).

Assume these two steps are each done once for L iterations: O(Lknd).

**What is K-Means optimizing?**

Potential function F (1; C ) of centers 1 and point
allocations(分配) C:



## 网络结构挖掘(Web Structure Mining)

### Structure of Networks

* Objects: nodes, vertices
* Interactions: links, edges
* System: network, graph

$$G=(V, E)$$

### Communities

Granovetter’s theory (and common sense) suggest that networks are composed of tightly connected sets of nodes

Network Communities(社区)

Sets of nodes with lots of connections inside and few to outside (the rest of the network)

#### Finding Network Communities

Community Detection Methods

Connection between community detection and clustering

* Agglomerative 

  hierarchical clustering 

* Partitional clustering

  K-means

Based on Structural Similarity

**Vertex Similarity**

A: adjacency matrix of undirected G 

$$A_{ij}=1$$，if (i,j) is an edge, else 0

* Structural dissimilarity measure 
* Jaccard similarity
* Cosine similarity

**Divisive hierarchical algorithm – Girvan and Newman**

* Spectral graph cut
* Modularity maximization

**Divisive Removal of Weak Ties/Bridges**

* Bridges: Form part of the shortest path between pairs of nodes in different parts of the network


* Simple idea: Remove bridges and local bridges

Generalize the Role of Bridges: Look for the edges that carry the most of “traffic” in a network

* Without the edge, paths between many pairs of nodes may have to be “re-routed” a longer way
* Edges to link different densely-connected regions
* Good candidates for removal in a divisive method
* Generalize the (local) bridges

For nodes A and B connected by a path assume 1 unit of “flow” (If A and B in different connected components, flow = 0)

* Divide flow evenly along all possible shortest paths from A to B
* if k shortest paths from A and B, then 1/k units of flow pass along each

**Edge Betweenness: **

Betweenness of an edge: the total amount of flow it carries

counting flow between all pairs of nodes using this edge

**Girvan-Newman:**

*Divisive hierarchical clustering based on the notion of edge betweenness: Number of shortest paths passing through the edge*

Undirected unweighted networks Repeat until no edges are left:

1. Calculate betweenness of edges (O(mn), or O(n2) on a sparse graph, with breadth-first-search)
2. Remove edges with highest betweenness
3. Connected components are communities
4. Gives a hierarchical decomposition of the network

### Basic Graph Notation

A graph G = (V,E) consists of vertices V and edges E
Edges can be weighted or unweighted.

#### The adjacency matrix 

邻接矩阵 A (weight matrix W) describes the graph:

* Aij=0 (Wij=0) if vertices i and j are not connected


* Aij=1 (Wij=weight of the edge), if they are connected

**The degree of a vertex is the sum of all adjacent edge weights: di= ΣjAij (ΣjWij )**

All vertices which can be reached from each other by a path form a connected component(连通分支)

Graph Partitioning：

What makes a good partition?

* Maximize the number of within- group connections
* Minimize the number of between- group connections

What makes a good clustering?

* Points assigned to the same cluster should be highly similar
* Points assigned to different clusters should be highly dissimilar

### Graph Cuts

Express partitioning objectives as a function of the “edge cut” of the partition.
Cut: Set of edges with only one vertex in a group.

$$cut(A,B)=\sum\limits_{i\in A, j\in B}w_{ij}$$

The partitioning can be described by a labeling function y that indicates the two joint sets：

见推导

**Min-cut：**minimize weight of connections between groups：

Easy to solve: O(|V| |E|) algorithm

* Not satisfactory partition – often isolates(孤立) vertices
  * Only consider sexternal cluster connections
  * Does not consider internal cluster density

**Balanced min-cut：**

$$min\,cut(A,B)\mbox{  subject to } |A|=|B|$$

**Ratio cut：**

$$RatioCut(A,B)=cut(A,B)(\frac{1}{|A|}+\frac{1}{|B|})$$

**Normalized cut：**

$$N\,cut(A,B)=cut(A,B)(\frac{1}{vol(A)}+\frac{1}{vol(B)})$$

$$vol(A)=\sum\limits_{i\in A}d_i$$

> NP-hard to solve!

Spectral clustering is a relaxation of these

Both ratio cut and normalized cut prefer a balanced partition

**Derivation of Normalized cut cont’d: **

见推导

However, we still have the discrete constraint on g, which causes NP-hardness.

**Relaxation(松弛):**

见推导

**Spectral Graph Theory**

Possible approach

* Represent a similarity graph as a matrix
* Apply knowledge from Linear Algebra...

1. How to define a “good” partition of the graph? 
   * Minimize a given graph cut criterion
2. How to efficiently identify such a partition?
   * Approximate using information provided by the eigenvalues and eigenvectors of a graph

**Spectral Clustering**

1. Construct the affinity matrix W from data (A in community detection setting)

2. Compute the second vector of the matrix

   - Ratio Cut：$$D-W$$
   - Normalized Cut：$$D^{-\frac{1}{2}}(D-W)D^{-\frac{1}{2}}$$

3. Taking the signs of the second eigenvector and cluster the

   data

**Spectral Clustering Algorithms**

Three basic stages:

1) Pre-processing: Construct a matrix representation of the graph

2) Decomposition:

* Compute eigenvalues and eigenvectors of the matrix
* Map each point to a lower-dimensional representation based on one or more eigenvectors

3) Grouping

* Assign points to two or more clusters, based on the newrepresentation

**K-way Spectral Clustering**

Two basic approaches:

Recursive bi-partitioning

- Recursively apply bi-partitioning algorithm in a hierarchical divisive

  manner

- Disadvantages: Inefficient, unstable

Cluster multiple eigenvectors

* Build a reduced space from multiple eigenvectors
* Commonly used in recent papers
* A preferable approach...

**Three basic stages:**

**1) Pre-processing**
Construct a matrix representation of the graph (nxn matrix)

**2) Decomposition**
Compute eigenvalues and eigenvectors of the matrix
Map each point to a lower-dimensional representation based on
smallest k eigenvectors (V: nxk matrix) 

**3) Grouping**
Assign points to k clusters, by running k-means on the new representation (kmeans(V, k))

### Modularity

Communities: set of tightly connected nodes
Define: Modularity Q

* A measure of how well a network is partitioned into communities

Given a partitioning of the network into groups

Q ∝ ∑s∈S [(# edges within group s) − (expected # edges within group s)]

Null Model: Configuration Model

Given real G on n nodes and m edges, construct rewired network G’

* Same degree distribution but random connections
* Consider G’ as a multigraph

The expected number of edges between nodes i and j of degrees di and dj equals to:

$$\frac{d_id_j}{2m}$$

Modularity of partitioning S of graph G:

见推导

Modularity values take range [-1, 1]

* It is positive if the number of edges within groups exceeds the expected number
* 0.3<Q<0.7 means significant community struct

Modularity is useful for selecting the number of clusters: Modularity Q tells us whether S represents any significant community structure

Simple case: split the graph into 2 communities

1. The partitioning can be described by a labeling function y that indicates the two joint sets
2. Optimal solution: top (biggest) eigenvector(s) of the modularity matrix
3. Apply k-means as a post-processing step to obtain k-way community partition

## Influence

**Importance of Nodes**

* Not all nodes are equally important
* Centrality(核心性，中心效应) Analysis:
  * Find out the most important nodes in one network
* Commonly-used Measures
  * Degree Centrality
  * Closeness Centrality
  * Betweenness Centrality
  * Eigenvector Centrality

### 度量节点中心性的标准

1. Degree Centrality

   **The importance of a node is determined by the number of nodes adjacent to it**

   * The larger the degree, the more important the node is
   * Only a small number of nodes have high degrees in many reallife networks

   $$C_D(v_i)=d_i=\sum\limits_{j}A_{ij}$$

   Normalized degree centrality: 除以n-1

2. Closeness Centrality

   “Central” nodes are important, as they can reach the whole network more quickly than non-central nodes
   Importance measured by how close a node is to other nodes

   Average Distance: $$D_{avg}(v_i)=\frac{1}{n-1}\sum\limits_{j\neq i}^{n}g(v_i,v_j)$$

   * g(vi; vj ) denotes the geodesic distance(测地距离) between nodes vi; vj

   Closeness Centrality: 

   $$C_{c}(v_i)=D_{avg}(v_i)^{-1}$$

3. Betweenness Centrality

   Nodes betweenness counts the number of shortest paths that pass one node
   Nodes with high betweeness are important in communication and information diffusion

   $$C_B(v_i)=\sum\limits_{v_s\neq v_i\neq v_t\in V,s<t}\frac{\sigma_{st}(v_i)}{\sigma_{st}}$$

   $$\sigma_{st}$$: The number of shortest paths between s and t
   $$\sigma_{st}(v_i)$$: The number of shortest paths between s and t that passes $$v_i$$

   Normalized betweenness centrality: 除以(n-1)(n-2)/2

4. Eigenvector Centrality

   One’s importance is determined by his friends’ importance
   If one has many important friends, he should be important as
   well.

   $$C_E(v_i)\propto \sum\limits_jA_{ij}C_E(v_i)$$

   Let x denote the eigenvector centrality of node from v1 to vn

   $$x\propto Ax \to Ax=\lambda x $$

   The centrality corresponds to the top eigenvector of the adjacency matrix A.

   A variant of this eigenvector centrality is the PageRank score.

Degree Centrality

* easy

Closeness Centrality

* Time: O(n2); Space: O(n3) (Floy, 1962)
* Time: O(n2logn+nm) (Johnson, 1977)

Betweenness Centrality

* O(n2) (O(nm) with sparsity)

Eigenvector Centrality

* Power method (Golub and Van Loan, 1996)

**Neighborhood Overlap:**

Tie Strength can be measured based on neighborhood overlap; the larger the overlap, the stronger the tie is.

**Learning from User Activities**

* One might learn how one influences his friends if the user activity log is accessible
* Depending on the adopted influence model
  * Independent cascading model
  * Linear threshold model
* Maximizing the likelihood of user activity given an influence model

### Linear threshold model (LTM)

An actor would take an action if the number of his friends who have taken the action exceeds (reaches) a certain threshold

* Each node v chooses a threshold Θv randomly from a uniform distribution in an interval between 0 and 1.

* A neighbor w can influence node v with strength bw,v

* In each discrete step, all nodes that were active in the previous step remain active

* The nodes satisfying the following condition will be activated

  $$\sum\limits_{w\in N_v, w\,is\,active}b_{w,v}\geq \theta_v$$

###  cascade model (ICM)

The independent cascade model focuses on the sender’s rather than the receiver’s view

* A node w, once activated at step t , has one chance to activate each of its neighbors randomly

  For a neighboring node (say, v), the activation succeeds with probability pw,v(e.g. p = 0.5)

* If the activation succeeds, then v will become active at step t+1

* In the subsequent rounds, w will not attempt to activate v anymore.

* The diffusion process, starts with an initial activated set of nodes, then continues until no further activation is possible

Two basic models used to study influence and informationdiffusion

- LTM: receiver-centered; 

  ICM: sender-centered

- LTM’s activation depends on the whole neighborhood of

  one node

  ICM activates each of its neighbors independently

- Once the thresholds are sampled, the LTM diffusionprocess is determined

  ICM varies depending on the cascading process

- Both are submodular (基数越大，增长效果越差)

  - f is submodular(子模性): (activating each additional node helps

    less)

### Most Influential Set of Nodes

S: is initial active set
f(S): The expected size of final active set
Set S is more influential if f(S) is larger

见推导

How hard is this problem?

* NP-COMPLETE
* Finding most influential set is at least as hard as a vertex cover
* There exists an approximate algorithm

**Consider the Hill Climbing algorithm to find S：**

见推导

Where f(S) ≥(1-1/e)\*OPT (f(S)>0.63\*OPT)

## Recommendation

Formal Model:

$$X \times S \to R$$ 

X: set of costomers

S: set of items

R: set of ratings

**Gathering Ratings:**

Explicit

* Ask people to rate items
* Doesn’t work well in practice – people can’t be bothered

Implicit

* Learn ratings from user actions

  e.g., purchase implies high rating

* What about low ratings?

**Extrapolating Utilities:**

Key problem: matrix U is sparse

* Most people have not rated most items

* Cold start:

  New items have no ratings

  New users have no history

Three approaches to recommender systems

* Content-based
* Collaborative
* Latent factor based

### Content-based Recommendations

Main idea: recommend items to customer x similar to previous items rated highly by x

Item Profiles

* For each item, create an item profile

* Profile is a set (vector) of features

  Movies: author, title, actor, director,...

  Text: set of “important” words in document

* How to pick important words?

  Usual heuristic from text mining is TF.IDF (Term Frequency * Inverse Doc Frequency)

  * Term ... Feature
  * Document ... Item

Pros: Content-based Approach

* No need for data on other users

  No cold-start or sparsity problems

* Able to recommend users with unique tastes

* Able to recommend new and unpopular items

  No first-rater problem

* Able to provide explanations

  Can provide explanations of recommended items by listing content-features that caused an item to be recommended

Cons: Content-based Approach

* Finding the appropriate features is hard

  E.g., images, movies, music

* Overspecialization

  * Never recommends items outside user’s content profile
  * People might have multiple interests
  * Unable to exploit quality judgments of other users

* Recommendations for new users

  * How to build a user profile?

### Collaborative Filtering(协同过滤)

1. Consider user x
2. Find set N of other users whose ratings are “similar” to x’s ratings
3. Estimate x’s ratings based on ratings of users in N

见推导

Item-Item vs. User-User

* In practice, it has been observed that item-item often works better than user-user

* Why?

  Items are simpler, users have multiple tastes

Pros and Cons of Collaborative Filtering

* Works for any kind of item

  No feature selection needed

* Cold start:

  Need enough users in the system to find a match

* Sparsity:

  The user/ratings matrix is sparse

  Hard to find users that have rated the same items

* First rater:

  Cannot recommend an item that has not been previously rated

  New items, esoteric items

* Popularity bias:

  Cannot recommend items to someone with unique taste

  Tends to recommend popular items

Collaborative Filtering: Complexity

* Expensive step is finding k most similar customers: O(|X|)

* Too expensive to do at runtime 

  Need to pre-compute

* Naïve pre-computation takes time O(|S|· |X|)

  Near-neighbor search in high dimensions 

* Can use clustering, partitioning as alternatives, but quality degrades

## 矩阵求导

见推导
