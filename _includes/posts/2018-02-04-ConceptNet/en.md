> This blog is about how to make full use of ConceptNet when you want to build your own special version of ConceptNet.

*If you want to know more about the background knowledge and rules of ConceptNet, the links below may be helpful for you.*

[github wiki](https://github.com/commonsense/conceptnet5/wiki)

[online display](http://conceptnet.io)

[paper](http://alumni.media.mit.edu/~hugo/publications/papers/BTTJ-ConceptNet.pdf)

## Preparation

*Before we can make full use of ConceptNet, we need to know how to efficiently use the tools required.*

### 1. Docker

***This is usually our first choice. But we can ignore Docker and set up all the dependencies of ConceptNet separately.***

[Docker](https://www.docker.com/) is a platform for making software reproducible, by reproducing the entire Linux environment it runs in.

ConceptNet 5.5 uses Docker as the primary way of making its build process reproducible. If you want, you can ignore Docker and set up all the dependencies of ConceptNet separately, but Docker will make sure you have a container that satisfies all of ConceptNet's dependencies.

### 2. PostgreSQL

PostgreSQL is a **general purpose and object-relational database management system**, the most advanced open source database system. PostgreSQL was developed based on [POSTGRES 4.2](http://db.cs.berkeley.edu/postgres.html) at [Berkeley Computer Science Department](http://www.cs.berkeley.edu/), University of California.

Relational database management systems are a key component of many web sites and applications. They provide a structured way to store, organize, and access information.

**It is a popular choice for many small and large projects and has the advantage of being standards-compliant and having many advanced features like reliable transactions and concurrency without read locks.**

[Chinese Tutorial](http://www.postgres.cn/docs/9.6/index.html)

[Common Operations - Chinese](http://www.cnblogs.com/kaituorensheng/p/4667160.html#_label26)

[English Tutorial](http://www.postgresqltutorial.com)

[English Tutorial 1](https://www.postgresql.org/docs/current/static/tutorial.html)

#### Architectural Fundamentals

In database jargon, PostgreSQL uses **a client/server model**. A PostgreSQL session consists of the following cooperating processes (programs):

- A server process, which manages the database files, accepts connections to the database from client applications, and performs database actions on behalf of the clients. The database server program is called `postgres`. 
- The user's client (frontend) application that wants to perform database operations. Client applications can be very diverse in nature: a client could be a text-oriented tool, a graphical application, a web server that accesses the database to display web pages, or a specialized database maintenance tool. Some client applications are supplied with the PostgreSQL distribution; most are developed by users.

As is typical of client/server applications, the client and the server can be on different hosts. In that case they communicate over a TCP/IP network connection. You should keep this in mind, because the files that can be accessed on a client machine might not be accessible (or might only be accessible using a different file name) on the database server machine.

The PostgreSQL server can handle multiple concurrent connections from clients. To achieve this it starts (“forks”) a new process for each connection. From that point on, the client and the new server process communicate without intervention by the original `postgres` process. Thus, the master server process is always running, waiting for client connections, whereas client and associated server processes come and go. (All of this is of course invisible to the user. We only mention it here for completeness.)

#### Installation

[How To Install and Use PostgreSQL on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04)

Since we haven't updated our local apt repository lately, let's do that now. We can then get the Postgres package and a "contrib" package that adds some additional utilities and functionality:

```shell
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```

**Using PostgreSQL Roles and Databases**

By default, Postgres uses a concept called "roles" to aid in authentication and authorization. These are, in some ways, similar to regular Unix-style accounts, but Postgres does not distinguish between users and groups and instead prefers the more flexible term "role".

The installation procedure created a user account called `postgres` that is associated with the default Postgres role. In order to use Postgres, we'll need to log into that account. You can do that by typing:

```shell
sudo -i -u postgres
```

You can get a Postgres prompt immediately by typing:

```shell
psql
```

You will be auto-logged in and will be able to interact with the database management system right away.

**Create a New Role**

We can create a new role by typing:

```shell
createuser --interactive
```

You can get more control by passing some additional flags. Check out the options by looking at the `man` page:

```shell
man createuser
```

**Create a New Database**

```shell
createdb test1
```

### 3. Snakemake

The Snakemake workflow management system is a tool to create **reproducible and scalable data analyses**. Workflows are described via a human readable, Python based language. They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition. Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

[English Documentaion](http://snakemake.readthedocs.io/en/latest/)

[Slides](http://slides.com/johanneskoester/snakemake-tutorial-2016#/)

[Sandwiches With Snakemake](https://github.com/leipzig/SandwichesWithSnakemake)

#### Quick Example

Snakemake workflows are essentially Python scripts extended by declarative code to define **rules**. Rules describe how to create **output files** from **input files**.

```python
rule targets:
    input:
        "plots/dataset1.pdf",
        "plots/dataset2.pdf"

rule plot:
    input:
        "raw/{dataset}.csv"
    output:
        "plots/{dataset}.pdf"
    shell:
        "somecommand {input} {output}"
```

- Similar to GNU Make, you **specify targets in terms of a pseudo-rule at the top**.
- For each target and intermediate file, you create rules that define how they are created from input files.
- Snakemake determines the rule dependencies by matching file names.
- Input and output files can contain multiple named wildcards.
- Rules can either use **shell commands, plain Python code or external Python or R scripts** to create output files from input files.
- Snakemake workflows can be easily executed on **workstations**, **clusters**, **the grid**, and **in the cloud** without modification. The job scheduling can be constrained by arbitrary resources like e.g. available CPU cores, memory or GPUs.
- Snakemake can automatically deploy required software dependencies of a workflow using [Conda](https://conda.io/) or [Singularity](http://singularity.lbl.gov/).
- Snakemake can use Amazon S3, Google Storage, Dropbox, FTP, WebDAV, SFTP and iRODS to access input or output files and further access input files via HTTP and HTTPS.

#### Basic Ideas

1. Define workflows in terms of rules

   [Slides](http://slides.com/johanneskoester/snakemake-tutorial-2016#/)

2. Dependencies are determined top-down

   **Problem:** 

   for a given set of targets, find a composition of rules to create them

   **Solution:**

   * For **a given target**, **a rule that can be applied to create it** is determined **(a job)**.
   * For the input files of the rule, go on recursively.
   * If no target is specified, Snakemake tries to apply the first rule in the workflow.

3. Job execution

   A job is executed if and only if: (determined via breadth-first-search on DAG of jobs)

   * output file is target and does not exist
   * output file needed by another executed job and does not exist
   * input file newer than output file
   * input file will be updated by other job
   * execution is enforced

4. Command line interface

   *Assumption: workflow defined in a **Snakefile** in the same directory.*

   ```python
   # execute the workflow with target D1.sorted.txt
   snakemake D1.sorted.txt

   # execute the workflow without target: first rule defines target
   snakemake

   # dry-run
   snakemake -n

   # dry-run, print shell commands
   snakemake -n -p

   # dry-run, print execution reason for each job
   snakemake -n -r

   # visualize the DAG of jobs using the Graphviz dot command
   snakemake --dag | dot -Tsvg > dag.svg
   ```

## ConceptNet Code Analysis

### 1. Data Processing

After we set up the enviroment, we execute the command:

```Shell
./build.sh
```

```shell
#!/bin/bash -e
dbname=${CONCEPTNET_DB_NAME:-conceptnet5}

check_disk_space() {
    kb_free=$(df -Pk data | awk '/[0-9]%/{print $(NF-2)}')
    if [ $kb_free -lt 200000000 ]; then
        echo "To build ConceptNet, you will need at least 200 GB of disk space"
        echo "available for the ./data directory. Try making it a symbolic link"
        echo "to a larger drive."
        echo
        echo "Your data directory is currently on this drive:"
        df -h data
        exit 1
    fi
}

complain_db () {
    echo
    echo "You don't have access to the '$dbname' PostgreSQL database."
    echo "You may need to install PostgreSQL 9.5 or later, create this database,"
    echo "give yourself access to it, or set \$CONCEPTNET_DB_NAME to a database"
    echo "that you can use."
    exit 1
}

check_db () {
    cn5-db check || complain_db
}

check_disk_space
pip install -e '.[vectors]'
check_db

nakemake --resources 'ram=30' -j 2
```

After checking, we can build all the data from raw data:

```Shell
snakemake -j 8 --resources 'ram=16' all
```

(`-j 8` says to run 8 processes of Snakemake in parallel, and `ram=16` constraints the processes that run simultaneously so that they should require around 16 GB of RAM.)

Let's see rule all in [conceptnet5](https://github.com/commonsense/conceptnet5)/**Snakefile**:

```python
rule all:
    input:
        DATA + "/assertions/assertions.csv",
        DATA + "/psql/edges.csv.gz",
        DATA + "/psql/edge_sources.csv.gz",
        DATA + "/psql/edge_features.csv.gz",
        DATA + "/psql/nodes.csv.gz",
        DATA + "/psql/node_prefixes.csv.gz",
        DATA + "/psql/sources.csv.gz",
        DATA + "/psql/relations.csv.gz",
        DATA + "/psql/done",
        DATA + "/stats/languages.txt",
        DATA + "/stats/language_edges.txt",
        DATA + "/stats/relations.txt",
        DATA + "/assoc/reduced.csv",
        DATA + "/vectors/mini.h5"
```

```python
# The directory containing the data files. By default, this is "data" under
# the current directory, but it can be overridden using the
# CONCEPTNET_BUILD_DATA environment variable. This will happen during testing.
DATA = os.environ.get("CONCEPTNET_BUILD_DATA", "data")
```

Some useful output, but we don't know their meanings yet.

Let's see how these data is produced (based on our knowledge of SnakeMake).

#### download

```python
# Downloaders
# ===========
rule download_raw_package:
    output:
        DATA + "/raw/conceptnet-raw-data-5.6.zip"
    shell:
        "wget -nv {RAW_DATA_URL} -O {output}"

rule extract_raw:
    input:
        DATA + "/raw/conceptnet-raw-data-5.6.zip"
    output:
        DATA + "/raw/{dirname}/{filename}"
    shell:
        "unzip {input} raw/{wildcards.dirname}/{wildcards.filename} -d {DATA}"

rule download_conceptnet_ppmi:
    output:
        DATA + "/precomputed/vectors/conceptnet-55-ppmi.h5"
    shell:
        "wget -nv {PRECOMPUTED_DATA_URL}/numberbatch/16.09/conceptnet-55-ppmi.h5 -O {output}"

rule download_numberbatch:
    output:
        DATA + "/precomputed/vectors/numberbatch.h5"
    shell:
        "wget -nv {PRECOMPUTED_DATA_URL}/numberbatch/16.09/numberbatch.h5 -O {output}"

rule download_opensubtitles_ppmi:
    output:
        DATA + "/precomputed/vectors/opensubtitles-ppmi-5.h5"
    shell:
        "wget -nv {PRECOMPUTED_DATA_URL}/numberbatch/17.02/opensubtitles-ppmi-5.h5 -O {output}"
```

```Python
RAW_DATA_URL = "https://zenodo.org/record/1165009/files/conceptnet-raw-data-5.6.zip"
PRECOMPUTED_DATA_PATH = "/precomputed-data/2016"
PRECOMPUTED_DATA_URL = "https://conceptnet.s3.amazonaws.com" + PRECOMPUTED_DATA_PATH
PRECOMPUTED_S3_UPLOAD = "s3://conceptnet" + PRECOMPUTED_DATA_PATH
```

#### precomputation

```python
# Some build steps are difficult to run, so we've already run them and put
# the results in S3. Of course, that can't be the complete solution, because
# we have to have run those build steps first. So when USE_PRECOMPUTED is
# True, we will download the computed files; when it's False, we will compute
# them.
USE_PRECOMPUTED = not os.environ.get("CONCEPTNET_REBUILD_PRECOMPUTED")
```

```python
# The versions of Wiktionary data to download. Updating these requires
# uploading new Wiktionary dumps to ConceptNet's S3.
WIKTIONARY_VERSIONS = {
    'en': '20171201',
    'fr': '20160305',
    'de': '20160407'
}
WIKTIONARY_LANGUAGES = sorted(list(WIKTIONARY_VERSIONS))

# If USE_PRECOMPUTED is False, should we upload the files we compute so they
# can be used as precomputed files later? (Requires ConceptNet S3 credentials.)
UPLOAD = False

# Precomputation
# ==============
# This section is for tricky build steps where we would rather just distribute
# the result of the computation.

def find_wiktionary_input(wildcards):
    if USE_PRECOMPUTED:
        return []
    else:
        language = wildcards.language
        version = WIKTIONARY_VERSIONS[wildcards.language]
        filename = DATA + "/raw/wiktionary/{0}wiktionary-{1}-pages-articles.xml.bz2".format(
            language, version
        )
        return [filename]

rule precompute_wiktionary:
    input:
        find_wiktionary_input
    output:
        DATA + "/precomputed/wiktionary/parsed-{version}/{language}.jsons.gz"
    run:
        if USE_PRECOMPUTED:
            # download the computed files
            shell("wget {PRECOMPUTED_DATA_URL}/wiktionary/"
                  "parsed-{wildcards.version}/{wildcards.language}.jsons.gz "
                  "-O {output}")
        else:
            # This is a mess because, for most of these sub-steps, the file
            # being output isn't {output} but its uncompressed version
            shell("bunzip2 -c {input} "
                  "| wiktionary-parser {wildcards.language} "
                  "| gzip -c > {output}")
            if UPLOAD:
                # upload the files we compute so they can be used as precomputed files later
                shell("aws s3 cp {output} "
                    "{PRECOMPUTED_S3_UPLOAD}/wiktionary/"
                    "parsed-{wildcards.version}/{wildcards.language}.jsons.gz "
                    "--acl public-read")
```

#### read

cn5-read is defined in **setup.py**

```
'cn5-read = conceptnet5.readers.cli:cli'
```

```python
# Readers
# =======
# These are steps that turn raw data into files of uncombined 'edges'.

rule read_conceptnet4:
    input:
        DATA + "/raw/conceptnet4/conceptnet4_flat_{num}.jsons",
        DATA + "/db/wiktionary.db"
    output:
        DATA + "/edges/conceptnet4/conceptnet4_flat_{num}.msgpack"
    run:
        single_input = input[0]
        shell("cn5-read conceptnet4 {single_input} {output}")

rule read_dbpedia:
    input:
        DATA + "/raw/dbpedia/instance_types_en.tql.bz2",
        DATA + "/raw/dbpedia/interlanguage_links_en.tql.bz2",
        DATA + "/raw/dbpedia/mappingbased_objects_en.tql.bz2",
        DATA + "/stats/core_concepts.txt"
    output:
        DATA + "/edges/dbpedia/dbpedia_en.msgpack",
    shell:
        "cn5-read dbpedia {DATA}/raw/dbpedia "
        "{output} "
        "{DATA}/stats/core_concepts.txt "

rule read_jmdict:
    input:
        DATA + "/raw/jmdict/JMdict.xml"
    output:
        DATA + "/edges/jmdict/jmdict.msgpack"
    shell:
        "cn5-read jmdict {input} {output}"

rule read_nadya:
    input:
        DATA + "/raw/nadya/nadya-2017.csv"
    output:
        DATA + "/edges/nadya/nadya.msgpack"
    shell:
        "cn5-read nadya {input} {output}"

rule read_ptt_petgame:
    input:
        DATA + "/raw/ptt_petgame/conceptnet_zh_{part}.txt"
    output:
        DATA + "/edges/ptt_petgame/{part}.msgpack"
    shell:
        "cn5-read ptt_petgame {input} {output}"

rule read_opencyc:
    input:
        DATA + "/raw/opencyc/opencyc-2012-05-10-readable.nq"
    output:
        DATA + "/edges/opencyc/opencyc.msgpack"
    shell:
        "cn5-read opencyc {input} {output}"

rule read_verbosity:
    input:
        DATA + "/raw/verbosity/verbosity.txt"
    output:
        DATA + "/edges/verbosity/verbosity.msgpack"
    shell:
        "cn5-read verbosity {input} {output}"

rule prescan_wiktionary:
    input:
        expand(
            DATA + "/precomputed/wiktionary/parsed-{version}/{language}.jsons.gz",
            version=[WIKT_PARSER_VERSION],
            language=WIKTIONARY_LANGUAGES
        )
    output:
        DATA + "/db/wiktionary.db"
    shell:
        "mkdir -p {DATA}/tmp && "
        "cn5-read wiktionary_pre {input} {DATA}/tmp/wiktionary.db && "
        "mv {DATA}/tmp/wiktionary.db {output}"

rule read_wiktionary:
    input:
        DATA + "/precomputed/wiktionary/parsed-%s/{language}.jsons.gz" % WIKT_PARSER_VERSION,
        DATA + "/db/wiktionary.db"
    output:
        DATA + "/edges/wiktionary/{language}.msgpack",
    shell:
        "cn5-read wiktionary {input} {output}"

rule read_wordnet:
    input:
        DATA + "/raw/wordnet-rdf/wn31.nt"
    output:
        DATA + "/edges/wordnet/wordnet.msgpack",
    shell:
        "cn5-read wordnet {input} {output}"

rule read_emoji:
    input:
        DATA + "/raw/emoji/{language}.xml"
    output:
        DATA + "/edges/emoji/{language}.msgpack"
    shell:
        "cn5-read emoji {input} {output}"

rule read_cc_cedict:
    input:
        DATA + "/raw/cedict/cedict_1_0_ts_utf-8_mdbg.txt.gz"
    output:
        DATA + "/edges/cedict/cedict.msgpack",
    shell:
        "cn5-read cc_cedict {input} {output}"
```

*We will go through the detailed process in which we turn raw data into files of uncombined 'edges' in msgpack format. But we will do it later.*

#### msgpack2csv

```python
'cn5-convert = conceptnet5.formats.convert:cli'
```

```python
# Converting msgpack to csv
# =========================

rule edge_msgpack_to_csv:
    input:
        DATA + "/edges/{dir}/{filename}.msgpack"
    output:
        DATA + "/edges/{dir,[^/]+}/{filename}.csv"
    shell:
        "cn5-convert msgpack_to_tab_separated {input} {output}"

rule assertion_msgpack_to_csv:
    input:
        DATA + "/assertions/{filename}.msgpack"
    output:
        DATA + "/assertions/{filename}.csv"
    shell:
        "cn5-convert msgpack_to_tab_separated {input} {output}"

rule sort_edges:
    input:
        expand(DATA + "/edges/{dataset}.csv", dataset=DATASET_NAMES)
    output:
        DATA + "/collated/sorted/edges.csv"
    shell:
        "mkdir -p {DATA}/tmp && cat {input} | LC_ALL=C sort -T {DATA}/tmp | LC_ALL=C uniq > {output}"

rule combine_assertions:
    input:
        DATA + "/collated/sorted/edges.csv"
    output:
        DATA + "/assertions/assertions.msgpack"
    shell:
        "cn5-build combine {input} {output}"
```

#### into PostgreSQL

```python
cn5-db = conceptnet5.db.cli:cli
```

```python
# Putting data in PostgreSQL
# ==========================
rule prepare_db:
    input:
        DATA + "/assertions/assertions.msgpack"
    output:
        DATA + "/psql/edges.csv",
        DATA + "/psql/edge_sources.csv",
        DATA + "/psql/edge_features.csv",
        DATA + "/psql/nodes.csv",
        DATA + "/psql/node_prefixes.csv",
        DATA + "/psql/sources.csv",
        DATA + "/psql/relations.csv"
    shell:
        "cn5-db prepare_data {input} {DATA}/psql"

rule gzip_db:
    input:
        DATA + "/psql/{name}.csv"
    output:
        DATA + "/psql/{name}.csv.gz"
    shell:
        "gzip -c {input} > {output}"

rule load_db:
    input:
        DATA + "/psql/edges.csv",
        DATA + "/psql/edge_sources.csv",
        DATA + "/psql/edge_features.csv",
        DATA + "/psql/nodes.csv",
        DATA + "/psql/node_prefixes.csv",
        DATA + "/psql/sources.csv",
        DATA + "/psql/relations.csv"
    output:
        DATA + "/psql/done"
    shell:
        "cn5-db load_data {DATA}/psql && touch {output}"
```

#### csv2txt

```python
# Collecting statistics
# =====================
rule relation_stats:
    input:
        DATA + "/assertions/assertions.csv"
    output:
        DATA + "/stats/relations.txt"
    shell:
        "cut -f 2 {input} | LC_ALL=C sort | LC_ALL=C uniq -c "
        "| LC_ALL=C sort -nbr > {output}"

rule all_terms:
    input:
        DATA + "/psql/nodes.csv"
    output:
        DATA + "/stats/terms.txt"
    shell:
        "cut -f 2 {input} > {output}"

rule core_concepts_left:
    input:
        expand(DATA + "/edges/{dataset}.csv", dataset=CORE_DATASET_NAMES)
    output:
        DATA + "/stats/core_concepts_left.txt"
    shell:
        "cut -f 3 {input} > {output}"

rule core_concepts_right:
    input:
        expand(DATA + "/edges/{dataset}.csv", dataset=CORE_DATASET_NAMES)
    output:
        DATA + "/stats/core_concepts_right.txt"
    shell:
        "cut -f 3 {input} > {output}"

rule core_concepts:
    input:
        DATA + "/stats/core_concepts_left.txt",
        DATA + "/stats/core_concepts_right.txt"
    output:
        DATA + "/stats/core_concepts.txt"
    shell:
        "LC_ALL=C sort -u {input} > {output}"


rule concepts_left:
    input:
        DATA + "/assertions/assertions.csv"
    output:
        DATA + "/stats/concepts_left.txt"
    shell:
        "cut -f 3 {input} > {output}"

rule concepts_right:
    input:
        DATA + "/assertions/assertions.csv"
    output:
        DATA + "/stats/concepts_right.txt"
    shell:
        "cut -f 4 {input} > {output}"


rule concept_counts:
    input:
        DATA + "/stats/concepts_left.txt",
        DATA + "/stats/concepts_right.txt"
    output:
        DATA + "/stats/concept_counts.txt"
    shell:
        "cat {input} | grep '^/c/' | cut -d '/' -f 1,2,3,4 "
        "| LC_ALL=C sort | LC_ALL=C uniq -c > {output}"


rule core_concept_counts:
    input:
        DATA + "/stats/core_concepts_left.txt",
        DATA + "/stats/core_concepts_right.txt"
    output:
        DATA + "/stats/core_concept_counts.txt"
    shell:
        "cat {input} | grep '^/c/' | cut -d '/' -f 1,2,3,4 "
        "| LC_ALL=C sort | LC_ALL=C uniq -c > {output}"


rule language_stats:
    input:
        DATA + "/stats/concepts_left.txt",
        DATA + "/stats/concepts_right.txt"
    output:
        DATA + "/stats/languages.txt"
    shell:
        "cat {input} | grep '^/c/' | LC_ALL=C sort | LC_ALL=C uniq | cut -d '/' -f 3 "
        "| LC_ALL=C sort | LC_ALL=C uniq -c | sort -nbr > {output}"

rule language_edge_stats:
    input:
        DATA + "/stats/concepts_left.txt",
        DATA + "/stats/concepts_right.txt"
    output:
        DATA + "/stats/language_edges.txt"
    shell:
        "cat {input} | grep '^/c/' | LC_ALL=C sort | cut -d '/' -f 3 "
        "| LC_ALL=C sort | LC_ALL=C uniq -c | sort -nbr > {output}"
```

#### associations

```python
# Building associations
# =====================
rule assertions_to_assoc:
    input:
        DATA + "/assertions/assertions.msgpack"
    output:
        DATA + "/assoc/assoc-with-dups.csv"
    shell:
        "cn5-convert msgpack_to_assoc {input} {output}"

rule assoc_uniq:
    input:
        DATA + "/assoc/assoc-with-dups.csv"
    output:
        DATA + "/assoc/assoc.csv"
    shell:
        "LC_ALL=C sort {input} | LC_ALL=C uniq > {output}"

rule reduce_assoc:
    input:
        DATA + "/assoc/assoc.csv"
    output:
        DATA + "/assoc/reduced.csv"
    shell:
        "cn5-build reduce_assoc {input} {output}"
```

#### vector space

```python
cn5-vectors = conceptnet5.vectors.cli:cli
```

```Python
# Building the vector space
# =========================
rule convert_word2vec:
    input:
        DATA + "/raw/vectors/GoogleNews-vectors-negative300.bin.gz"
    output:
        DATA + "/vectors/w2v-google-news.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_word2vec -n {SOURCE_EMBEDDING_ROWS} {input} {output}"

rule convert_glove:
    input:
        DATA + "/raw/vectors/glove12.840B.300d.txt.gz"
    output:
        DATA + "/vectors/glove12-840B.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_glove -n {SOURCE_EMBEDDING_ROWS} {input} {output}"

rule convert_fasttext_crawl:
    input:
        DATA + "/raw/vectors/crawl-300d-2M.vec.gz"
    output:
        DATA + "/vectors/crawl-300d-2M.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_fasttext -n {SOURCE_EMBEDDING_ROWS} {input} {output}"

rule convert_fasttext:
    input:
        DATA + "/raw/vectors/fasttext-wiki-{lang}.vec.gz"
    output:
        DATA + "/vectors/fasttext-wiki-{lang}.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_fasttext -n {SOURCE_EMBEDDING_ROWS} -l {wildcards.lang} {input} {output}"

rule convert_lexvec:
    input:
        DATA + "/raw/vectors/lexvec.commoncrawl.300d.W+C.pos.vectors.gz",
    output:
        DATA + "/vectors/lexvec-commoncrawl.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_fasttext -n {SOURCE_EMBEDDING_ROWS} {input} {output}"

rule convert_opensubtitles_ft:
    input:
        DATA + "/raw/vectors/ft-opensubtitles.vec.gz",
    output:
        DATA + "/vectors/fasttext-opensubtitles.h5"
    resources:
        ram=24
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_fasttext -n {MULTILINGUAL_SOURCE_EMBEDDING_ROWS} {input} {output}"

rule convert_polyglot:
    input:
        DATA + "/raw/vectors/polyglot-{language}.pkl"
    output:
        DATA + "/vectors/polyglot-{language}.h5"
    shell:
        "CONCEPTNET_DATA=data cn5-vectors convert_polyglot -l {wildcards.language} {input} {output}"

rule import_opensubtitles_ppmi:
    input:
        DATA + "/precomputed/vectors/opensubtitles-ppmi-5.h5"
    output:
        DATA + "/vectors/opensubtitles-ppmi-5.h5"
    shell:
        "cp {input} {output}"

rule retrofit:
    input:
        DATA + "/vectors/{name}.h5",
        DATA + "/assoc/reduced.csv"
    output:
        temp(expand(DATA + "/vectors/{{name}}-retrofit.h5.shard{n}", n=range(RETROFIT_SHARDS)))
    resources:
        ram=24
    shell:
        "cn5-vectors retrofit -s {RETROFIT_SHARDS} {input} {DATA}/vectors/{wildcards.name}-retrofit.h5"

rule join_retrofit:
    input:
        expand(DATA + "/vectors/{{name}}-retrofit.h5.shard{n}", n=range(RETROFIT_SHARDS))
    output:
        DATA + "/vectors/{name}-retrofit.h5"
    resources:
        ram=24
    shell:
        "cn5-vectors join_retrofit -s {RETROFIT_SHARDS} {output}"

rule merge_intersect:
    input:
        expand(DATA + "/vectors/{name}-retrofit.h5", name=INPUT_EMBEDDINGS)
    output:
        DATA + "/vectors/numberbatch-biased.h5",
        DATA + "/vectors/intersection-projection.h5"
    resources:
        ram=24
    shell:
        "cn5-vectors intersect {input} {output}"

rule debias:
    input:
        DATA + "/vectors/numberbatch-biased.h5"
    output:
        DATA + "/vectors/numberbatch.h5"
    resources:
        ram=30
    shell:
        "cn5-vectors debias {input} {output}"

rule miniaturize:
    input:
        DATA + "/vectors/numberbatch-biased.h5",
        DATA + "/vectors/w2v-google-news.h5"
    output:
        DATA + "/vectors/mini.h5"
    resources:
        ram=4
    shell:
        "cn5-vectors miniaturize {input} {output}"

rule export_text:
    input:
        DATA + "/vectors/numberbatch.h5",
    output:
        DATA + "/vectors/plain/numberbatch.txt.gz"
    shell:
        "cn5-vectors export_text {input} {output}"


rule export_english_text:
    input:
        DATA + "/vectors/numberbatch.h5",
    output:
        DATA + "/vectors/plain/numberbatch-en.txt.gz"
    shell:
        "cn5-vectors export_text -l en {input} {output}"
```

#### morphology

```python
# If USE_MORPHOLOGY is true, we will build and learn from sub-words derived
# from Morfessor.
USE_MORPHOLOGY = False
```

*USE_MORPHOLOGY = False, so I will jump over this part. You can see it on github if you want.*

*In the meantime, I will jump over the evaluation and webdata process. I will focus on getting useful local concepnet5 files such database containing all edges.*

### 2. 

## Papers

*Besides reading the code and figuring out how to use these tools, we can read some papers in which the authers have maken use of conceptnet. Using conceptnet in this way may be much faster than learning the whole strucuture by yourself.*

### Word Embeddings

[ConceptNet 5.5: An Open Multilingual Graph of General Knowledge](https://arxiv.org/pdf/1612.03975.pdf)

> Two prominent matrices of embeddings are the word2vec embeddings trained on **100 billion words of Google News using skip-grams with negative sampling (Mikolov et al. 2013)**, and **the GloVe 1.2 embeddings trained on 840 billion words of the Common Crawl (Pennington, Socher, and Man- ning 2014)**. These matrices are downloadable, and we will be using them both as a point of comparison and as inputs to an ensemble. Levy, Goldberg, and Dagan (2015) evaluated multiple embedding techniques and the effects of various explicit and implicit hyperparameters, produced their own performant word embeddings using **a truncated SVD of words and their contexts**, and provided recommendations for the engineering of word embeddings.

ConceptNet is a knowledge graph that connects words and phrases of natural language (**terms**) with labeled, weighted edges (**assertions**). The original release of ConceptNet (Liu and Singh 2004) was intended as a parsed representation of Open Mind Common Sense (Singh 2002), a crowd-sourced knowledge project. This paper describes the release of Con- ceptNet 5.5, which has expanded to include **lexical and world knowledge from many different sources in many languages**.

- **Term Representation**

  ConceptNet represents terms in a standardized form. The text is Unicode-normalized in [NFKC form](http://unicode.org/reports/tr15/) using Python’s unicodedata implementation, lowercased, and split into non-punctuation tokens using the tokenizer in the Python package wordfreq (Speer et al. 2016), which builds on the standard Unicode word segmentation algorithm. The tokens are joined with underscores, and this text is prepended with the URI /c/lang, where lang is the [BCP 47 language code](https://tools.ietf.org/html/bcp47) for the language the term is in. As an example, the English term “United States” becomes /c/en/united states.
  Relations have a separate namespace of URIs prefixed with /r, such as /r/PartOf. These relations are given artificial names in English, but apply to all languages. The statement that was obtained in Portuguese as “O alimento e ́ usado para comer” is still represented with the relation /r/UsedFor.

- **Vocabulary**

  In ConceptNet, a node is a word or phrase of a natural lan- guage, often a common word in its undisambiguated form. The word “lead” in English is a term in ConceptNet, repre- sented by the URI /c/en/lead, even though it has multi- ple meanings. The advantage of ambiguous terms is that they can be extracted easily from natural language, which is also ambiguous. This ambiguous representation is equivalent to that used by systems that learn distributional semantics from text.

#### Applying ConceptNet to Word Embeddings

**Computing ConceptNet Embeddings Using PPMI (positive point-wise mutual information)**

We can represent the ConceptNet graph as a sparse, symmetric term-term matrix. **Each cell contains the sum of the weights of all edges that connect the two corresponding terms.** For performance reasons, when building this matrix, we prune the ConceptNet graph by discarding terms connected to fewer than three edges.

We consider this matrix to represent terms and their contexts. In a corpus of text, the context of a term would be the terms that appear nearby in the text; here, the context is the other nodes it is connected to in ConceptNet. **We can calculate word embeddings directly from this sparse matrix by following the practical recommendations of Levy, Goldberg, and Dagan (2015)**.

