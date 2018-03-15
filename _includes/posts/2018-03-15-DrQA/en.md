## Reading Wikipedia to Answer Open-Domain Questions

[facebook开源github](https://github.com/facebookresearch/DrQA)

This paper proposes to tackle ***open-domain question answering*** using Wikipedia as the unique knowledge source: the answer to any factoid question is a text span in a Wikipedia article. This task of machine reading at scale combines the challenges of ***document retrieval*** (finding the relevant articles) with that of ***machine comprehension of text*** (identifying the answer spans from those articles).

We develop ***DrQA***, a strong system for question answering from Wikipedia composed of: (1) ***Document Retriever***, a module using bigram hashing and TF-IDF matching designed to, given a question, efficiently return a subset of relevant articles and (2) ***Document Reader***, a multi-layer recurrent neural network machine comprehension model trained to detect answer spans in those few returned documents. 

![](http://img.blog.csdn.net/20170910201351850?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3BhcmtleHBlcnQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Our experiments on multiple existing QA datasets indicate that ***(1) both modules are highly competitive with respect to existing counterparts***and ***(2) multitask learning using distant supervision on their combination is an effective complete system on this challenging task***.

> Wikipedia contains up-to-date knowledge that humans are interested in. It is designed, how- ever, for humans – not machines – to read.

Open-domain QA was originally defined as finding answers in collections of unstructured documents, following the setting of [the annual TREC competitions](http://trec.nist.gov/data/qamain.html).

### Document Retriever

A simple ***inverted index lookup*** followed by ***term vector model scoring*** performs quite well on this task for many question types, compared to the built-in ElasticSearch based Wikipedia Search API (Gormley and Tong, 2015). Articles and questions are compared as ***TF-IDF weighted bag-of- word vectors***. We further improve our system by taking local word order into account with ***n-gram features***.

Our best performing system uses bigram counts while preserving speed and memory efficiency by using the hashing of ***(Weinberger et al., 2009)*** to map the bigrams to 224 bins with an unsigned murmur3 hash.

### Document Reader

Our Document Reader model is inspired by the recent success of neural network models on machine comprehension tasks, in a similar spirit to the ***AttentiveReader*** described in ***(Hermann et al., 2015; Chen et al., 2016)***.

*a question q consisting of $$l$$ tokens $$\{q_1,…,q_l\}$$*

*a document or a small set of documents of n paragraphs where a single paragraph $$p$$ consists of m tokens $${p_1, . . . , p_m}$$*

#### - Paragraph encoding

We first represent all tokens $$p_i$$ in a paragraph $$p$$ as a sequence of feature vectors $$\tilde{\mathbf{p}}_i \in \mathbb{R}^d$$ and pass them as the input to a recurrent neural network and thus obtain:

$$\{\mathbf{p}_1,…,\mathbf{p}_m\} = RNN(\{\tilde{\mathbf{p}}_1,…,\tilde{\mathbf{p}}_m\})$$

where $$\mathbf{p}_i$$ is expected to encode useful context information around token $$p_i$$. Specifically, we choose to use a multi-layer bidirectional long short-term memory network (LSTM), and take $$\mathbf{p}_i$$ as the concatenation of each layer’s hidden units in the end.

The feature vector $$\tilde{\mathbf{p}}_i$$ is comprised of the following parts:

* Word embeddings: $$f_{emb} (p_i ) = \mathbf{E}(pi )$$

  We use the 300-dimensional Glove word embeddings trained from 840B Web crawl data (Pennington et al., 2014). We keep most of the pre-trained word embeddings fixed and only fine-tune the 1000 most frequent question words because the representations of some key words such as what, how, which, many could be crucial for QA systems.

* Exact match: $$f_{exact\_match}(p_i) = \mathbb{I}(p_i \in q)$$

  We use three simple binary features, indicating whether $$p_i$$ can be exactly matched to one question word in $$q$$, either in its original, lowercase or lemma form.

* Token features: $$f_{token}(p_i) = (POS(p_i), NER(p_i), TF(p_i))$$

  We also add a few manual features which reflect some properties of token $$p_i$$ in its context, which include its part-of-speech (POS) and named entity recognition (NER) tags and its (normalized) term frequency (TF).

* Aligned question embedding: 

  Following (Lee et al., 2016) and other recent works, the last part we incorporate is an aligned question embedding $$f_{align}(p_i) = \sum_j a_{i,j} \mathbf{E}(q_j)$$, where the attention score $$a_{i,j}$$ captures the similarity between $$pi$$ and each question words $$q_j$$. Specifically, $$a_{i,j}$$ is computed by the dot products between nonlinear mappings of word embeddings:

  $$a_{i,j} = \frac{exp(\alpha(\mathbf{E}(p_i) \cdot \alpha(\mathbf{E}(q_j)))}{\sum_{j'} exp(\alpha(\mathbf{E}(p_i) \cdot \alpha(\mathbf{E}(q_{j'})))}$$

  and $$\alpha(·)$$ is a single dense layer with ReLU nonlinearity. Compared to the exact match features, these features add soft alignments between similar but non-identical words (e.g., car and vehicle).   

#### - Question encoding

The question encoding is simpler, as we only apply another recurrent neural network on top of the word embeddings of $$q_i$$ and combine the resulting hidden units into one single vector: $$\{\mathbf{q}_1,…,\mathbf{q}_l\} \to \mathbf{q}$$. We compute $$\mathbf{q} = \sum_j b_j\mathbf{q}_j$$ where $$bj$$ encodes the importance of each question word:

$$b_j = \frac{exp(\mathbf{w \cdot q_j})}{\sum_{j'} exp(\mathbf{w \cdot q_{j'})}}$$

