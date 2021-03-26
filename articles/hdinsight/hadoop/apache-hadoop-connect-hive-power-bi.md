---
title: Visualisera Apache Hive data med Power BI – Azure HDInsight
description: Lär dig hur du använder Microsoft Power BI för att visualisera Hive-data som bearbetas av Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: bb448a4befb15618485b2b5951222761180a1f22
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866751"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualisera Apache Hive-data med Microsoft Power BI och ODBC i Azure HDInsight

Lär dig hur du ansluter Microsoft Power BI Desktop till Azure HDInsight med ODBC och visualiserar Apache Hive data.

> [!IMPORTANT]
> Du kan utnyttja Hive ODBC-drivrutinen för att importera via den allmänna ODBC-anslutningen i Power BI Desktop. Det rekommenderas dock inte för BI-arbetsbelastningar med icke-interaktiv natur för Hive-frågemotor. [Hdinsights interaktiva Query Connector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Spark Connector](/power-bi/spark-on-hdinsight-with-direct-connect) är bättre för deras prestanda.

I den här artikeln läser du in data från en `hivesampletable` Hive-tabell till Power BI. Hive-tabellen innehåller vissa mobil telefon användnings data. Sedan kan du rita användnings data på en världs karta:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI kart rapporten" border="true":::

Informationen gäller även för den nya [interaktiva frågans](../interactive-query/apache-interactive-query-get-started.md) kluster typ. Information om hur du ansluter till en interaktiv HDInsight-fråga med direkt frågor finns i [visualisera interaktiva frågor Hive-data med Microsoft Power BI med direkt fråga i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du går igenom den här artikeln måste du ha följande objekt:

* HDInsight-kluster. Klustret kan antingen vara ett HDInsight-kluster med Hive eller ett nyligen publicerat kluster för interaktiv fråga. Information om hur du skapar kluster finns i [skapa kluster](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Du kan ladda ned en kopia från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Se [skapa HIVE ODBC-datakälla](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Läs in data från HDInsight

**Hivesampletable** Hive-tabellen innehåller alla HDInsight-kluster.

1. Starta Power BI Desktop.

1. I den översta menyn går du till **Start**  >  **Hämta data**  >  **mer.**...

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Excel Power BI öppna data" border="true":::

1. I dialog rutan **Hämta data** väljer du **annan** från vänster, väljer **ODBC** från höger och väljer sedan **Anslut** längst ned.

1. I dialog rutan **från ODBC** väljer du det data käll namn som du skapade i det sista avsnittet i list rutan. Välj sedan **OK**.

1. För den första användningen öppnas en **ODBC-drivrutins** dialog ruta. Välj **standard eller anpassad** på den vänstra menyn. Välj sedan **Anslut** för att öppna **Navigator**.

1. I dialog rutan **navigering** expanderar du **ODBC > HIVE > standard**, väljer **hivesampletable** och väljer sedan **load**.

## <a name="visualize-data"></a>Visualisera data

Fortsätt från den senaste proceduren.

1. I fönstret visualiseringar väljer du **karta**. det är en jordglob-ikon.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png" alt-text="Power BI anpassnings rapport för HDInsight" border="true":::

1. I fönstret **fält** väljer du **land** och **devicemake**. Du kan se data som ritas på kartan.

1. Expandera kartan.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du visualiserar data från HDInsight med hjälp av Power BI.  Mer information finns i följande artiklar:

* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med hjälp av Power Query](apache-hadoop-connect-excel-power-query.md).
* [Visualisera interaktiva frågor Apache Hive data med Microsoft Power BI med direkt fråga](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)