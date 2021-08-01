---
title: spark加载数据到ES
date: 2021-08-01 09:19:13
img: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/H8s52K.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Ixi9r8.jpg
toc: true
mathjax: false
summary: spark多种方式将数据写入的elasticsearch，会通过源码介绍scala的隐式转换进行mixin方式实现不侵入实现RDD具有saveToES功能
keywords:
-  spark
-  es
-  大数据 
categories: 大数据
tags:
-  spark
-  es
-  大数据

---

在日常开发中一定会遇到，spark将计算好的数据load到es中，供后端同学查询使用。下面介绍一下spark写es的方式。 使用scala进行演示，对应的java自己google了。


spark写es需要使用到 对应的包es包。maven配置如下

## MAVEN配置

```maven
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.12</artifactId>
            <version>3.0.0</version>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch-hadoop</artifactId>
            <version>7.0.0</version>
        </dependency>
```


### 使用MAP方式

代码如下

```sql
package org.bigdata.es

import org.apache.spark.{SparkConf, SparkContext}
import org.elasticsearch.spark._

object D01 {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setAppName("d01").setMaster("local[*]")
    conf.set("es.index.auto.create", "true")

    val sc: SparkContext = new SparkContext(conf)

    // map方式
    val numbers = Map("one" -> 1, "two" -> 2, "three" -> 3)
    val airports = Map("arrival" -> "Otopeni", "SFO" -> "San Fran")
    sc.makeRDD(Seq(numbers, airports)).saveToEs("spark/docs")

  }
}
```

注意： 必须要导入 **import org.elasticsearch.spark._**, 不然，就没有 `saveToEs`方法了


下面介绍一下， org.elasticsearch.spark._ 导入的隐式函数


![包对象中隐式函数](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/uJdnvn.jpg)


在 org.elasticsearch.spark._  下面的包对象中有 一个隐式函数，将 RDD转成 SparkRDDFunctions



### 反编译成 java代码

 ```java
package org.bigdata.es;

import org.apache.spark.SparkConf;
import org.apache.spark.SparkContext;
import scala.collection.Seq;
import scala.collection.immutable.Map;
import scala.runtime.BoxesRunTime;

public final class D01$ {
  public static final D01$ MODULE$;
  
  public void main(String[] args) {
    SparkConf conf = (new SparkConf()).setAppName("d01").setMaster("local[*]");
    conf.set("es.index.auto.create", "true");
    SparkContext sc = new SparkContext(conf);
    (new scala.Tuple2[3])[0] = scala.Predef$ArrowAssoc$.MODULE$.$minus$greater$extension(scala.Predef$.MODULE$.ArrowAssoc("one"), BoxesRunTime.boxToInteger(1));
    (new scala.Tuple2[3])[1] = scala.Predef$ArrowAssoc$.MODULE$.$minus$greater$extension(scala.Predef$.MODULE$.ArrowAssoc("two"), BoxesRunTime.boxToInteger(2));
    (new scala.Tuple2[3])[2] = scala.Predef$ArrowAssoc$.MODULE$.$minus$greater$extension(scala.Predef$.MODULE$.ArrowAssoc("three"), BoxesRunTime.boxToInteger(3));
    Map numbers = (Map)scala.Predef$.MODULE$.Map().apply((Seq)scala.Predef$.MODULE$.wrapRefArray((Object[])new scala.Tuple2[3]));
    (new scala.Tuple2[2])[0] = scala.Predef$ArrowAssoc$.MODULE$.$minus$greater$extension(scala.Predef$.MODULE$.ArrowAssoc("arrival"), "Otopeni");
    (new scala.Tuple2[2])[1] = scala.Predef$ArrowAssoc$.MODULE$.$minus$greater$extension(scala.Predef$.MODULE$.ArrowAssoc("SFO"), "San Fran");
    Map airports = (Map)scala.Predef$.MODULE$.Map().apply((Seq)scala.Predef$.MODULE$.wrapRefArray((Object[])new scala.Tuple2[2]));
    (new Map[2])[0] = numbers;
    (new Map[2])[1] = airports;
    org.elasticsearch.spark.package$.MODULE$.sparkRDDFunctions(sc.makeRDD((Seq)scala.collection.Seq$.MODULE$.apply((Seq)scala.Predef$.MODULE$.wrapRefArray((Object[])new Map[2])), sc.makeRDD$default$2(), scala.reflect.ClassTag$.MODULE$.apply(Map.class)), scala.reflect.ClassTag$.MODULE$.apply(Map.class)).saveToEs("spark/docs");
  }
  
  private D01$() {
    MODULE$ = this;
  }
}
```



再给一下，其他的 写es的代码

## 其他方式写ES

### 使用样例类方式
```scala

package org.bigdata.es

import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}
import org.elasticsearch.spark.rdd.EsSpark

object D02 {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setAppName("d01").setMaster("local[*]")
    conf.set("es.index.auto.create", "true")

    val sc: SparkContext = new SparkContext(conf)
    val upcomingTrip: Trip = Trip("OTP", "SFO")
    val lastWeekTrip: Trip = Trip("MUC", "OTP")

    val rdd: RDD[Trip] = sc.makeRDD(Seq(upcomingTrip, lastWeekTrip))
    EsSpark.saveToEs(rdd, "spark/docs", Map("es.mapping.id" -> "id"))
  }
}


// define a case class
case class Trip(departure: String, arrival: String)

```



### 使用字符串json方式

```scala
package org.bigdata.es

import org.apache.spark.{SparkConf, SparkContext}
import org.elasticsearch.spark._


object D03 {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setAppName("d01").setMaster("local[*]")
    conf.set("es.index.auto.create", "true")

    val sc: SparkContext = new SparkContext(conf)

    val json1 = """{"reason" : "business", "airport" : "SFO"}"""
    val json2 = """{"participants" : 5, "airport" : "OTP"}"""
    sc.makeRDD(Seq(json1, json2)).saveToEs("spark/json-trips")

  }
}

```



### 动态index

```scala
package org.bigdata.es

import org.apache.spark.{SparkConf, SparkContext}
import org.elasticsearch.spark._

object D04 {
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setAppName("d01").setMaster("local[*]")
    conf.set("es.index.auto.create", "true")

    val sc: SparkContext = new SparkContext(conf)

    val game = Map(
      "media_type" -> "game",
      "title" -> "FF VI",
      "year" -> "1994")

    val book = Map("media_type" -> "book", "title" -> "Harry Potter", "year" -> "2010")
    val cd = Map("media_type" -> "music", "title" -> "Surfing With The Alien")

    sc.makeRDD(Seq(game, book, cd)).saveToEs("my-collection-{media_type}/doc")

  }
}

```