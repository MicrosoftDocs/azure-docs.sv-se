---
title: 'Snabb start: komma igång med att analysera med Spark'
description: I den här självstudien får du lära dig att analysera data med Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 8559bd0a354a64872e58d014d1027ed971773b60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655350"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

I den här självstudien får du lära dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure-Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **nytt** 
1. Ange **Spark1** som **namn på Apache Spark pool** .
1. För **Node-storlek** anger du **liten**.
1. För **antal noder** ställer du in minst 3 och maximalt 3
1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

## <a name="understanding-serverless-apache-spark-pools"></a>Förstå Server lös Apache Spark pooler

En server lös Spark-pool är ett sätt att ange hur en användare vill arbeta med Spark. När du börjar använda en pool skapas en spark-session vid behov. Poolen styr hur många Spark-resurser som kommer att användas av den sessionen och hur länge sessionen är bra sist innan den pausas automatiskt. Du betalar för Spark-resurser som används under den sessionen, inte för själva poolen. På så sätt kan du arbeta med Spark i en spark-pool utan att behöva oroa dig för att hantera kluster. Detta liknar hur en server lös SQL-pool fungerar.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analysera NYC taxi-data i Blob Storage med Spark

1. Gå till **utveckla** hubben i Synapse Studio
2. Skapa en newnNotebook med standard språket inställt på **PySpark (python)**.
3. Skapa en ny Code-cell och klistra in följande kod i cellen.
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. I antecknings boken, på menyn **Anslut till** , väljer du den **Spark1** -server som vi skapade tidigare.
1. Välj **Kör** i cellen. Synapse kommer att starta en ny Spark-session för att köra den här cellen om det behövs. Om en ny Spark-session behövs tar det en första sekund att skapa två sekunder. 
1. Om du bara vill se schemat för dataframe kör du en cell med följande kod:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Data är tillgängliga via dataframe med namnet **data**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Lägg till en ny till antecknings boken och ange sedan följande kod:

    ```py
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande kod. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör cellen för att visa de NYC taxi-data som vi läste in i **nyctaxi** Spark-databasen.
1. Skapa en ny kod cell och ange följande kod. Kör sedan cellen för att utföra samma analys som vi gjorde tidigare med den dedikerade SQL-poolen **SQLPOOL1**. Den här koden sparar och visar resultatet av analysen i en tabell med namnet **nyctaxi. passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. I cell resultaten väljer du **diagram** för att visa data som visualiseras.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med dedikerad SQL-pool](get-started-analyze-sql-pool.md)
