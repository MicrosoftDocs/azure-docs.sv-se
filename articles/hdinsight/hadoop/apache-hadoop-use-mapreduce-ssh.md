---
title: MapReduce och SSH-anslutning med Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder SSH för att köra MapReduce-jobb med Apache Hadoop på HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939646"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Använda MapReduce med Apache Hadoop på HDInsight med SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Lär dig hur du skickar MapReduce-jobb från en SSH-anslutning (Secure Shell) till HDInsight.

> [!NOTE]
> Om du redan är bekant med att använda Linux-baserade Apache Hadoop-servrar, men du är nybörjare på HDInsight, se [Linux-baserade HDInsight-tips](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Förutsättningar

Ett Apache Hadoop kluster i HDInsight. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Använda Hadoop-kommandon

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du är ansluten till HDInsight-klustret använder du följande kommando för att starta ett MapReduce-jobb:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Det här kommandot startar `wordcount` -klassen, som finns i `hadoop-mapreduce-examples.jar` filen. `/example/data/gutenberg/davinci.txt`Dokumentet används som indata och utdata lagras på `/example/data/WordCountOutput` .

    > [!NOTE]
    > Mer information om det här MapReduce-jobbet och exempel data finns i [använda MapReduce i Apache Hadoop i HDInsight](hdinsight-use-mapreduce.md).

    Jobbet avger information som bearbetas och returnerar information som liknar följande text när jobbet har slutförts:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. När jobbet har slutförts använder du följande kommando för att Visa utdatafilerna:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Det här kommandot visar två filer `_SUCCESS` och `part-r-00000` . `part-r-00000`Filen innehåller utdata för det här jobbet.

    > [!NOTE]  
    > Vissa MapReduce-jobb kan dela resultaten i flera **del-r-#** # # #-filer. I så fall använder du # # # # suffix för att ange filernas ordning.

1. Om du vill visa utdata använder du följande kommando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Det här kommandot visar en lista över de ord som finns i **wasbs://example/data/gutenberg/davinci.txt** -filen och antalet gånger som varje ord har inträffat. Följande text är ett exempel på de data som finns i filen:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Nästa steg

Som du kan se ger Hadoop-kommandon ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster och sedan Visa jobbets utdata. Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
