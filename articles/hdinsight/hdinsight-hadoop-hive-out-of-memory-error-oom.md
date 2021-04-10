---
title: Åtgärda fel i en Hive-minnes fel i Azure HDInsight
description: Åtgärda fel i en Hive-minnes fel i HDInsight. Kund scenariot är en fråga över många stora tabeller.
keywords: slut på minnes fel, OOM, Hive-inställningar
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: d1e8f596ee022a59baa89e7f78648c98420eb44b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868876"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Åtgärda ett Apache Hive slut på minnes fel i Azure HDInsight

Lär dig hur du åtgärdar ett Apache Hive slut på minnes fel (OOM) när du bearbetar stora tabeller genom att konfigurera inställningarna för Hive-minnet.

## <a name="run-apache-hive-query-against-large-tables"></a>Kör Apache Hive fråga mot stora tabeller

En kund körde en Hive-fråga:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Vissa olika delarna av den här frågan:

* T1 är ett alias till en stor tabell, TABLE1, som har massor av sträng kolumn typer.
* Andra tabeller är inte som stora men har många kolumner.
* Alla tabeller är kopplade till varandra, i vissa fall med flera kolumner i TABLE1 och andra.

Hive-frågan tog 26 minuter att slutföra i ett 24-nods a3 HDInsight-kluster. Kunden noterade följande varnings meddelanden:

```output
    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product
```

Med hjälp av Apache Tez körnings motor. Samma fråga kördes i 15 minuter och utlöste sedan följande fel:

```output
    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space
```

Felet kvarstår när du använder en större virtuell dator (till exempel D12).

## <a name="debug-the-out-of-memory-error"></a>Felsöka slut på minnes fel

Våra support-och teknik team hittade ett av de problem som orsakade att minnet är slut på ett [känt problem som beskrivs i Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

"När Hive. Auto. convert. Join. noconditionaltask = True vi kontrollerar noconditionaltask. size och om summan av tabell storlekarna i kart kopplingen är mindre än noconditionaltask. storleken på planen genererar en kart koppling, problemet med detta är att beräkningen inte tar hänsyn till den overhead som introduceras av en annan hash-implementation som resultat om summan av indatatyperna är mindre än noconditionaltask storlek med en liten marginal fråga kommer att lanseras OOM."

**Hive. Auto. convert. Join. noconditionaltask** i hive-site.xml-filen har angetts till **True**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Det är troligt att kopplings koppling var orsaken till minnes felet i Java-heap. Som förklaras i blogg inlägget [Hadoop garn Memory Settings i HDInsight](/archive/blogs/shanyu/hadoop-yarn-memory-settings-in-hdinsight), när Tez körnings motor används, hör det heap-utrymme som används faktiskt till Tez-behållaren. Se följande avbildning som beskriver Tez container Memory.

:::image type="content" source="./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png" alt-text="Minnes diagram för Tez container: Hive-slut på minnes fel" border="false":::

I blogg inlägget föreslår följande två minnes inställningar att behållar minnet för heap: **Hive. Tez. container. size** och **Hive. Tez. java. |. väljer**. Från vår erfarenhet innebär det inte att behållarens storlek är för litet. Det innebär att Java-heap-storleken (Hive. Tez. java. önskad) är för liten. Så när du ser slut på minne kan du försöka öka **Hive. Tez. java.** Välj. Om det behövs kan du behöva öka **Hive. Tez. container. size**. Inställningen **Java.** den ska vara runt 80% av **container. size**.

> [!NOTE]  
> Inställningen **Hive. Tez. java.** Välj måste alltid vara mindre än **Hive. Tez. container. size**.

Eftersom en D12-dator har 28 GB minne valde vi att använda en behållar storlek på 10 GB (10240 MB) och tilldela 80% till Java. väljer du:

```console
SET hive.tez.container.size=10240
SET hive.tez.java.opts=-Xmx8192m
```

Med de nya inställningarna har frågan körts under 10 minuter.

## <a name="next-steps"></a>Nästa steg

Att få ett OOM-fel innebär inte nödvändigt vis att behållarens storlek är för liten. I stället bör du konfigurera minnes inställningarna så att heap-storleken höjs och minst 80% av behållarens minnes storlek. Information om hur du optimerar Hive-frågor finns i [optimera Apache Hive frågor för Apache Hadoop i HDInsight](hdinsight-hadoop-optimize-hive-query.md).