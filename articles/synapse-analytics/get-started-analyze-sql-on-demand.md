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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312798"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analysera data med en server lös SQL-pool

I den här självstudien får du lära dig hur du analyserar data med en server lös SQL-pool. 

## <a name="the-built-in-serverless-sql-pool"></a>Den inbyggda SQL-poolen utan Server

SQL-pooler utan Server gör att du kan använda SQL utan att behöva reservera kapacitet. Faktureringen för en server lös SQL-pool baseras på mängden data som bearbetas för att köra frågan och inte antalet noder som används för att köra frågan.

Varje arbets yta levereras med en förkonfigurerad server utan SQL-pool som kallas **inbyggd**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analysera NYC taxi-data med en server lös SQL-pool


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
