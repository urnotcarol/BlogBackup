---
title: Hadoop练习-流量统计
id: 370
date: 2017-06-22 21:04:17
tags:
- Hadoop
categories:
- 数据分析初学
---

## 要求
1.  统计出每个手机号的上行流量、下行流量和总流量。
2.  将所得结果按照总流量倒序排序。
3.  将结果按照手机归属地不同（根据号码前缀判断）设置不同的分区。

## 思路

1.  map阶段预处理数据，手机号作为key，使用自定义的类封装value，包含三个流量值。reduce阶段将同一手机号的流量和统计出来。
2.  map阶段将key和value交换，在value的类中重写compareTo函数，按照总流量倒序排。

<!-- more -->
3.  设置分类器，根据手机号前缀来映射到不同的分区（并不知道实际归属的情况，这里假设134-139开头各映射到一个分区，其余统一映射到一个分区）。使得map阶段的数据在到达reduce阶段之前就到了不同的分区，会有个数等于分区数的reduce任务被执行。


<!-- more -->
## 代码
```
package cn.youe.flowcalc;

import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Partitioner;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

/**
 * Created by Carol on 2017/6/21.
 */
public class FlowCalcDriver {
    static class Flow implements WritableComparable {
        int upFlow = 0;
        int downFlow = 0;
        int totalFlow = 0;

        public int getUpFlow() {
            return upFlow;
        }

        public void setUpFlow(int upFlow) {
            this.upFlow = upFlow;
        }

        public int getDownFlow() {
            return downFlow;
        }

        public void setDownFlow(int downFlow) {
            this.downFlow = downFlow;
        }

        public int getTotalFlow() {
            return totalFlow;
        }

        public void setTotalFlow(int totalFlow) {
            this.totalFlow = totalFlow;
        }

        public void set(int upFlow, int downFlow, int totalFlow) {
            this.upFlow = upFlow;
            this.downFlow = downFlow;
            this.totalFlow = totalFlow;
        }

        public void write(DataOutput out) throws IOException {
            out.writeInt(this.upFlow);
            out.writeInt(this.downFlow);
            out.writeInt(this.totalFlow);
        }

        public void readFields(DataInput in) throws IOException {
            this.upFlow = in.readInt();
            this.downFlow = in.readInt();
            this.totalFlow = in.readInt();
        }

        @Override
        public String toString() {
            return this.upFlow + "\t" + this.downFlow + "\t" + this.totalFlow;
        }

        public int compareTo(Flow that) {
            return this.totalFlow > that.getTotalFlow() ? -1 : 1;
        }
    }

    static class FlowCalcMapper extends Mapper<LongWritable, Text, Text, Flow> {
        Text text = new Text();
        Flow flow = new Flow();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String[] fields = value.toString().split("\t");
            int upFlow = Integer.parseInt(fields[fields.length - 3]);
            int downFlow = Integer.parseInt(fields[fields.length - 2]);
            int totalFlow = upFlow + downFlow;
            text.set(fields[1]);   //不会出现指向同一个数据的问题
            flow.set(upFlow, downFlow, totalFlow);
            context.write(text, flow);
        }
    }

    static class FlowCalcReducer extends Reducer<Text, Flow, Text, Flow> {
        protected void reduce(Text key, Iterable<Flow> values, Context context) throws IOException, InterruptedException {
            Flow flow = new Flow();
            for (Flow value:values) {
                flow.setUpFlow(flow.getUpFlow() + value.upFlow);
                flow.setDownFlow(flow.getDownFlow() + value.getDownFlow());
                flow.setTotalFlow(flow.getTotalFlow() + value.getTotalFlow());
            }
            context.write(key, flow);
        }
    }

    static class FlowSortMapper extends Mapper<LongWritable, Text, Flow, Text> {
        Text text = new Text();
        Flow flow = new Flow();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String fields[] = value.toString().split("\t");
            text.set(fields[0]);
            flow.set(Integer.parseInt(fields[1]), Integer.parseInt(fields[2]), Integer.parseInt(fields[3]));
            context.write(flow, text);  //交换key和value，flow作为key，手机号码作为value。以达到对总流量排序的目的。
        }
    }

    static class FlowSortReducer extends Reducer<Flow, Text, Text, Flow> {
        protected void reduce(Flow key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
            for (Text value:values) {
                context.write(value, key);
            }
        }
    }

    static class phonePartitioner extends Partitioner<Flow, Text> {
        @Override
        public int getPartition(Flow key, Text value, int num) {
            int res;
            int prefix = Integer.parseInt(value.toString().substring(0, 3));
            if (prefix >= 134 && prefix <= 139) {
                res = prefix % 134;
            }
            else {
                res = 6;
            }
            return res;
        }
    }

    static class FlowPartitionMapper extends Mapper<LongWritable, Text, Flow, Text> {
        Text text = new Text();
        Flow flow = new Flow();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String fields[] = value.toString().split("\t");
            text.set(fields[0]);
            flow.set(Integer.parseInt(fields[1]), Integer.parseInt(fields[2]), Integer.parseInt(fields[3]));
            context.write(flow, text);
        }
    }

    static class FlowPartitionReducer extends Reducer<Flow, Text, Text, Flow> {
        @Override
        protected void reduce(Flow key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
            for (Text value:values) {
                context.write(value, key);
            }
        }
    }

    public static void main(String[] args) throws Exception {
        //第一个任务，统计总流量
        Job job = Job.getInstance();

        job.setJarByClass(FlowCalcDriver.class);

        job.setMapperClass(FlowCalcMapper.class);
        job.setReducerClass(FlowCalcReducer.class);

        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Flow.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Flow.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        boolean flag = job.waitForCompletion(true);

        //第二个任务，总流量倒序输出
        Job sortJob = Job.getInstance();

        sortJob.setJarByClass(FlowCalcDriver.class);

        sortJob.setMapperClass(FlowSortMapper.class);
        sortJob.setReducerClass(FlowSortReducer.class);

        sortJob.setMapOutputKeyClass(Flow.class);
        sortJob.setMapOutputValueClass(Text.class);

        sortJob.setOutputKeyClass(Text.class);
        sortJob.setOutputValueClass(Flow.class);

        FileInputFormat.addInputPath(sortJob, new Path(args[1]));
        FileOutputFormat.setOutputPath(sortJob, new Path(args[2]));

        boolean sortFlag = sortJob.waitForCompletion(true);

        //第三个任务，按手机号前缀分区
        Job partJob = Job.getInstance();
        partJob.setJarByClass(FlowCalcDriver.class);
        partJob.setMapperClass(FlowPartitionMapper.class);
        partJob.setReducerClass(FlowPartitionReducer.class);

        partJob.setMapOutputKeyClass(Flow.class);
        partJob.setMapOutputValueClass(Text.class);

        partJob.setOutputKeyClass(Text.class);
        partJob.setOutputValueClass(Flow.class);

        partJob.setPartitionerClass(phonePartitioner.class);
        partJob.setNumReduceTasks(7);

        FileInputFormat.addInputPath(partJob, new Path(args[2]));
        FileOutputFormat.setOutputPath(partJob, new Path(args[3]));

        boolean partFlag = partJob.waitForCompletion(true);

        System.exit((flag == true && sortFlag == true && partFlag == true)? 1:0);
    }
}
```