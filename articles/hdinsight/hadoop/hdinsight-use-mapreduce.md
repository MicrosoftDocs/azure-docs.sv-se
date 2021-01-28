---
title: MapReduce med Apache Hadoop på HDInsight
description: Lär dig hur du kör Apache MapReduce-jobb på Apache Hadoop i HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944261"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Använda MapReduce i Apache Hadoop på HDInsight

Lär dig hur du kör MapReduce-jobb på HDInsight-kluster.

## <a name="example-data"></a>Exempeldata

HDInsight innehåller olika exempel data uppsättningar, som lagras i- `/example/data` och- `/HdiSamples` katalogen. Dessa kataloger finns i standard lagrings utrymmet för klustret. I det här dokumentet använder vi `/example/data/gutenberg/davinci.txt` filen. Den här filen innehåller antecknings böckerna för Leonardo da Vinci.

## <a name="example-mapreduce"></a>Exempel MapReduce

Ett exempel på MapReduce ord räknings program ingår i ditt HDInsight-kluster. Det här exemplet finns `/example/jars/hadoop-mapreduce-examples.jar` på standard lagrings utrymmet för klustret.

Följande Java-kod är källan till det MapReduce-program som finns i `hadoop-mapreduce-examples.jar` filen:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Instruktioner för att skriva egna MapReduce-program finns i [utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Kör MapReduce

HDInsight kan köra HiveQL-jobb med olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig bäst. Följ sedan länken för en genom gång.

| **Använd detta**... | **... för att göra detta** |  ... från det här **klient operativ systemet** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Använda Hadoop-kommandot via **SSH** |Linux, UNIX, Mac OS X eller Windows |
| [Klammerparentes](apache-hadoop-use-mapreduce-curl.md) |Skicka jobbet via en fjärr anslutning med hjälp av **rest** |Linux, UNIX, Mac OS X eller Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Skicka jobbet via en fjärr anslutning med hjälp av **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med data i HDInsight finns i följande dokument:

* [Utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Använda Apache Hive med HDInsight](./hdinsight-use-hive.md)
