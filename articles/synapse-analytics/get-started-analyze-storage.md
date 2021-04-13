---
title: 'Självstudie: Kom igång med att analysera data i lagringskonton'
description: I den här självstudien lär du dig att analysera data som finns i ett lagringskonto.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b88a7e6a9851018fce255fac0e39a30563b9bf4
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363843"
---
# <a name="analyze-data-in-a-storage-account"></a>Analysera data i ett lagringskonto

I den här självstudien lär du dig att analysera data som finns i ett lagringskonto.

## <a name="overview"></a>Översikt

Hittills har vi gått in på scenarier där data finns i databaser på arbetsytan. Nu ska vi visa hur du arbetar med filer i lagringskonton. I det här scenariot använder vi det primära lagringskontot för arbetsytan och containern som vi angav när vi skapade arbetsytan.

* Namnet på lagringskontot: **contosolake**
* Namnet på containern i lagringskontot: **användare**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Skapa CSV- och Parquet-filer i ditt lagringskonto

Kör följande kod i en notebook-kod i en ny kodcell. Det skapar en CSV-fil och en parquet-fil i lagringskontot.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analysera data i ett lagringskonto

Du kan analysera data i arbetsytans ADLS Gen2-standardkonto eller länka ett ADLS Gen2- eller Blob Storage-konto till din arbetsyta via "**Manage**" > "**Linked Services**" > "**New**" (Stegen nedan refererar till det primära ADLS Gen2-kontot).

1. I Synapse Studio du till **datahubben** och väljer sedan **Länkad**.
1. Gå till **Azure Data Lake Storage Gen2**  >  **myworkspace (Primär – contosolake).**
1. Välj **användare (primär).** Du bör se **mappen NYCMapp.** I bör du se två mappar med **namnet PassengerCountStats_csvformat** och **PassengerCountStats_parquetformat**.
1. Öppna **mappen PassengerCountStats_parquetformat.** Inuti visas en parquet-fil med ett namn som `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Högerklicka på **.parquet** och välj ny **notebook-dator** och välj sedan **Läs in till DataFrame.** En ny notebook-dator skapas med en cell så här:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Anslut till Spark-poolen med namnet **Spark1.** Kör cellen.
1. Välj tillbaka till **mappen användare.** Högerklicka på **.parquet-filen igen** och välj sedan **Nytt SQL-skript**  >  **VÄLJ DE 100 översta raderna.** Det skapar ett SQL-skript så här:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    I skriptfönstret kontrollerar du att fältet **Anslut till** är inställt på den inbyggda serverlösa **SQL-poolen.**

1. Kör skriptet.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Orkestrera aktiviteter med pipelines](get-started-pipelines.md)
