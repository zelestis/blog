---
title: Background
description: 
permalink: honours_background
aliases: 
tags: 
draft: false
---
I'm doing my honour's thesis! The topic here is about Just-in-Time query compilation in Relational Database engines. Step one here is to collect information about the current databases that do this, how they function and whether they're actually good. This page is a quick summary about the options around and how they work.


Most of the databases are from [dbdb io](https://dbdb.io/browse?query-compilation=jit-compilation)

TODOs left on this page:
* I need to categorise the databases into ones that use the JVM for compiling, LLVM, or a novel approach
* I need to pick out a handful, ideally the bleeding edge in their area for the type of query compiler/optimizer which are viable to actually benchmark
* I need to finish summarizing a handful of the relevant databases. Quite a lot of the databases are simply a clone of another one, but a different use case. Really when there's ten things using LLVM, only one of them is worth investigating as it's a difference in the part I care about


# Relevant databases
## Open Source Databases
### Lingo Db
[Website](https://www.lingo-db.com/) | [Code](https://github.com/lingo-db/lingo-db) | Research papers [one](https://www.vldb.org/pvldb/vol15/p2389-jungmair.pdf) [two](https://vldb.org/pvldb/vol14/p2483-bandle.pdf) | [Benchmarking Tools](https://github.com/lingo-db/lingo-db/tree/eee8c78847b2377ddc8b84974585182a7bb67700/resources/sql/tpch)


Their main ideas seem to be about making sub operators (scan, materialize, scatter, gather, map, fold, and loop) first-class entites and they use MLIR to support their compiler. The impressive thing is that it's very few lines of code - only about 2.5K - and they manage to get their query compiler quite reasonably fast. This means it's a good starting point for understanding the topic of my thesis, since it's mostly about the JIT compiler. It supports enough SQL operations to run TPC-H benchmarks

### Mutable
[Website](https://mutable.uni-saarland.de/) | [Code](https://github.com/mutable-org/mutable) | [Research paper](https://bigdata.uni-saarland.de/publications/Haffner,%20Dittrich%20-%20mutable:%20A%20Modern%20DBMS%20for%20Research%20and%20Fast%20Prototyping%20@CIDR2023.pdf)| [Benchmarking](https://github.com/mutable-org/mutable/blob/main/benchmark/README.md)

As they say, the main goal of this database is to produce prototypes very quickly for research ideas. They remedy this with metaprogramming and just in time compilation.

### Derby
* https://dbdb.io/db/derby
TODO

### GreenPlum
https://dbdb.io/db/esgyndb
TODO. Seems to be a data warehouse that uses Orca for the query optimiser

### Impala
https://dbdb.io/db/impala 
This also uses LLVM, and it's quite old. The last stable release was only two years ago, so it might be quite an interesting, older sample of a JIT compiler.

### Apache Kudu
https://dbdb.io/db/kudu
Another interesting LLVM JIT one. I suspect the compiler is going to be very similar to Impala

### Neo4j
https://dbdb.io/db/neo4j
The world's leading graphs database. Seems worth checking out.

### NoisePage
https://dbdb.io/db/noisepage
Seems to be archived on github, but the description seems worth mentioning. It compiles their programming language into bytecode and then those can be handled with LLVM.

### OpenMLDB
https://dbdb.io/db/openmldb
Yet another LLVM-able JIT compiler. Seems to be built for machine learning applications.

### OrientDB
https://dbdb.io/db/orientdb
Yet another database that's supported by the JIT compiler in its query optimizations

### PrestoDB
https://dbdb.io/db/prestodb
This one seems quite interesting. It started in 2013 at Facebook, and it uses JIT to optimize its queries.

### QuestDB
https://dbdb.io/db/questdb
This one seems quite interesting. It's a time series database though, so it might be hard to compare to the others. I think it uses a novel? JIT compiler so it could be neat

### RaptorDB
https://dbdb.io/db/raptordb
Abandoned, but it seems quite interesting. It's a key-value store for JSON documents and it uses the .NET runtime framework to optimise it's query compilation

### SingleStore
https://dbdb.io/db/singlestore
Seems to be yet another LLVM powered query compiler. However, it has more steps before feeding it into LLVM

### Apache Spark
https://dbdb.io/db/spark-sql
This one is very popular, so worth checking out. It uses the Scala compiler. It might be hard to squeeze it into a benchmarking tool though.

### StarRocks
https://dbdb.io/db/starrocks
Seems quite interesting. Doesn't specify how it does the JIT compilation on this page.

### Tajo
https://dbdb.io/db/tajo
Yet another JIT powered by the JVM, but it seems abandoned and it doesn't seem like it has much unique going on. It's a distributed data warehouse system

### Postgresql
https://www.postgresql.org/docs/current/jit.html
Seems they have some level of JIT support, but I'm not sure how deep it goes

## Proprietary
### HyPer
[Website](https://hyper-db.de/) |  [dbdb.io](https://dbdb.io/db/hyper)

### ExtremeDb
* https://dbdb.io/db/extremedb
	* Strictly a commercial database, so it seems sort of interesting, but it seems incredibly flexible and it ended development in 2021. Could be worth trying to read into/understand. TODO.
* Firebolt
* https://dbdb.io/db/esgyndb
* Branded as a low latency database, which seems interesting. Specifically a cloud data warehouse though so I'm not sure whether it's particularly relevant
* TODO

## HANA
https://dbdb.io/db/hana
* Seems somewhat interesting, but it's a column oriented and in memory database. Apparently it uses LLVM JIT for query optimisations, which means it might not be that unique to look into when we already have LingoDB serving a similar role

### PieCloudDB
https://dbdb.io/db/pieclouddb
* Seems everything about it is in Chinese, and supports AI models. Not sure how keep I am on this one since it has to do with OLAPs - analytical processing

### Proteus
https://proteusdb.com/
Yet another LLVM-supported database query optimizer. It has some GPU optimizations in it, but it seems to have quite a lot of publications. Most seem to be more focused on the GPU side, but some of them have information about trying to get CPU-GPU parallelism in JIT compiled engines.


### Umbra
https://dbdb.io/db/umbra

This one looks quite interesting. It has a fairly specific query compiler and ultimately feeds it into LLVM, but it also has a lowering through "Flying Start"


# Irrelevant databases
* https://dbdb.io/db/aresdb
	* Database by Uber that's used for analytics and is a GPU-based database. There doesn't seem to be much information about its JIT, and the main focus is about it being GPU based.
* https://dbdb.io/db/activepivot
	* Seems to be a very specialized database for multidimensional data, and the JIT component is only for Multidimensional Expression queries. They moved over to be mostly about analytics on their websites, and it's called something else entirely now.
* https://dbdb.io/db/blazingsql
	* Seems like a semi-interesting database with GPU acceleration. It uses RAPIDS for compiling it's code, and CUDA has support for JIT in the compiler. It seems mostly about GPU and, and "ended" developedment in 2021 according to the dbdb io page. The last commit on their github was 2 years ago, but it does have 2000 stars
	* It is interesting that they're using RAPID though, that might make it worth looking into since it's a bit more novel and with JIT 
* https://dbdb.io/db/cedardb
	* I can't really find much information on their docs about the JIT component of this database. It seems to be the commercial offering of Umbra... so we may as well look into umbra instead
* https://dbdb.io/db/datomic
	* Compiles with Clojure, and this produces java byte code which is then JIT-compatible with the JVM. Moderately interesting, but it's proprietary. Could be worth trying to benchmark and read about though. I'd rather find some database that uses the JVM for query optimisation and is open source which leads to Derby
* https://dbdb.io/db/esgyndb
	* The description on dbdb seems interesting, but the hyperlinks lead to to a Chinese website which doesn't look useful at all
* https://dbdb.io/db/juliadb
	* Seems to be unmaintained and they moved to https://github.com/JuliaData/DataFrames.jl and https://github.com/JuliaParallel/DTables.jl. Not sure if either one uses JIT
* https://dbdb.io/db/splice-machine
	* This one might be relevant, but it seems to be HBase, SparkSQL and Apache Derby glued together.
## Uncategorised databases


## Similar literature reviews to look at
Exploring Simple Architecture of Just-in-Time Compilation in Databases - https://link.springer.com/chapter/10.1007/978-981-97-7244-5_44
An Empirical Analysis of Just-in-Time Compilation in Modern Databases - https://link.springer.com/chapter/10.1007/978-3-031-47843-7_16
Just-In-Time data structures https://www.cidrdb.org/cidr2015/Papers/CIDR15_Paper9.pdf
Towards Just-in-time Compilation of SQL Queries with OMR JitBuilder - https://www.cs.unb.ca/~sray/papers/CASCON2021_preprint.pdf
Just-in-time Compilation in Vectorized Query Execution - https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=a888aff682f443209d0ddf4785cd5b76c2d8d0cf
Compiled Query Execution Engine using JVM - https://ieeexplore.ieee.org/abstract/document/1617391
