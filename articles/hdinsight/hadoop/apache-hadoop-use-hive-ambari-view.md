---
title: Använda Apache Ambari Hive-vy med Apache Hadoop i Azure HDInsight
description: Lär dig hur du använder Hive-vyn från webbläsaren för att skicka Hive-frågor. Hive-vyn är en del av Ambari-webbgränssnittet som medföljer ditt Linux-baserade HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 1f2dbef014f1b48b554e6bc30af83b936fe532a7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944872"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Använd Apache Ambari Hive-vyn med Apache Hadoop i HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du kör Hive-frågor med hjälp av Apache Ambari Hive-vyn. I Hive-vyn kan du redigera, optimera och köra Hive-frågor från webbläsaren.

## <a name="prerequisites"></a>Förutsättningar

Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/).  Instruktioner finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters) . Klustret öppnas i en ny portal vy.

1. Välj **Ambari vyer** från **kluster instrument paneler**. När du uppmanas att autentisera ska du använda det `admin` konto namn och lösen ord för kluster inloggning (standard) som du angav när du skapade klustret. Du kan också navigera till `https://CLUSTERNAME.azurehdinsight.net/#/main/views` i din webbläsare där `CLUSTERNAME` är namnet på klustret.

1. I listan med vyer väljer du __Hive-vy__.

    ![Apache Ambari Välj Apache Hive vy](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Sidan Hive-vy liknar följande bild:

    ![Bild av fråge kalkyl bladet för Hive-vyn](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Från fliken __fråga__ klistrar du in följande HiveQL-instruktioner i kalkyl bladet:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Dessa uttryck gör följande:

    |Uttryck | Beskrivning |
    |---|---|
    |DROP TABLE|Tar bort tabellen och data filen, om tabellen redan finns.|
    |SKAPA EXTERN TABELL|Skapar en ny "extern" tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. Data finns kvar på den ursprungliga platsen.|
    |RAD FORMAT|Visar hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.|
    |LAGRAD SOM TEXTFILE-PLATS|Visar var data lagras och att de lagras som text.|
    |SELECT|Väljer ett antal rader där kolumnen T4 innehåller värdet [ERROR].|

   > [!IMPORTANT]  
   > Låt __databasen__ vara markerad som __standard__. I exemplen i det här dokumentet används standard databasen som ingår i HDInsight.

1. Om du vill starta frågan väljer du **Kör** nedanför kalkyl bladet. Knappen aktiverar orange och texten ändras till **stoppa**.

1. När frågan har slutförts visas resultatet av åtgärden i fliken **resultat** . Följande text är resultatet av frågan:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    Du kan använda fliken **logg** om du vill visa den loggnings information som jobbet har skapat.

   > [!TIP]  
   > Hämta eller Spara resultat från den nedrullningsbara dialog rutan **åtgärder** under fliken  **resultat** .

### <a name="visual-explain"></a>Visuell förklaring

Om du vill visa en visualisering av frågeplan väljer du fliken **visuell förklaring** under kalkyl bladet.

Den **visuella förklaringen** av frågan kan vara till hjälp när du ska förstå flödet av komplexa frågor.

### <a name="tez-ui"></a>Tez-gränssnitt

Om du vill visa Tez-gränssnittet för frågan väljer du fliken **Tez-gränssnitt** under kalkyl bladet.

> [!IMPORTANT]  
> Tez används inte för att lösa alla frågor. Du kan lösa många frågor utan att använda Tez.

## <a name="view-job-history"></a>Visa jobbhistorik

På fliken __jobb__ visas en historik över Hive-frågor.

![Apache Hive historik på fliken jobb](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Databas tabeller

Du kan använda fliken __tabeller__ för att arbeta med tabeller i en Hive-databas.

![Bild av fliken Apache Hive tabeller](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Sparade frågor

På fliken **fråga** kan du välja att spara frågor. När du har sparat en fråga kan du återanvända den från fliken __sparade frågor__ .

![Fliken sparade frågor i Apache Hive vyer](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Sparade frågor lagras i standard kluster lagringen. Du kan hitta de sparade frågorna under sökvägen `/user/<username>/hive/scripts` . Dessa lagras som oformaterade text `.hql` filer.
>
> Om du tar bort klustret, men behåller lagringen, kan du använda ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) eller data Lake Storage Explorer (från [Azure Portal](https://portal.azure.com)) för att hämta frågorna.

## <a name="user-defined-functions"></a>Användardefinierade funktioner

Du kan utöka Hive genom användardefinierade funktioner (UDF). Använd en UDF för att implementera funktioner eller logik som inte är lätt att modellera i HiveQL.

Deklarera och spara en uppsättning UDF: er med hjälp av **UDF** -fliken överst i Hive-vyn. Dessa UDF: er kan användas med **Frågeredigeraren**.

![Apache Hive visning av fliken UDF: er](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Knappen **Infoga UDF: er** visas längst ned i **Frågeredigeraren**. Den här posten visar en listruta för de UDF: er som definierats i Hive-vyn. Om du väljer en UDF läggs HiveQL-instruktioner till i din fråga för att aktivera UDF.

Om du till exempel har definierat en UDF med följande egenskaper:

* Resurs namn: myudfs

* Resurs Sök väg:/myudfs.jar

* UDF-namn: myawesomeudf

* UDF-klass namn: com. myudfs. toppen

Om du använder knappen **Infoga UDF: er** visas en post med namnet **myudfs** med en annan listruta för varje UDF som definierats för resursen. I det här fallet är det **myawesomeudf**. Om du väljer den här posten läggs följande till i början av frågan:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Du kan sedan använda UDF-filen i din fråga. Ett exempel är `SELECT myawesomeudf(name) FROM people;`.

Mer information om hur du använder UDF: er med Hive i HDInsight finns i följande artiklar:

* [Använda python med Apache Hive och Apache gris i HDInsight](python-udf-hdinsight.md)
* [Använda en Java UDF med Apache Hive i HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive-inställningar

Du kan ändra olika Hive-inställningar, till exempel ändra körnings motorn för Hive från Tez (standard) till MapReduce.

## <a name="next-steps"></a>Nästa steg

Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använda Apache Beeline-klienten med Apache Hive](apache-hadoop-use-hive-beeline.md)
