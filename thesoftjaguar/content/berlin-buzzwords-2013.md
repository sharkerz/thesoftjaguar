Title: Berlin Buzzwords 2013
Date: 2013-07-30 09:51
Tags: conferences, elasticsearch, solr, lucene, open-source, big-data, java, cassandra, hadoop, pig, search, store, scale
Category: conference
Slug: berlin-buzzwords-2013
Summary: An overview of Berlin Buzzwords main topics, thoughts, comments and more.

*Berlin Buzzwords* took place on June 3rd and 4th at [Kulturbrauerei](http://kulturbrauerei.de/en), which is an awesome place. *Buzzwords* is a conference focused in Open Source technologies, mainly related to search engines, *Big Data* and *NoSQL* databases, having three main topics: `search`, `store` and `scale`. As in FrosCon, ApacheCon or FOSDEM, I went with my colleagues from [edelight](http://edelight.de), which sponsored the trip as usual (and is always proposing new events).

![Maschinenhaus](http://i.imgur.com/cU6iwfG.jpg)

The main technologies from the talks that I attended were [Elasticsearch](http://www.elasticsearch.org/), [Solr](http://lucene.apache.org/solr/) [Cassandra](http://cassandra.apache.org/), [Hadoop](http://hadoop.apache.org/), [Pig](http://pig.apache.org/), [Mahout](http://mahout.apache.org/), [FitNesse](http://fitnesse.org/), [MongoDB](http://www.mongodb.org/), [Logstash](http://logstash.net/) and [Kibana](http://kibana.org/), among others.

Instead of explaining what was going on in each talk that I attended, I have decided to cover the three main areas of the conference.

# Search

This topic covers several search engines, mainly *Elasticsearch*, but also *Solr* (and of course *Lucene* as the base), which are quite famous. But it also wraps how to analyze a lot of log information (or just a lot of data). I mainly attended *Elasticsearch* talks, but there were more technologies involved.

## Just search

So we want to get started with a search engine, but... How do we use them? [Getting down and dirty with Elasticsearch](http://berlinbuzzwords.de/sessions/getting-down-and-dirty-elasticsearch) starts from the basic concepts and explains how to improve our queries (which, IMHO, is the most interesting part of this talk). It differences exact values search (should match **entirely**) from full text search (search within the text), it introduces the *inverted index* concept for improving full text search, which is being done separating words and terms, sorting unique terms and listing docs containing those terms, via the use of **analyzers**.

The exact matching is done (or should be done) with **filters**, and the text search is achieved with **queries**, so the filters are faster than the queries and cacheable, but the queries provide the full text search. Finally, the talk addresses some different ways of implementing **autocomplete**: the *N-grams* method, good for partial word matching, and the *Edge N-grams* method, perfect for autocomplete (just activating the type `edge_ngram`).

The **analyzers** is a very interesting topic, helping us to deal with different languages at query time. [Language Support and Linguistics in Lucene/Solr/Elasticsearch and the open source ecosystem](http://berlinbuzzwords.de/sessions/language-support-and-linguistics-lucenesolrelasticsearch-and-open-source-and-commercial-eco) explains again the tokenization and normalization of the given text query, where the tokens are mapped to the document ids that contain them.

However, we will find the [precision and recall](http://en.wikipedia.org/wiki/Precision_and_recall) problem. The talk explains how Lucene, Elasticsearch and Solr deals with this, and how the synonyms can improve the recall. About the latter, the best practice is to apply the synonyms in the query side instead of in the index: it allows synonym updating without reindexing and is easier to turn the synonym feature off.

![Kesselhaus](http://i.imgur.com/pWprsej.jpg)

When we work with *NoSQL* based solutions, in this case with Elasticsearch, we always have a problem: how to divide the relational data? [Document relations with Elasticsearch](http://berlinbuzzwords.de/sessions/document-relations-elasticsearch) gives two answers to this problem. The first one is about setting the `_parent` field in the desired mapping, for linking the current document (*child*) to another one (*parent*). The advantage is that the parent document doesn't need to exist at indexing time, which improves the performance, and if we want to have the parent documents based on matches with their child ones, we can always set the `has_child` query.

The second workaround is the use of **nested objects**. We can set a JSON document with nested fields, instead of defining a parent, using the `nested` field type (which triggers Lucene's *block indexing*). This document will be flattened and the *block indexing* translate the ES document into multiple Lucene documents. With this approach, the root document and its nested documents remain always in the same block, and when querying, you establish it as a nested query, specify the nested level, the score mode and then the query inside that nested document.

## Test driven development in Big Data

The search topic is also closely related to Big Data. What's the point of having tons and tons of data if we can't find anything useful there? Big Data solutions usually are complex and not easy to test.

[Bug bites Elephant?](http://berlinbuzzwords.de/sessions/bug-bites-elephant-test-driven-quality-assurance-big-data-application-development) presents a way of assuring quality data following the Test-driven development process, specifically when using *Hadoop*, *Pig* and/or *Hive*.

There are several ways of achieving this (*JUnit*, *MRUnit*, *iTest* or simply using scripts), but the talk introduces *FitNesse* as a natural language test specification, where the tests are written as stories instead as programming code. The FitNesse server translates the natural language into Java and integrates with REST or Jenkins if needed.

![FitNesse into action](http://i.imgur.com/O1Qfp1o.png)

This has several advantages, like having a wiki page with the tests written in a language that everyone can understand (or integrate PigLatin directly into that wiki page). However, natural language has its limits, like for instance, if you want to check the expected result (like the output of a Pig job alias).

## Don't drown in a log ocean

Who hasn't had issues when dealing with log information? We want to know what's going on when something is wrong, but sometimes the log is too verbose (Java :P) or simply we have the log files distributed among a lot of servers.

![Big Data is any thing which is crash Excel](http://i.imgur.com/3tfClkO.jpg)

[The State of Open Source Logging](http://berlinbuzzwords.de/sessions/state-open-source-logging) shows some technologies that address this problem, like *fluentd*, *Logstash*, *Graylog2*, *ELSA*, *Flume* or *Scribe*. Therefore, one work around to this problem is to centralize all log files into a Elasticsearch cluster, transforming every log line into a JSON document. The talk is focused on the **Kibana** way, which is a very powerful Logstash and Elasticsearch interface.

## Machine learning baby

Imagine that we have access to a lot of data, and actually we can (Twitter Public API). Now we want to do something useful with that data, like in [Geospatial Event Detection in the Twitter Stream](http://berlinbuzzwords.de/sessions/geospatial-event-detection-twitter-stream), a nice talk stating the desire of creating real actionable insights from tweet data: fires, police alerts, events, demonstrations, accidents...

![There's a fire! Twitter knows before anyone](http://i.imgur.com/IOV0C27.jpg)

In order to detect those events, they grouped the tweets by location (tweet cluster) storing them in *MongoDB*, and then, check if the tweets from the cluster belong to the same topic (marking the group as good). Where is the machine learning? Well, marking the tweet cluster as good is not a trivial thing: the use of a machine learning tool (like Weka) can solve the problem.

The machine learning tool will then make a choice (is the cluster good or bad?) based on a series of user defined rules. In this specific case, it was checking if the tweets had a common theme (n-gram overlap), sentiment (was it positive, negative? Which was the overall sentiment? What would be the sentiment strenght?), subjectivity, number of hashtags, retweet ratio, event categories, embedded links, foursquare tweets (or other kind of predefined tweets), total number of tweets in the cluster, unique locations, bad locations (airports, train stations...), among other parameters. This highlights the relevance of having an important number of quality rules, besides the tool used for performing machine learning.


# Store

## Cassandra in da house

I didn't assist a lot of `Store` talks, and the ones that I did were almost all about *Cassandra*, topic in which I am not too familiar with. [On Cassandra's Evolution](http://berlinbuzzwords.de/sessions/cassandras-evolutions) gave a brief explanation about Casandra's ring of nodes.

They explain the concept of *token*. A *token* belongs to a data range, so the entire data will be divided in *tokens*. So how is the data going to be distributed using these tokens? There are two ways: without and with virtual nodes. The main difference is that virtual nodes will use more tokens per node, (smaller ones)

![Virtual Nodes in Cassandra](http://www.datastax.com/wp-content/uploads/2012/10/VNodes3.png)

Other concepts were explained, like how to repair the cluster with and without virtual nodes, but at the end, the use of virtual nodes was more interesting: you can pick tokens from pretty much every node in the ring, instead of from some nodes of your entire cluster (this is due to the fact that the tokens are smaller), so if you have a lot of data to transfer because you are rebuilding a node, this can make the difference. There are other advantages, they allow heterogeneous nodes and the load balancing is simpler when adding new nodes.

Another interesting part was the introduction to the **Cassandra Query Language** (CQL3). Is kind of a *denormalized* SQL, strictly real time oriented, with no joins, no sub-queries, no aggregation and a limited ORDER BY. They also announced the replace of the Thrift transport protocol for a binary (native) one, which is asynchronous, gives server notifications for new nodes and schema changes, and is totally optimized for CQL3. Another interesting concept is the request tracing, you can trace queries, for instance, seeing which node receives the query and which nodes has the requires replicas, making debugging easier for tracing anti patterns.

![Request tracing](http://i.imgur.com/6AcXIX8.png)

In [Cassandra by Example](http://berlinbuzzwords.de/sessions/cassandra-example-data-modeling-cql3) they extend these concepts using a Django example application called [Twissandra](https://github.com/twissandra/twissandra).


# Scale

The most relevant talk about `scale`, in my opinion, was [Elasticsearch in Miniature](http://berlinbuzzwords.de/sessions/scaling-other-way-elasticsearch-miniature), a nice way of presenting Elasticsearch's distributed capabilities.

![Master not found... Doh!](http://i.imgur.com/8eW4m23.jpg)

The cool thing is that the Elasticsearch guys installed their system in 5 Raspberry Pi, creating a test ES cluster over WiFi: this allowed them to [show us](http://www.youtube.com/watch?feature=player_embedded&v=AA_gihv5H-Y) things like how the shards and replicas were rebalanced when the number of nodes in the cluster was changing, or what was the cluster state after destroying and creating replicas. The interesting thing is that everything went fine, having into account that this kind of demos usually tend to fail in the final presentation, and the fact that the network wasn't really reliable is a plus.
