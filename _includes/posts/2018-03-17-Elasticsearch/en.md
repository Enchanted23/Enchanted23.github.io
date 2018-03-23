**Elasticsearch (ES) is a distributed, scalable, real-time search and analytics engine.** It enables you to search, analyze, and explore your data, often in ways that you did not anticipate at the start of a project. It exists because raw data sitting on a hard drive is just not useful.

Elasticsearch is not just about full-text search. We explain structured search, analytics, the complexities of dealing with human language, geolocation, and relationships.

You can see this [Tutorial-Chinese](https://es.xiaoleilu.com/010_Intro/00_README.html) to know how to download Elasticsearch and some useful tools.

You can see this [Tutorial_1-Chinese](https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/) to get deeper understanding of Elasticsearch.

You can see this [API_EN](http://elasticsearch-py.readthedocs.io/en/master/api.html) to know how to search for APIs.

## Python + Elasticsearch

If you want to see some dead simple examples, you can take a look at this [useful blog](https://tryolabs.com/blog/2015/02/17/python-elasticsearch-first-steps/).

To use ES with Python, it gets easier if we install elasticsearch-py package.

```shell
pip install elasticsearch
```

(You can also use [Elasticsearch DSL](https://github.com/elastic/elasticsearch-dsl-py) or [pyes](https://github.com/aparo/pyes). Elasticsearch DSL's docs can be found [here](https://elasticsearch-dsl.readthedocs.io/en/latest/).)

I already have a .db file which contains recent articles from Wikipedia. Now I want to use ES to contruct a local search engine for Wikipedia.

### 1. creat index

I need to creat indexes from the .db file I have. The simplest way is to perform queries on the original database and create indexes using the returned results.

Firstly, I created a python class which make it easier to perform instructions on the original Sqlite database.

```python
import sqlite3
from . import utils
from . import DEFAULTS

class DocDB(object):
    """Sqlite backed document storage.

    Implements get_doc_text(doc_id).
    """

    def __init__(self, db_path=None):
        self.path = db_path or DEFAULTS['db_path']
        self.connection = sqlite3.connect(self.path, check_same_thread=False)

    def __enter__(self):
        return self

    def __exit__(self, *args):
        self.close()

    def path(self):
        """Return the path to the file that backs this database."""
        return self.path

    def close(self):
        """Close the connection to the database."""
        self.connection.close()

    def get_doc_ids(self):
        """Fetch all ids of docs stored in the db."""
        cursor = self.connection.cursor()
        cursor.execute("SELECT id FROM documents")
        results = [r[0] for r in cursor.fetchall()]
        cursor.close()
        return results

    def get_doc_text(self, doc_id):
        """Fetch the raw text of the doc for 'doc_id'."""
        cursor = self.connection.cursor()
        cursor.execute(
            "SELECT text FROM documents WHERE id = ?",
            (utils.normalize(doc_id),)
        )
        result = cursor.fetchone()
        cursor.close()
        return result if result is None else result[0]
```

Then I can use this class to create indexes of all wikipedia articles.

```python
import argparse
import os
import logging

from spkb import retriever
from elasticsearch import Elasticsearch

logger = logging.getLogger()
logger.setLevel(logging.INFO)
fmt = logging.Formatter('%(asctime)s: [ %(message)s ]', '%m/%d/%Y %I:%M:%S %p')
console = logging.StreamHandler()
console.setFormatter(fmt)
logger.addHandler(console)

# ------------------------------------------------------------------------------
# Build article --> Elasticsearch index.
# ------------------------------------------------------------------------------

def get_es_index(args, db, db_opts):
    """create index for Elasticsearch.

    """
    # Connect to our cluster
    es = Elasticsearch([{'host': 'localhost', 'port': 9200}])

    # Get doc_ids
    logger.info('Get doc_ids...')
    db_class = retriever.get_class(db)
    with db_class(**db_opts) as doc_db:
        doc_ids = doc_db.get_doc_ids()

    # Create indexes
    logger.info('Creating indexes...')
    with db_class(**db_opts) as doc_db:
        for i, doc_id in enumerate(doc_ids):
            es.index(index='wiki', doc_type='article', id=i, body={'text': doc_db.get_doc_text(doc_id)})


# ------------------------------------------------------------------------------
# Main.
# ------------------------------------------------------------------------------

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('db_path', type=str, default=None,
                        help='Path to sqlite db holding document texts')
    args = parser.parse_args()

    logging.info('Connecting to Elasticsearch...')
    get_es_index(args, 'sqlite', {'db_path': args.db_path})
```

### 2. search for articles

