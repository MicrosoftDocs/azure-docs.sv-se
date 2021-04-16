---
title: 'Självstudie: Kom igång med att analysera data med en serverlös SQL-pool'
description: I den här självstudien lär du dig att analysera data med en serverlös SQL-pool med hjälp av data som finns i Spark-databaser.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567561"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analysera data med en serverlös SQL-pool

I den här självstudien lär du dig att analysera data med en serverlös SQL-pool. 

## <a name="the-built-in-serverless-sql-pool"></a>Den inbyggda serverlösa SQL-poolen

Med serverlösa SQL-pooler kan du använda SQL utan att behöva reservera kapacitet. Faktureringen för en serverlös SQL-pool baseras på mängden data som bearbetas för att köra frågan och inte antalet noder som används för att köra frågan.

Varje arbetsyta levereras med en förkonfigurerad serverlös **SQL-pool som kallas Inbyggd.** 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analysera nyc-taxidata med en serverlös SQL-pool

1. I Synapse Studio du till **hubben Utveckla**
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
1. Klicka på **Kör**. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med en serverlös Spark-pool](get-started-analyze-spark.md)
