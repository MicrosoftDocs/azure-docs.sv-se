---
title: 'Snabb start: skapa en server lös Apache Spark pool med hjälp av webb verktyg'
description: Den här snabb starten visar hur du använder webb verktygen för att skapa en server lös Apache Spark-pool i Azure Synapse Analytics och hur du kör en Spark SQL-fråga.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669486"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Snabb start: skapa en server lös Apache Spark pool i Azure Synapse Analytics med hjälp av webb verktyg

I den här snabb starten får du lära dig hur du skapar en server lös Apache Spark pool i Azure Synapse med hjälp av webb verktyg. Du lär dig sedan att ansluta till Apache Spark-poolen och köra Spark SQL-frågor mot filer och tabeller. Apache Spark möjliggör snabb data-analys och kluster-computing med minnesintern bearbetning. Information om spark i Azure Synapse finns i [Översikt: Apache Spark på Azure Synapse](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> Faktureringen för Spark-instanser beräknas per minut, oavsett om du använder dem eller inte. Se till att stänga av Spark-instansen när du är färdig med den, eller ange en kort tids gräns. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse Analytics-arbetsyta](quickstart-create-workspace.md)
- [Server lös Apache Spark pool](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-notebook"></a>Skapa en notebook-fil

En antecknings bok är en interaktiv miljö som har stöd för olika programmeringsspråk. Med antecknings boken kan du interagera med dina data, kombinera kod med markdown, text och utföra enkla visualiseringar.

1. Från vyn Azure Portal för den Azure Synapse-arbetsyta som du vill använda väljer du **Starta Synapse Studio**.
2. När Synapse Studio har lanserats väljer du **utveckla**. Välj sedan **+** ikonen "" om du vill lägga till en ny resurs.
3. Därifrån väljer du **antecknings bok**. En ny antecknings bok skapas och öppnas med ett automatiskt genererat namn.
 
     ![Ny antecknings bok](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Ny antecknings bok")

4. I fönstret **Egenskaper** anger du ett namn för antecknings boken.
5. Klicka på **publicera** i verktygsfältet.
6. Om det bara finns en Apache Spark pool i din arbets yta, är den markerad som standard. Använd List rutan för att välja rätt Apache Spark pool om ingen är markerad.
7. Klicka på **Lägg till kod**. Standard språket är `Pyspark` . Du kommer att använda en blandning av Pyspark och Spark SQL, så att standard valet är bra. Andra språk som stöds är Scala och .NET för Spark.
8. Därefter skapar du ett enkelt Spark DataFrame-objekt som du kan ändra. I det här fallet skapar du den från kod. Det finns tre rader och tre kolumner:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Kör nu cellen med någon av följande metoder:

   - Tryck på **SKIFT + RETUR**.
   - Välj den blå uppspelnings ikonen till vänster om cellen.
   - Välj knappen **Kör alla** i verktygsfältet.

       ![Skapa data ram objekt](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Om instansen av Apache Spark-poolen inte redan körs startas den automatiskt. Du kan se instans status för Apache Spark bassäng under den cell som du kör och även på status panelen längst ned i antecknings boken. Beroende på Poolens storlek bör det ta 2-5 minuter att starta. När koden har körts visas informationen i cellen som visar hur lång tid det tog att köra och dess körning. I utmatnings cellen visas utdata.

    ![Utdata från att köra en cell](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Informationen finns nu i en DataFrame där du kan använda data på många olika sätt. Du kommer att behöva den i olika format för resten av den här snabb starten.
12. Ange koden nedan i en annan cell och kör den. då skapas en spark-tabell, en CSV-fil och en Parquet-fil med kopior av data:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Om du använder Storage Explorer är det möjligt att se effekten av de två olika sätten att skriva en fil som används ovan. När inget fil system anges används standardvärdet i det här fallet `default>user>trusted-service-user>demo_df` . Data sparas på platsen för det angivna fil systemet.

    Observera i formaten "CSV" och "Parquet" och skriv åtgärder som en katalog skapas med många partitionerade filer.

    ![Vyn Storage Explorer för utdata](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "Vyn Storage Explorer för utdata")

    ![Skärm bild som visar standard > demodata > demo_df Sök väg.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Vyn Storage Explorer för utdata")

## <a name="run-spark-sql-statements"></a>Kör Spark SQL-instruktioner

Structured Query Language (SQL) är det vanligaste språket som används för att skicka frågor till och definiera data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

1. Klistra in följande kod i en tom cell och kör sedan koden. Kommandot visar tabellerna i poolen.

   ```sql
   %%sql
   SHOW TABLES
   ```

   När du använder en bärbar dator med Azure Synapse Apache Spark-poolen får du en `sqlContext` för inställning som du kan använda för att köra frågor med Spark SQL. `%%sql` instruerar antecknings boken att använda för inställningen `sqlContext` för att köra frågan. Frågan hämtar de översta 10 raderna från en system tabell som ingår i alla Azure Synapse Apache Spark pooler som standard.

2. Kör ytterligare en fråga för att visa data i `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Koden ger två utdatakolumner, en som innehåller data, som visar vyn jobb.

    Som standard visar vyn resultat ett rutnät. Men det finns en vy växlaren under rutnätet som gör att vyn kan växla mellan rutnät och diagramvyer.

    ![Fråga utdata i Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Fråga utdata i Azure Synapse Spark")

3. I **vyn** växlaren väljer du **diagram**.
4. Välj ikonen **visnings alternativ** längst till höger.
5. I fältet **diagram typ** väljer du stapeldiagram.
6. I kolumn fältet X-axel väljer du "State".
7. I kolumn fältet Y-axel väljer du "lön".
8. I fältet **agg regering** väljer du till "medel".
9. Välj **Använd**.

   ![Diagramets utdata i Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Diagramets utdata i Azure Synapse Spark")

10. Det är möjligt att du får samma upplevelse av att köra SQL men utan att behöva byta språk. Du kan göra detta genom att ersätta SQL-cellen ovan med den här PySpark-cellen. resultatet av utdata är detsamma eftersom **visnings** kommandot används:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Varje cell som kördes tidigare hade alternativet att gå till **Historik Server** och **övervakning**. Genom att klicka på länkarna går du till olika delar av användar upplevelsen.

> [!NOTE]
> En del av den [Apache Spark officiella dokumentationen](https://spark.apache.org/docs/latest/) använder Spark-konsolen, som inte är tillgänglig på Synapse Spark. Använd [antecknings boken](quickstart-apache-spark-notebook.md) eller [IntelliJ](./spark/intellij-tool-synapse.md) -upplevelser i stället.

## <a name="clean-up-resources"></a>Rensa resurser

Azure Synapse sparar dina data i Azure Data Lake Storage. Du kan på ett säkert sätt låta en spark-instans stängas av när den inte används. Du debiteras för en server lös Apache Spark pool så länge den körs, även om den inte används. 

Eftersom avgifterna för poolen är många gånger mer än avgifterna för lagring, är det ekonomiskt klokt att låta Spark-instanser stängas av när de inte används.

Om du vill se till att Spark-instansen stängs av avslutar du alla anslutna sessioner (Notebooks). Poolen stängs av när **tids gränsen för inaktivitet** i Apache Spark-poolen har uppnåtts. Du kan också välja **avsluta sessionen** från statusfältet längst ned i antecknings boken.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en server lös Apache Spark pool och kör en grundläggande Spark SQL-fråga.

- [Azure Synapse Analytics](overview-what-is.md)
- [Dokumentation om .NET för Apache Spark](/dotnet/spark)