and $$\mathbf{w}$$ is a weight vector to learn.

#### - Prediction

At the paragraph level, the goal is to predict the span of tokens that is most likely the correct answer. We take the the paragraph vectors $$\{\mathbf{p}_1,…,\mathbf{p}_m\}$$ and the question vector $$\mathbf{q}$$ as input, and simply train two classifiers independently for predicting the two ends of the span. Concretely, we use a bilinear term to capture the similarity between $$\mathbf{p}_i$$ and $$\mathbf{q}$$ and compute the probabilities of each token being start and end as:

$$P_{start}(i) \in exp (\mathbf{p_iW_sq}) $$

$$P_{end}(i) \in exp (\mathbf{p_iW_eq}) $$

During prediction, we choose the best span from token $$i$$ to token $$i'$$ such that $$i \le i' \le i+15 $$ and $$P_{start}(i) \times P_{end}(i')$$ is maximized. To make scores compatible across paragraphs in one or several retrieved documents, we use the unnormalized exponential and take argmax over all considered paragraph spans for our final prediction.

### Data

Our work relies on three types of data: ***(1) Wikipedia that serves as our knowledge source for finding answers***, ***(2) the SQuAD dataset which is our main resource to train Document Reader*** and ***(3) three more QA datasets (CuratedTREC, We- bQuestions and WikiMovies)*** that in addition to SQuAD, are used to test the open-domain QA abilities of our full system, and to evaluate the ability of our model to learn from multitask learning and distant supervision.

#### - Wikipedia (Knowledge Source)

We use the [2016-12-21 dump of English Wikipedia](https://dumps.wikimedia.org/enwiki/ latest) for all of our full-scale experiments as the knowledge source used to answer questions. For each page, only the plain text is [extracted](https://github. com/attardi/wikiextractor.) and all structured data sections such as lists and figures are stripped. After discarding internal disambiguation, list, index, and outline pages, we retain 5,075,182 articles consisting of 9,008,962 unique uncased token types.

#### - SQuAD

The Stanford Question Answering Dataset (SQuAD) (Rajpurkar et al., 2016) is a dataset for machine comprehension based on Wikipedia. The dataset contains 87k examples for training and 10k for development, with a large hidden test set which can only be accessed by the SQuAD creators.

#### - Open-domain QA Evaluation Resources

SQuAD is one of the largest general purpose QA datasets currently available. SQuAD questions have been collected via a process involving showing a paragraph to each human annotator and asking them to write a question. As a result, their distribution is quite specific. We hence propose to train and evaluate our system on other datasets developed for open-domain QA that have been constructed in different ways (not necessarily in the context of answering from Wikipedia).

#### - Distantly Supervised Data

All the QA datasets presented above contain train- ing portions, but CuratedTREC, WebQuestions and WikiMovies only contain question-answer pairs, and not an associated document or paragraph as in SQuAD, and hence cannot be used for training Document Reader directly. Following previous work on distant supervision (DS) for relation extraction (Mintz et al., 2009), we use a procedure to automatically associate paragraphs to such training examples, and then add these examples to our training set.

### Experiments

This section first presents evaluations of our Document Retriever and Document Reader modules separately, and then describes tests of their combination, DrQA, for open-domain QA on the full Wikipedia.

#### - Finding Relevant Articles

Results on all datasets indicate that our simple approach outperforms [Wikipedia Search](https://www. mediawiki.org/wiki/API:Search.), especially with bigram hashing. We also compare doing retrieval with Okapi BM25 or by using cosine distance in the word embeddings space (by encoding questions and articles as bag-of-embeddings), both of which we find performed worse.

#### - Reader Evaluation on SQuAD

Next we evaluate our Document Reader component on the standard SQuAD evaluation (Rajpurkar et al., 2016).

**Implementation details:**  We use 3-layer bidirectional LSTMs with $$h = 128$$ hidden units for both paragraph and question encoding. We apply the Stanford CoreNLP toolkit (Manning et al., 2014) for tokenization and also generating lemma, part- of-speech, and named entity tags.
Lastly, all the training examples are sorted by the length of paragraph and divided into mini- batches of 32 examples each. We use Adamax for optimization as described in (Kingma and Ba, 2014). Dropout with $$p = 0.3$$ is applied to word embeddings and all the hidden units of LSTMs.
**Result and analysis:**  SQuAD has been a very competitive machine comprehension benchmark since its creation and we only list the best-performing systems in the table. Our system (single model) can achieve $$70.0\%$$ exact match and $$79.0\%$$ F1 scores on the test set, which surpasses all the published results and can match the top performance on the SQuAD leaderboard at the time of writing. Additionally, we think that our model is conceptually simpler than most of the existing systems. We conducted an ablation analysis on the feature vector of paragraph tokens. Without the aligned question embedding feature (only word embedding and a few manual features), our system is still able to achieve F1 over $$77\%$$. More interestingly, if we remove both $$f_{aligned}$$ and $$f_{exact\_match}$$ , the performance drops dramatically, so we conclude that both features play a similar but complementary role in the feature representa- tion related to the paraphrased nature of a question vs. the context around an answer.

#### - Full Wikipedia Question Answering

We compare three versions of DrQA which evaluate the impact of using distant supervision and multitask learning across the training sources provided to Document Reader (Document Retriever remains the same for each case):
• SQuAD: AsingleDocumentReadermodelis trained on the SQuAD training set only and used on all evaluation sets.
• Fine-tune (DS): A Document Reader model is pre-trained on SQuAD and then fine-tuned for each dataset independently using its distant supervision (DS) training set.
• Multitask (DS): A single Document Reader model is jointly trained on the SQuAD training set and all the DS sources.

### Conclusion

We studied the task of machine reading at scale, by using Wikipedia as the unique knowledge source for open-domain QA. Our results indicate that MRS is a key challenging task for researchers to focus on. Machine comprehension systems alone cannot solve the overall task. Our method integrates search, distant supervision, and multitask learning to provide an effective complete system. Evaluating the individual components as well as the full system across multiple benchmarks showed the efficacy of our approach.

Future work should aim to improve over our DrQA system. Two obvious angles of attack are: ***(i) incorporate the fact that Document Reader aggregates over multiple paragraphs and documents directly in the training, as it currently trains on paragraphs independently;*** and ***(ii) perform end- to-end training across the Document Retriever and Document Reader pipeline, rather than independent systems***.

### Installing DrQA

*Setting up DrQA is easy!*

DrQA requires Linux/OSX and Python 3.5 or higher. It also requires installing [PyTorch](http://pytorch.org/). Its other dependencies are listed in requirements.txt. CUDA is strongly recommended for speed, but not necessary.

Run the following commands to clone the repository and install DrQA:

```shell
git clone https://github.com/facebookresearch/DrQA.git
cd DrQA; pip install -r requirements.txt; python setup.py develop
```

Note: requirements.txt includes a subset of all the possible required packages. Depending on what you want to run, you might need to install an extra package (e.g. spacy).

If you use the CoreNLPTokenizer or SpacyTokenizer you also need to download the Stanford CoreNLP jars and spaCy `en`model, respectively. If you use Stanford CoreNLP, have the jars in your java `CLASSPATH` environment variable, or set the path programmatically with:

```python
import drqa.tokenizers
drqa.tokenizers.set_default('corenlp_classpath', '/your/corenlp/classpath/*')
```

**IMPORTANT: The default tokenizer is CoreNLP so you will need that in your CLASSPATH to run the README examples.**

Ex: `export CLASSPATH=$CLASSPATH:/path/to/corenlp/download/*`.

If you do not already have a CoreNLP [download](https://stanfordnlp.github.io/CoreNLP/index.html#download) you can run:

```Shell
./install_corenlp.sh
```

Verify that it runs:

```python
from drqa.tokenizers import CoreNLPTokenizer
tok = CoreNLPTokenizer()
tok.tokenize('hello world').words()c  # Should complete immediately
```

For convenience, the Document Reader, Retriever, and Pipeline modules will try to load default models if no model argument is given. See below for downloading these models.

### Trained Models and Data

To download all provided trained models and data for Wikipedia question answering, run:

```shell
./download.sh
```

*Warning: this downloads a 7.5GB tarball (25GB untarred) and will take some time.*

This stores the data in `data/` at the file paths specified in the various modules' defaults. This top-level directory can be modified by setting a `DRQA_DATA` environment variable to point to somewhere else.

Default directory structure (see [embeddings](https://github.com/facebookresearch/DrQA/blob/master/scripts/reader/README.md#note-on-word-embeddings) for more info on additional downloads for training):

```
DrQA
├── data (or $DRQA_DATA)
    ├── datasets
    │   ├── SQuAD-v1.1-<train/dev>.<txt/json>
    │   ├── WebQuestions-<train/test>.txt
    │   ├── freebase-entities.txt
    │   ├── CuratedTrec-<train/test>.txt
    │   └── WikiMovies-<train/test/entities>.txt
    ├── reader
    │   ├── multitask.mdl
    │   └── single.mdl
    └── wikipedia
        ├── docs.db
        └── docs-tfidf-ngram=2-hash=16777216-tokenizer=simple.npz

```

Default model paths for the different modules can also be modified programmatically in the code, e.g.:

```python
import drqa.reader
drqa.reader.set_default('model', '/path/to/model')
reader = drqa.reader.Predictor()  # Default model loaded for prediction
```

#### Document Retriever

TF-IDF model using Wikipedia (unigrams and bigrams, $$2^{24}$$ bins, simple tokenization), evaluated on multiple datasets (test sets, dev set for SQuAD):

| Model                                                        | SQuAD P@5 | CuratedTREC P@5 | WebQuestions P@5 | WikiMovies P@5 | Size  |
| ------------------------------------------------------------ | --------- | --------------- | ---------------- | -------------- | ----- |
| [TF-IDF model](https://s3.amazonaws.com/fair-data/drqa/docs-tfidf-ngram%3D2-hash%3D16777216-tokenizer%3Dsimple.npz.gz) | 78.0      | 87.6            | 75.0             | 69.8           | ~13GB |

*P@5 here is defined as the % of questions for which the answer segment appears in one of the top 5 documents*.

#### Document Reader

Model trained only on SQuAD, evaluated in the SQuAD setting:

| Model                                                        | SQuAD Dev EM | SQuAD Dev F1 | Size   |
| ------------------------------------------------------------ | ------------ | ------------ | ------ |
| [Single model](https://s3.amazonaws.com/fair-data/drqa/single.mdl) | 69.4         | 78.9         | ~130MB |

Model trained with distant supervision without NER/POS/lemma features, evaluated on multiple datasets (test sets, dev set for SQuAD) in the full Wikipedia setting:

| Model                                                        | SQuAD EM | CuratedTREC EM | WebQuestions EM | WikiMovies EM | Size   |
| ------------------------------------------------------------ | -------- | -------------- | --------------- | ------------- | ------ |
| [Multitask model](https://s3.amazonaws.com/fair-data/drqa/multitask.mdl) | 29.5     | 27.2           | 18.5            | 36.9          | ~270MB |

#### Wikipedia

Our full-scale experiments were conducted on the 2016-12-21 dump of English Wikipedia. The dump was processed with the [WikiExtractor](https://github.com/attardi/wikiextractor) and filtered for internal disambiguation, list, index, and outline pages (pages that are typically just links). We store the documents in an sqlite database for which `drqa.retriever.DocDB` provides an interface.

| Database                                                     | Num. Documents | Size  |
| ------------------------------------------------------------ | -------------- | ----- |
| [Wikipedia](https://s3.amazonaws.com/fair-data/drqa/docs.db.gz) | 5,075,182      | ~13GB |

#### QA Datasets

The datasets used for DrQA training and evaluation can be found here:

- SQuAD: [train](https://rajpurkar.github.io/SQuAD-explorer/dataset/train-v1.1.json), [dev](https://rajpurkar.github.io/SQuAD-explorer/dataset/dev-v1.1.json)
- WebQuestions: [train](http://nlp.stanford.edu/static/software/sempre/release-emnlp2013/lib/data/webquestions/dataset_11/webquestions.examples.train.json.bz2), [test](http://nlp.stanford.edu/static/software/sempre/release-emnlp2013/lib/data/webquestions/dataset_11/webquestions.examples.test.json.bz2), [entities](https://s3.amazonaws.com/fair-data/drqa/freebase-entities.txt.gz)
- WikiMovies: [train/test/entities](https://s3.amazonaws.com/fair-data/drqa/WikiMovies.tar.gz) (Rehosted in expected format from <https://research.fb.com/downloads/babi/>)
- CuratedTrec: [train/test](https://s3.amazonaws.com/fair-data/drqa/CuratedTrec.tar.gz) (Rehosted in expected format from <https://github.com/brmson/dataset-factoid-curated>)

##### Format A

The `retriever/eval.py`, `pipeline/eval.py`, and `distant/generate.py` scripts expect the datasets as a `.txt` file where each line is a JSON encoded QA pair, like so:

```
'{"question": "q1", "answer": ["a11", ..., "a1i"]}'
...
'{"question": "qN", "answer": ["aN1", ..., "aNi"]}'
```

Scripts to convert SQuAD and WebQuestions to this format are included in `scripts/convert`. This is automatically done in `download.sh`.

##### Format B

The `reader` directory scripts expect the datasets as a `.json` file where the data is arranged like SQuAD:

```
file.json
├── "data"
│   └── [i]
│       ├── "paragraphs"
│       │   └── [j]
│       │       ├── "context": "paragraph text"
│       │       └── "qas"
│       │           └── [k]
│       │               ├── "answers"
│       │               │   └── [l]
│       │               │       ├── "answer_start": N
│       │               │       └── "text": "answer"
│       │               ├── "id": "<uuid>"
│       │               └── "question": "paragraph question?"
│       └── "title": "document id"
└── "version": 1.1
```

##### Entity lists

Some datasets have (potentially large) candidate lists for selecting answers. For example, WikiMovies' answers are OMDb entries while WebQuestions is based on Freebase. If we have known candidates, we can impose that all predicted answers must be in this list by discarding any higher scoring spans that are not.

### DrQA Components

#### Document Retriever

DrQA is not tied to any specific type of retrieval system -- as long as it effectively narrows the search space and focuses on relevant documents.

Following classical QA systems, we include an efficient (non-machine learning) document retrieval system based on sparse, TF-IDF weighted bag-of-word vectors. We use bags of hashed n-grams (here, unigrams and bigrams).

To see how to build your own such model on new documents, see the retriever README.

To interactively query Wikipedia:                                                                                                                                                                                                                                                                                         

```shell
python scripts/retriever/interactive.py --model /path/to/model
```

If `model` is left out our [default model](https://github.com/facebookresearch/DrQA#document-retriever-1) will be used (assuming it was [downloaded](https://github.com/facebookresearch/DrQA#installing-drqa)).

To evaluate the retriever accuracy (% match in top 5) on a dataset:

```shell
python scripts/retriever/eval.py /path/to/format/A/dataset.txt --model /path/to/model
```

#### Document Reader

DrQA's Document Reader is a multi-layer recurrent neural network machine comprehension model trained to do extractive question answering. That is, the model tries to find the answer to any question as a text span in one of the returned documents.

The Document Reader was inspired by, and primarily trained on, the [SQuAD](https://arxiv.org/abs/1606.05250) dataset. It can also be used standalone on such SQuAD-like tasks where a specific context is supplied with the question, the answer to which is contained in the context.

To see how to train the Document Reader on SQuAD, see the reader [README](https://github.com/facebookresearch/DrQA/blob/master/scripts/reader/README.md).

To interactively ask questions about text with a trained model:

```shell
python scripts/reader/interactive.py --model /path/to/model
```

Again, here `model` is optional; a [default model](https://github.com/facebookresearch/DrQA#document-reader-1) will be used if it is left out.

To run model predictions on a dataset:

```shell
python scripts/reader/predict.py /path/to/format/B/dataset.json --model /path/to/model
```

#### DrQA Pipeline

The full system is linked together in `drqa.pipeline.DrQA`.

To interactively ask questions using the full DrQA:

```shell
python scripts/pipeline/interactive.py
```

Optional arguments:

```shell
--reader-model    Path to trained Document Reader model.
--retriever-model Path to Document Retriever model (tfidf).
--doc-db          Path to Document DB.
--tokenizer      String option specifying tokenizer type to use (e.g. 'corenlp').
--candidate-file  List of candidates to restrict predictions to, one candidate per line.
--no-cuda         Use CPU only.
--gpu             Specify GPU device id to use.
```

To run predictions on a dataset:

```shell
python scripts/pipeline/predict.py /path/to/format/A/dataset.txt
```

Optional arguments:

```shell
--out-dir             Directory to write prediction file to (<dataset>-<model>-pipeline.preds).
--reader-model        Path to trained Document Reader model.
--retriever-model     Path to Document Retriever model (tfidf).
--doc-db              Path to Document DB.
--embedding-file      Expand dictionary to use all pretrained embeddings in this file (e.g. all glove vectors to minimize UNKs at test time).
--candidate-file      List of candidates to restrict predictions to, one candidate per line.
--n-docs              Number of docs to retrieve per query.
--top-n               Number of predictions to make per query.
--tokenizer           String option specifying tokenizer type to use (e.g. 'corenlp').
--no-cuda             Use CPU only.
--gpu                 Specify GPU device id to use.
--parallel            Use data parallel (split across GPU devices).
--num-workers         Number of CPU processes (for tokenizing, etc).
--batch-size          Document paragraph batching size (Reduce in case of GPU OOM).
--predict-batch-size  Question batching size (Reduce in case of CPU OOM).
```

#### Distant Supervision (DS)

DrQA's performance improves significantly in the full-setting when provided with distantly supervised data from additional datasets. Given question-answer pairs but no supporting context, we can use string matching heuristics to automatically associate paragraphs to these training examples.

> Question: What U.S. state’s motto is “Live free or Die”?
>
> Answer: New Hampshire
>
> DS Document: Live Free or Die **“Live Free or Die”** is the official **motto** of the **U.S. state** of **\*New Hampshire***, adopted by the **state** in 1945. It is possibly the best-known of all state mottos, partly because it conveys an assertive independence historically found in American political philosophy and partly because of its contrast to the milder sentiments found in other state mottos.

The `scripts/distant` directory contains code to generate and inspect such distantly supervised data. More information can be found in the distant supervision [README](https://github.com/facebookresearch/DrQA/blob/master/scripts/distant/README.md).

#### Tokenizers

We provide a number of different tokenizer options for convenience. Each has its own pros/cons based on how many dependencies it requires, overhead for running it, speed, and performance. For our reported experiments we used CoreNLP (but results are all similar).

Available tokenizers:

- *CoreNLPTokenizer*: Uses [Stanford CoreNLP](https://stanfordnlp.github.io/CoreNLP/) (option: 'corenlp'). We used v3.7.0. Requires Java 8.
- *SpacyTokenizer*: Uses [spaCy](https://spacy.io/) (option: 'spacy').
- *RegexpTokenizer*: Custom regex-based PTB-style tokenizer (option: 'regexp').
- *SimpleTokenizer*: Basic alpha-numeric/non-whitespace tokenizer (option: 'simple').

See the [list](https://github.com/facebookresearch/DrQA/blob/master/drqa/tokenizers/__init__.py) of mappings between string option names and tokenizer classes.

