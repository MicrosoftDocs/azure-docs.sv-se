---
title: Apache Hive & Data Lake verktyg för Visual Studio – Azure HDInsight
description: Lär dig hur du använder Data Lake verktyg för Visual Studio för att köra Apache Hive frågor med Apache Hadoop på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3e54991f6afd2ace19bf7b70e57bda6f551f7780
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943172"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Köra Apache Hive-frågor med hjälp av Data Lake Tools för Visual Studio

Lär dig hur du använder Data Lake verktyg för Visual Studio för att fråga Apache Hive. Med Data Lake-verktygen kan du enkelt skapa, skicka och övervaka Hive-frågor till Apache Hadoop på Azure HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Information om hur du skapar det här objektet finns i [skapa Apache Hadoop kluster i Azure HDInsight med Resource Manager-mall](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). Stegen i den här artikeln använder Visual Studio 2019.

* HDInsight Tools för Visual Studio eller Azure Data Lake Tools för Visual Studio. Information om hur du installerar och konfigurerar verktygen finns i [installera Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Kör Apache Hive frågor med Visual Studio

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor.
* Skapa ett Hive-program.

### <a name="create-an-ad-hoc-hive-query"></a>Skapa en ad hoc Hive-fråga

Ad hoc-frågor kan köras i antingen **batch** -eller **interaktivt** läge.

1. Starta **Visual Studio** och välj **Fortsätt utan kod**.

2. Från **Server Explorer** högerklickar du på **Azure**, väljer **Anslut till Microsoft Azure prenumeration...** och slutför inloggnings processen.

3. Expandera **HDInsight**, högerklicka på det kluster där du vill köra frågan och välj sedan **Skriv en Hive-fråga**.

4. Ange följande Hive-fråga:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Välj **Kör**. Körnings läget är som standard **interaktivt**.

    ![Kör interaktiv Hive-fråga, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Om du vill köra samma fråga i **batchläge** växlar du den nedrullningsbara listan från **interaktiv** till **batch**. Körnings knappen ändras från att **köras** till att **skickas**.

    ![Skicka batch Hive-fråga, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver visas `SELECT * FROM` alla föreslagna tabell namn i IntelliSense. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF. IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

7. I verktygsfältet fråga (avsnittet under fliken fråga och ovanför frågetexten) väljer du **Skicka** eller väljer PULLDOWN-pilen bredvid **Skicka** och välj **Avancerat** i listan PULLDOWN. Om du väljer det senare alternativet

8. Om du har valt alternativet Avancerad överföring konfigurerar du **jobbnamn**, **argument**, **ytterligare konfigurationer** och **status katalog** i dialog rutan **Skicka skript** . Välj sedan **Skicka**.

    ![Dialog rutan Skicka skript, HDInsight Hadoop Hive-fråga](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Skapa ett Hive-program

Följ dessa steg om du vill köra en Hive-fråga genom att skapa ett Hive-program:

1. Öppna **Visual Studio**.

2. I fönstret **Starta** väljer du **skapa ett nytt projekt**.

3. Skriv *Hive* i rutan **Sök efter mallar** i fönstret **skapa ett nytt projekt** . Välj sedan **Hive-program** och välj **Nästa**.

4. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn**, väljer eller skapar en **plats** för det nya projektet och väljer sedan **skapa**.

5. Öppna filen **script. HQL** som skapas med det här projektet och klistra in följande HiveQL-uttryck:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Dessa uttryck gör följande:

    * `DROP TABLE`: Tar bort tabellen om den finns.

    * `CREATE EXTERNAL TABLE`: Skapar en ny extern tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. (Data finns kvar på den ursprungliga platsen.)

        > [!NOTE]  
        > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa, till exempel ett MapReduce-jobb eller en Azure-tjänst.
        >
        > Att släppa en extern tabell tar **inte** bort data, endast tabell definitionen.

    * `ROW FORMAT`: Anger Hive hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.

    * `STORED AS TEXTFILE LOCATION`: Talar om för Hive att data lagras i *exemplet/data* katalogen och att de lagras som text.

    * `SELECT`: Väljer antalet rader där kolumnen `t4` innehåller värdet `[ERROR]` . Den här instruktionen returnerar värdet `3` eftersom tre rader innehåller det här värdet.

    * `INPUT__FILE__NAME LIKE '%.log'`: Anger att Hive endast ska returnera data från filer som slutar med. log. Den här satsen begränsar sökningen till *exempel. log* -filen som innehåller data.

6. Välj det HDInsight-kluster som du vill använda för den här frågan från verktygsfältet Frågeredigeraren (som har ett liknande utseende i verktygsfältet för ad hoc-fråga). Ändra sedan **interaktiv** till **batch** (om det behövs) och välj **Skicka** för att köra uttrycken som ett Hive-jobb.

   **Sammanfattningen av Hive-jobbet** visas och visar information om det jobb som körs. Använd länken **Uppdatera** för att uppdatera jobb informationen tills **jobbets status** ändras till **slutförd**.

   ![Slutförd Sammanfattning av Hive-jobb, Hive-program, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Visa utdata för jobbet genom att välja **jobbets utdata** . Den visas `[ERROR] 3` , vilket är det värde som returneras av den här frågan.

### <a name="additional-example"></a>Ytterligare exempel

I följande exempel förlitar `log4jLogs` sig den tabell som du skapade i föregående procedur, [skapar du ett Hive-program](#create-a-hive-application).

1. Från **Server Explorer** högerklickar du på klustret och väljer **Skriv en Hive-fråga**.

2. Ange följande Hive-fråga:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa uttryck gör följande:

    * `CREATE TABLE IF NOT EXISTS`: Skapar en tabell om den inte redan finns. Eftersom `EXTERNAL` nyckelordet inte används skapar den här instruktionen en intern tabell. Interna tabeller lagras i Hive-datalagret och hanteras av Hive.

        > [!NOTE]  
        > Till skillnad från `EXTERNAL` tabeller, raderas även underliggande data när en intern tabell släpps.

    * `STORED AS ORC`: Lagrar data i *optimerade rad kolumn* format (Orc). ORC är ett mycket optimerat och effektivt format för att lagra Hive-data.

    * `INSERT OVERWRITE ... SELECT`: Markerar rader från `log4jLogs` tabellen som innehåller `[ERROR]` och infogar sedan data i `errorLogs` tabellen.

3. Ändra **interaktiv** till **batch** vid behov och välj sedan **Skicka**.

4. Kontrol lera att jobbet har skapats för tabellen genom att gå till **Server Explorer** och expandera **Azure**  >  **HDInsight**. Expandera ditt HDInsight-kluster och expandera sedan **Hive-databaser** som  >  **standard**. Tabellen **errorLogs** och tabellen **log4jLogs** visas.

## <a name="next-steps"></a>Nästa steg

Som du kan se ger HDInsight-verktygen för Visual Studio ett enkelt sätt att arbeta med Hive-frågor i HDInsight.

* Allmän information om Hive i HDInsight finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md)

* Information om andra sätt att arbeta med Hadoop i HDInsight finns i [använda MapReduce i Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

* Mer information om HDInsight Tools för Visual Studio finns i[använda data Lake verktyg för Visual Studio för att ansluta till Azure HDInsight och köra apache Hive frågor](apache-hadoop-visual-studio-tools-get-started.md)
