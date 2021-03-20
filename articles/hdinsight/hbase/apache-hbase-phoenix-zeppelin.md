---
title: Kör Apache grundläggande frågor i Azure HDInsight med Apache Phoenix
description: Lär dig hur du använder Apache Zeppelin för att köra apache Base-frågor med Phoenix.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944769"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Använda Apache Zeppelin för att köra Apache Phoenix frågor över Apache HBase i Azure HDInsight

Apache Phoenix är ett stort parallellt Relations databas lager som bygger på HBase. Med Phoenix kan du använda SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutiner under för att skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser.  Du kan också använda Phoenix för att uppdatera rader individuellt och i grupp. Phoenix använder en NOSQL-intern kompilering i stället för att använda MapReduce för att kompilera frågor, vilket gör det möjligt att skapa låg latens program ovanpå HBase.

Apache Zeppelin är en webbaserad antecknings bok med öppen källkod som gör att du kan skapa data drivna, samarbets dokument med interaktiva data analyser och språk som SQL och Scala. Det hjälper data utvecklare & data experter att utveckla, organisera, köra och dela kod för data behandling. Det gör att du kan visualisera resultat utan att referera till kommando raden eller att använda kluster informationen.

HDInsight-användare kan använda Apache Zeppelin för att fråga Phoenix-tabeller. Apache Zeppelin är integrerat med HDInsight-kluster och det finns inga ytterligare steg att använda det. Skapa helt enkelt en Zeppelin-anteckningsbok med JDBC-tolken och börja skriva dina Phoenix SQL-frågor

## <a name="prerequisites"></a>Förutsättningar

Ett Apache HBase-kluster i HDInsight. Se [Kom igång med Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Skapa en Apache Zeppelin-anteckning

1. Ersätt `CLUSTERNAME` med namnet på klustret i följande URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Ange sedan URL-adressen i en webbläsare. Ange ditt användar namn och lösen ord för kluster inloggning.

1. Välj **Skapa ny anteckning** på sidan Zeppelin.

    ![Zeppelin för interaktiv HDInsight-fråga](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. I dialog rutan **Skapa ny anteckning** skriver eller väljer du följande värden:

    - Kommentars namn: Ange ett namn för anteckningen.
    - Standard tolk: Välj **JDBC** i list rutan.

    Välj sedan **Skapa anteckning**.

1. Se till att antecknings bokens huvud visar en ansluten status. Den betecknas av en grön prick i det övre högra hörnet.

    ![Status för Zeppelin Notebook](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Status för Zeppelin Notebook")

1. Skapa en HBase-tabell. Ange följande kommando och tryck sedan på **SKIFT + RETUR**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    **% JDBC (Phoenix)** -instruktionen på den första raden instruerar den bärbara datorn att använda JDBC-tolken Phoenix.

1. Visa skapade tabeller.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Infoga värden i tabellen.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Fråga tabellen.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Ta bort en post.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Ta bort tabellen.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Nästa steg

- [Apache Phoenix stöder nu Zeppelin i Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html)