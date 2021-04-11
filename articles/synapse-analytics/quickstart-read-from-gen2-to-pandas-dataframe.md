---
title: 'Snabb start: läsa data från ADLS Gen2 till Pandas dataframe'
description: Läs data från ett Azure Data Lake Storage Gen2-konto till en Pandas-dataframe med python i Synapse Studio i Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969878"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Snabb start: läsa data från ADLS Gen2 till Pandas dataframe i Azure Synapse Analytics

I den här snabb starten får du lära dig hur du enkelt använder python för att läsa data från en Azure Data Lake Storage (ADLS) Gen2 till en Pandas-dataframe i Azure Synapse Analytics.

Från en Synapse Studio-anteckningsbok kommer du att:

- ansluta till en behållare i Data Lake Storage Gen2 som är länkad till din Azure Synapse Analytics-arbetsyta
- Läs data från en PySpark-anteckningsbok med `spark.read.load`
- konvertera data till en Pandas-dataframe med `.toPandas()`

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
- Synapse Analytics-arbetsytan med Data Lake Storage Gen2 konfigurerad som standard lagring – du måste vara för **lagring av BLOB-data** för det data Lake Storage Gen2-filsystem som du arbetar med. Mer information om hur du skapar en arbets yta finns i [skapa en Synapse-arbetsyta](get-started-create-workspace.md).
- Apache Spark pool i arbets ytan – mer information finns i [skapa en server lös Apache Spark pool](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Ladda upp exempel data till ADLS Gen2

1. I Azure Portal skapar du en behållare i samma Data Lake Storage Gen2 som används av Synapse Studio. Du kan hoppa över det här steget om du vill använda det länkade standard lagrings kontot i din Azure Synapse Analytics-arbetsyta.

1. I Synapse Studio klickar du på **data**, väljer den **länkade** fliken och väljer behållaren under **Azure Data Lake Storage Gen2**.

1. Ladda ned exempel filen [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) och ladda upp den till behållaren.

1. Välj den överförda filen, klicka på **Egenskaper** och kopiera värdet för **sökvägen till ABFSS** .

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Läsa data från ADLS Gen2 till en Pandas-dataframe

1. Klicka på **utveckla** i det vänstra fönstret.

1. Klicka på **+** och välj "notebook" om du vill skapa en ny antecknings bok.

1. I **bifoga till** väljer du din Apache Spark-pool. Klicka på **skapa Apache Spark pool** om du inte har någon.

1. Klistra in följande python-kod i cellen Notebook Code och infoga sökvägen till ABFSS som du kopierade tidigare:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Kör cellen.

Efter några minuter bör texten som visas se ut ungefär så här.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Synapse Analytics?](overview-what-is.md)
- [Kom igång med Azure Synapse Analytics](get-started.md)
- [Skapa en server lös Apache Spark-pool](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
