---
title: Utforska data i Hive-tabeller med Hive-frågor – team data science process
description: Använd exempel på Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321121"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Utforska data i Hive-tabeller med Hive-frågor

Den här artikeln innehåller exempel på Hive-skript som används för att utforska data i Hive-tabeller i ett HDInsight Hadoop-kluster.

Den här uppgiften är ett steg i [processen för team data vetenskap](overview.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapat ett Azure Storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Etablerade ett anpassat Hadoop-kluster med HDInsight-tjänsten. Om du behöver instruktioner, se [anpassa Azure HDInsight Hadoop kluster för avancerad analys](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Data har laddats upp till Hive-tabeller i Azure HDInsight Hadoop kluster. Om den inte har det följer du anvisningarna i [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) för att ladda upp data till Hive-tabeller först.
* Fjärråtkomst till klustret har Aktiver ATS. Om du behöver instruktioner, se [åtkomst till Head-noden i Hadoop-klustret](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Om du behöver instruktioner om hur du skickar Hive-frågor, se [så här skickar du Hive-frågor](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exempel skript för Hive-frågor för data utforskning
1. Hämta antalet observationer per partition  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Få antalet observationer per dag  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Hämta nivåerna i en kategoriska-kolumn  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Hämta antalet nivåer i kombination av två kategoriska-kolumner  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Hämta fördelningen för numeriska kolumner  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrahera poster från att koppla ihop två tabeller
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Ytterligare fråge skript för data scenarier för taxi resor
Exempel på frågor som är speciella för [NYC taxi rese data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenarier finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Dessa frågor har redan angivet data schema och är redo att skickas till att köras.