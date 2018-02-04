> This blog is about how to make full use of ConceptNet when you want to build your own special version of ConceptNet.

*If you want to know more about the background knowledge and rules of ConceptNet, the links below may be helpful for you.*

[github wiki](https://github.com/commonsense/conceptnet5/wiki)

[online display](http://conceptnet.io)

[paper](http://alumni.media.mit.edu/~hugo/publications/papers/BTTJ-ConceptNet.pdf)

## Preparation

*Before we can make full use of ConceptNet, we need to know how to efficiently use the tools required.*

### PostgreSQL

PostgreSQL is a **general purpose and object-relational database management system**, the most advanced open source database system. PostgreSQL was developed based on [POSTGRES 4.2](http://db.cs.berkeley.edu/postgres.html) at [Berkeley Computer Science Department](http://www.cs.berkeley.edu/), University of California.

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



### Snakemake

The Snakemake workflow management system is a tool to create **reproducible and scalable data analyses**. Workflows are described via a human readable, Python based language. They can be seamlessly scaled to server, cluster, grid and cloud environments, without the need to modify the workflow definition. Finally, Snakemake workflows can entail a description of required software, which will be automatically deployed to any execution environment.

[English Documentaion](http://snakemake.readthedocs.io/en/latest/)

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



