# DSLab 2020 Week 6

## Spark 2/3: DataFrames

<p style="text-align: center;">
   <img src="figs/spark_logo.png" width="200">
</p>

<p style="text-align: right;"><em><strong>Pamela Delgado</em></strong></p>

--

## Last week recap

* introduction to Spark
* RDDs: Spark's core data abstraction
* operations on RDDs: transformations and actions
* exercises to get started + Guttenberg


### This week

* Spark DataFrames

---

## What are DataFrames?

* distributed collection of data
* organized into named columns

* pretty much like relational DB table
* plus optimizations

--

## Why DataFrames?
## (as opposed to RDDs)

* original RDD's target: Big Data
* inspired by R and Pandas
* keep flexibility (string data files, Hive tables, external database, RDDs)
* variety of formats (json, parquet, ORC, JDBC, other formats with plugins)

--

## DataFrames vs RDDs

* do not require using basic RDD methods (`map`, `reduce`, etc.)
* schema tells Spark data types
* allows execution optimizations

---

## [DataFrame demo](./dataframes-demo.html)

* [Download the notebook](./dataframes-demo.ipynb)

--

## [DataFrame Covid demo](./dataframes-covid.html)

* [Download the notebook](./dataframes-covid.ipynb)

---

## DataFrames under the hood

<img src="figs/pyspark_architecture.png" height=400px>


* spark workers pull data from sources into the JVM
* data is actually processed in python subprocesses
* (de)serialization and streaming at every step


--

## PySpark DataFrame optimization

<img src="figs/databricks_catalyst.png" height=150px>

* by typing the data and using built-in functions, operations can be optimized
* certain operations (e.g. filtering) can be pushed one lever lower (to the DB for example)
* in the end, still runs on RDD - can always resort to the lower-level API

--

## Useful Links

* [Lab repository](https://renku.iccluster.epfl.ch/projects/dslab2020/week6-spark-dataframes)
* [A nice post about DataFrames and code generation](https://virtuslab.com/blog/spark-sql-hood-part-i/)
* [General Spark docs](http://spark.apache.org/docs/latest)
* [Spark DataFrame intro (good for a lookup of basic syntax)](http://spark.apache.org/docs/latest/sql-programming-guide.html)
* [Spark DataFrame Reference](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html)
* [Spark ML Reference](http://spark.apache.org/docs/latest/api/python/pyspark.ml#pyspark-ml-package)
