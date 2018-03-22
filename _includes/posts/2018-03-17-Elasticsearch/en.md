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

I already have a db file which contains recent articles from Wikipedia. Now I want to use ES to contruct a local search engine for Wikipedia.

### 1. creat index

