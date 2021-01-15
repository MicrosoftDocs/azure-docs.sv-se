---
title: 'Självstudie: komma igång med att analysera data med Server lös SQL-pool'
description: I den här självstudien får du lära dig hur du analyserar data med en server lös SQL-pool med hjälp av data som finns i Spark-databaser.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209415"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analysera data med Server lös SQL-pool i Azure Synapse Analytics

I den här självstudien får du lära dig hur du analyserar data med Server lös SQL-pool med hjälp av data som finns i Spark-databaser. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analysera NYC taxi-data i Blob Storage med hjälp av SQL-pool utan Server

1. I **data** hubben under **länkad** högerklickar du på **Azure Blob Storage > exempel data uppsättningar > Nyc_tlc_yellow**, **nytt SQL-skript** och väljer **Välj de översta 100 raderna**
1. Då skapas ett nytt SQL-skript med följande kod:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Klicka på **Kör**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analysera NYC taxi-data i Spark-databaser med Server lös SQL-pool

Tabeller i Spark-databaser visas automatiskt och de kan frågas av Server lös SQL-pool.

1. Gå till **utveckla** hubben i Synapse Studio och skapa ett nytt SQL-skript.
1. Ange **Anslut till** till den **inbyggda SQL-** poolen utan server.
1. Klistra in följande text i skriptet och kör skriptet.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Första gången du kör en fråga som använder en server lös SQL-pool tar det cirka 10 sekunder för SQL-poolen utan server att samla de SQL-resurser som krävs för att köra dina frågor. Efterföljande frågor blir mycket snabbare.
  


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data i lagring](get-started-analyze-storage.md)
