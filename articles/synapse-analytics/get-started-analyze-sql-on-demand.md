---
title: 'Självstudie: komma igång med att analysera data med en server lös SQL-pool'
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
ms.openlocfilehash: c8a0ad3eb578280ea29d94dfbb5e74d873568541
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259650"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analysera data med en server lös SQL-pool

I den här självstudien får du lära dig hur du analyserar data med en server lös SQL-pool. 

## <a name="the-built-in-serverless-sql-pool"></a>Den inbyggda SQL-poolen utan Server

SQL-pooler utan Server gör att du kan använda SQL utan att behöva reservera kapacitet. Faktureringen för en server lös SQL-pool baseras på mängden data som bearbetas för att köra frågan och inte antalet noder som används för att köra frågan.

Varje arbets yta levereras med en förkonfigurerad server utan SQL-pool som kallas **inbyggd**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analysera NYC taxi-data i Blob Storage med hjälp av SQL-pool utan Server

I det här avsnittet ska du använda en server lös SQL-pool för att analysera NYC taxi-data i ett Azure Blob Storage-konto.

1. Gå till **utveckla** hubben i Synapse Studio
1. Skapa ett nytt SQL-skript.
1. Klistra in följande kod i skriptet.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Klicka på **Kör**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med en server lös Spark-pool](get-started-analyze-spark.md)
