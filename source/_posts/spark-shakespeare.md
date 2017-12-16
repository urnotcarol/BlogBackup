---
title: Spark统计莎士比亚文集词频大容量版
id: 379
date: 2017-07-08 11:06:28
tags:
- Scala
categories:
- 数据分析初学
---

## 题目要求
### 输入
1. 莎士比亚文集，存放在HDFS上的一个文件目录下面
2. 停词表：与scala目录同级的一个resources目录下

### 输出
1. 所有文集中空行的总数
2. 出现次数前100的单词

<!-- more -->

- 注意：不能上传停词表到HDFS上


## 数据预处理
源文件是一个一个100KB左右的小文件，经过批量复制一共9000多个，如果在处理时依次读取，会花费大量不必要的时间，因此先对其进行处理，合并成一个大文件，放在hdfs上。
### 文件合并思路
使用JAVA操作hdfs的API，读取本地指定目录下所有的文件，依次追加到集群上某一个文件中。相当于代替hdfs shell的`appendToFile`操作。
### 合并代码
```
//HDFSWriter.java
public class HDFSWriter {
    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        conf.setBoolean("dfs.support.append", true);

        File fileDir = new File(args[0]);
        File[] fileList = fileDir.listFiles();
        String hdfsPath = args[1];

        FileSystem hdfs = FileSystem.get(URI.create(hdfsPath), conf);

        OutputStream out = hdfs.append(new Path(hdfsPath));
        for (int i = 0; i < fileList.length; i++) {
            InputStream in = new BufferedInputStream(new FileInputStream(fileList[i]));
            IOUtils.copyBytes(in, out, 4096, false); //第四个参数含义为是否在复制完毕后关闭io流
            in.close();
        }
        out.close();
    }
}
```

### 执行过程
打成JAR之后，用hadoop jar运行
```
hadoop jar /home/hadoop/bigdata-hadoop-mr1-1.jar cn.youe.hdfs.HDFSWriter /home/hadoop/shakespeare merged.txt
```
运行完毕后，集群上出现了1.1G的合并后的文件
```
[root@hdp-node-02 hadoop]# hdfs dfs -ls -h /
-rw-r--r--   1 root supergroup      1.1 G 2017-06-28 20:52 /merged.txt
```
至此，数据预处理完毕，对merged.txt进行词频统计

## 分析
数据按行读入，先统计空行个数，同时将每一行分割成单词的集合。再过滤掉停词，进行词频统计。
## 源码
```
import org.apache.spark.{SparkConf, SparkContext}

object WSWorkCount {
  def main(args: Array[String]): Unit = {
    //args(0)是输入文件，args(1)是总输出路径
    val startTime = System.currentTimeMillis()
    val src = args(0)
    val outPath = args(1)
    val spaceOutPath = outPath + "/spaceout"
    val countOutPath = outPath + "/wordout"

    val conf = new SparkConf().setAppName("WSWorkCount")
    val sc = new SparkContext(conf)
    val lines = sc.textFile(src)

    val sum = sc.accumulator(0, "spaceCount")
    val words = lines.flatMap(line => {
      if (line.trim().length() == 0) sum += 1
      "[a-zA-Z]+".r.findAllIn(line).map(word => (word.toLowerCase(), 1))
    })

    //滤掉停用词
    val stopWords = sc.broadcast(scala.io.Source.fromInputStream(getClass.getResourceAsStream("/stopwords.txt")).getLines.map(_.trim()).toSet)
    val goodWords = words.filter(word => {
      !stopWords.value.contains(word._1)
    })

    //统计前100
    val firstHundred = goodWords.reduceByKey(_ + _).sortBy(_._2, false).take(100)
    val res = sc.parallelize(firstHundred)
    res.saveAsTextFile(countOutPath)

    //输出空格数
    val spaceRes = sc.parallelize(sum.toString())
    spaceRes.saveAsTextFile(spaceOutPath)

    val endTime = System.currentTimeMillis()
    val runTime = (endTime - startTime) / 1000
    val timeOutPath = outPath + "/" + runTime.toString()
    val timeRes = sc.parallelize(runTime.toString())
    timeRes.saveAsTextFile(timeOutPath)
  }
}
```
### 在集群运行
```
/usr/local/apps/spark-1.6.0-bin-hadoop2.6/bin/spark-submit \
--class WSWorkCount \
--master spark://hdp-node-01:7077 \
--executor-memory 512M \
--total-executor-cores 4 \
/home/hadoop/scalabase.jar \
hdfs://hdp-node-01:9000/merged.txt \
hdfs://hdp-node-01:9000/out05
```
## 主要优化思路
1. 统计空行数量的变量sum设置为累加器
2. stopWords设置为广播变量
3. 减少一个map
4. stopWords存储结构使用Set而非Array，查询效率更高

## 不用合并文件的方法
设置hdfs分割文件的大小，使程序可以一次读取大量内容而非按照文件依次去读，这样省去了文件合并的步骤，同时时间也很短。

<!-- more -->
### 代码
```
import org.apache.hadoop.io.{LongWritable, Text}
import org.apache.hadoop.mapreduce.lib.input.CombineTextInputFormat
import org.apache.log4j.{Level, Logger}
import org.apache.spark.{SparkConf, SparkContext}

object WSLocal {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.ERROR)
    // args(0)是输入文件，args(1)是总输出路径
    val src = "C:\\Users\\Administrator\\Desktop\\merged1"
    val outPath = "C:\\Users\\Administrator\\Desktop\\localOut40"
    val spaceOutPath = outPath + "\\spaceOut"
    val countOutPath = outPath + "\\wordOut"

    val startTime = System.currentTimeMillis()

    val conf = new SparkConf().setAppName("WSLocal").setMaster("local")
    val sc = new SparkContext(conf)

    sc.hadoopConfiguration.setLong("mapreduce.input.fileformat.split.maxsize", 20000000)
    val lines = sc.newAPIHadoopFile(src, classOf[CombineTextInputFormat], classOf[LongWritable], classOf[Text]).map(_._2.toString)

//    用上面两行代替这一行
//    val lines = sc.textFile(src) //lines是一个集合，每个元素是源文件中的一行

    val sum = sc.accumulator(0, "spaceCount")
    val words = lines.flatMap(line => {

      if (line.length() == 0){
        sum += 1
      }
      "[a-zA-Z]+".r.findAllIn(line).map(word => (word.toLowerCase(), 1))
    })

    //滤掉停用词
    val stopWords = sc.broadcast(scala.io.Source.fromInputStream(getClass.getResourceAsStream("/stopwords.txt")).getLines.map(_.trim()).toSet)
    val goodWords = words.filter(word => {
      !stopWords.value.contains(word._1)
    })

    //统计前100
    val firstHundred = goodWords.reduceByKey(_ + _).sortBy(_._2, false).take(100)
    val res = sc.parallelize(firstHundred)
    res.saveAsTextFile(countOutPath)

    //输出空格数
    val spaceRes = sc.parallelize(sum.toString())
    spaceRes.saveAsTextFile(spaceOutPath)

    val endTime = System.currentTimeMillis()

    val runTime = (endTime - startTime) / 1000
    val timeOutPath = outPath + "\\" + runTime.toString()
    val timeRes = sc.parallelize(runTime.toString)
    timeRes.saveAsTextFile(timeOutPath)
  }
}
```