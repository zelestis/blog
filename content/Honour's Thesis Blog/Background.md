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
* I need to pick out a handful, ideally the bleeding edge in their area for the type of query compiler/optimizer which are viable to actually benchmark
* I need to finish summarizing a handful of the relevant databases. Quite a lot of the databases are simply a clone of another one, but a different use case. Really when there's ten things using LLVM, only one of them is worth investigating as it's a difference in the part I care about
* I need to read through the literature reviews at the bottom


-----------
# Relevant databases
## Open Source Databases

### LLVM-backed
#### Lingo Db
[Website](https://www.lingo-db.com/) | [Code](https://github.com/lingo-db/lingo-db) | Research papers [one](https://www.vldb.org/pvldb/vol15/p2389-jungmair.pdf) [two](https://vldb.org/pvldb/vol14/p2483-bandle.pdf) | [Benchmarking Tools](https://github.com/lingo-db/lingo-db/tree/eee8c78847b2377ddc8b84974585182a7bb67700/resources/sql/tpch)

Their main ideas seem to be about making sub operators (scan, materialize, scatter, gather, map, fold, and loop) first-class entites and they use MLIR to support their compiler. The impressive thing is that it's very few lines of code - only about 2.5K - and they manage to get their query compiler quite reasonably fast. This means it's a good starting point for understanding the topic of my thesis, since it's mostly about the JIT compiler. It supports enough SQL operations to run TPC-H benchmarks
#### Impala
https://dbdb.io/db/impala 

This also uses LLVM, and it's quite old. The last stable release was only two years ago, so it might be quite an interesting, older sample of a JIT compiler.

#### Apache Kudu
https://dbdb.io/db/kudu

Another interesting LLVM JIT one. I suspect the compiler is going to be very similar to Impala

#### NoisePage
https://dbdb.io/db/noisepage

Seems to be archived on github, but the description seems worth mentioning. It compiles their programming language into bytecode and then those can be handled with LLVM.

#### OpenMLDB
https://dbdb.io/db/openmldb

Yet another LLVM-able JIT compiler. Seems to be built for machine learning applications.

#### SingleStore
https://dbdb.io/db/singlestore

Seems to be yet another LLVM powered query compiler. However, it has more steps before feeding it into LLVM

#### Postgresql
https://www.postgresql.org/docs/current/jit.html

Seems they have some level of JIT support, but I'm not sure how deep it goes

### JVM-backed
#### Derby
* https://dbdb.io/db/derby

> Derby parses the prepared statement using Javacc and generates the Java binary code directly. JIT complier is supported, so that after several executions, JIT compiler will compile it to native code for performance improvement.

#### Neo4j
https://dbdb.io/db/neo4j

The world's leading graphs database. Seems worth checking out.

#### OrientDB
https://dbdb.io/db/orientdb

Yet another database that's supported by the JVM compiler in its query optimizations

#### PrestoDB
https://dbdb.io/db/prestodb

This one seems quite interesting. It started in 2013 at Facebook, and it uses JIT to optimize its queries.

#### Apache Spark
https://dbdb.io/db/spark-sql

This one is very popular, so worth checking out. It uses the Scala compiler. It might be hard to squeeze it into a benchmarking tool though.

#### Tajo
https://dbdb.io/db/tajo

Yet another JIT powered by the JVM, but it seems abandoned and it doesn't seem like it has much unique going on. It's a distributed data warehouse system

-------------
### Others
#### Mutable
[Website](https://mutable.uni-saarland.de/) | [Code](https://github.com/mutable-org/mutable) | [Research paper](https://bigdata.uni-saarland.de/publications/Haffner,%20Dittrich%20-%20mutable:%20A%20Modern%20DBMS%20for%20Research%20and%20Fast%20Prototyping%20@CIDR2023.pdf)| [Benchmarking](https://github.com/mutable-org/mutable/blob/main/benchmark/README.md)

As they say, the main goal of this database is to produce prototypes very quickly for research ideas. They remedy this with metaprogramming and just in time compilation. Turns its code into Wasm which is backed by the V8 engine

#### GreenPlum
https://dbdb.io/db/greenplum

Seems to be a data warehouse that uses Orca for the query optimiser
> Greenplum utilizes query compilation for predicate evaluation, tuple deform and primitive type functions, etc. It doesn't compile the execution engine into a push-based model. As it is mentioned in the Query Execution section, the execution model is volcano pull style.

>  Greenplum differs from Postgres in 3 ways, first it leverages Orca as a query planner

#### QuestDB
https://dbdb.io/db/questdb

This one seems quite interesting. It's a time series database though, so it might be hard to compare to the others. I think it uses a novel? JIT compiler so it could be neat

#### RaptorDB
https://dbdb.io/db/raptordb

Abandoned, but it seems quite interesting. It's a key-value store for JSON documents and it uses the .NET runtime framework to optimise it's query compilation

#### StarRocks
https://dbdb.io/db/starrocks

Seems quite interesting. Doesn't specify how it does the JIT compilation on this page.

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

### HANA
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


-----------
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


----
# Papers / news about JIT in database compilers
## [Exploring Simple Architecture of Just-in-Time Compilation in Databases](https://link.springer.com/chapter/10.1007/978-981-97-7244-5_44)
* This is written at the same university I'm at, and with the same supervisor. So. I should read this properly
* Mentions there are two categories of JIT, Query Plan Execution (QPE) and Expression (EXP)
* HyPer is a QPE example
* PostgreSQL is a EXP example
	* Notable JIT-based systems include PostgreSQL [12], HyPer [8], Umbra [14], ClickHouse [7], and Mutable [6], with HyPer being a representative pioneer.
* They say HyPer is the representative pioneer of this idea, but it's very complicated, it's hard to test, and it isn't very extendable. They want to make a JIT-style system that counters these with low engineering effort, easy testing, and compatible with the ecosystem
* They end up piggy-backing off LLVM. The rest is basically what you'd expect for this architecture - compile SQL statement into C++, feed C++ into LLVM, use LLVM's JIT compiler
* It feels like the vast majority of their effort is in SQL -> C++, they write,
	* > From SQL to C++. In this process, each query’s code is manually written according to the sequential flow of database operators. That is, each query undergoes a manual pass phase to generate C++ code, ensuring that the output results are consistent with the results produced by PostgreSQL
	* My question is, shouldn't PostgreSQL already have code that does this?
* They quote quite ambitious performance metrics that are significantly better than psql. They only go up to 1GB of data though, which I don't think is enough. I.e. their bigger query takes 1000 milliseconds
* They quote some future work they'd be interested in. 
	* Converting SQL -> code
	* Using MLIR 
	* Expand the number of SQL operators
	* Support better parallelisation + vectorization
	* Integrate it into existing databases\
	* Make a query optimizer that supports JIT. Current ones support AOT
	* Caching JIT code

I am curious whether this is vanilla PostgreSQL, or if it is actually JIT-enabled


## [An Empirical Analysis of Just-in-Time Compilation in Modern Databases](https://link.springer.com/chapter/10.1007/978-3-031-47843-7_16)
* Again, this one is also published by my university
* > LLVM serves as the primary JIT architecture, which was implemented in PostgreSQL since version 11
	* This time, it seems they know about JIT in PostgreSQL. Maybe I didn't read the last paper properly then
* Their main goal in the abstract is to compare PostgreSQL's JIT to Mutable's JIT
* Side note: What is WASM? We can compile a number of languages into Wasm, including LLVM IR, and the main purpose is that it's useable by web technologies. Seems very weird to do a comparison with Wasm to me though, shouldn't the pure LLVM destroy it?
* Sort of weird, this article groups JITs into LLVM and WASM approaches
* They also list Hyper, but spell it as Hyper, not HyPer. I assume that's the same thing. They pick PostgreSQL as a representative of the idea instead of HyPer though
* Yeah, so I think the wasm side goes LLVM -> WASM IR -> Jit by V8 and binaryen
* They have nice diagrams for the JIT architecture of PostgreSQL and Mutable
* It seems Mutable's advantage is it feeds their entire query execution plan, while PostgreSQL only does expressions
* Mutable completely destroys postgresql. Also, they have much larger databases. Postgres's JIT doesn't actually seem to make much of an impact on postgres itself, but then they also seem to have a graph that says the benefits first happen at large queries. They never test for 10GB queries between Mutable and PostgreSQL
* Discussions and future work
	* Integrate WASM into PostgreSQL
	* Expand operators into Mutable
	* Harnessing novel hardware
	* Expanded practical evaluation - just compare even more databases

I kind of disagree that WASM is the thing that's making Mutable fast. I'm not sure how good WASM actually is, it's just when I think of the best JIT around I imagine the JVM because it's the oldest and most researched. Really the thing that's making it fast is that it has the whole query plan, and not just the expressions. I think their other paper basically proves this, because it also uses LLVM but gets significantly better results by passing in the entire query plan.


It is sort of interesting that they use PostgreSQL as a representative of HyPer, when HyPer is actually a QPE based compiler while PostgreSQL is a EXP one. I guess I should add a TODO to categorize all the above databases into 1. Compiler used, 2. QPE or EXP. I guess if I have a set of open source databases that are {LLVM, JVM, WASM} x {QPE, EXP} then I'd be fairly happy.

-----
# To read through
Just-In-Time data structures https://www.cidrdb.org/cidr2015/Papers/CIDR15_Paper9.pdf

Towards Just-in-time Compilation of SQL Queries with OMR JitBuilder - https://www.cs.unb.ca/~sray/papers/CASCON2021_preprint.pdf

Just-in-time Compilation in Vectorized Query Execution - https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=a888aff682f443209d0ddf4785cd5b76c2d8d0cf

Compiled Query Execution Engine using JVM - https://ieeexplore.ieee.org/abstract/document/1617391

Hackerrank article on JIT in postgres - https://news.ycombinator.com/item?id=26211895

Also this one. It seems when postgres introduced JIT, it caused significant problems - https://www.reddit.com/r/PostgreSQL/comments/qtsif5/cascade_of_doom_jit_and_how_a_postgres_update_led/
