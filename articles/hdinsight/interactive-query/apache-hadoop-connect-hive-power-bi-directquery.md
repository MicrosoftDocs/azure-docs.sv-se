---
title: Se interaktiva Query Hive-data med Power BI i Azure HDInsight
description: Använd Microsoft Power BI för att visualisera interaktiva Hive-frågedata från Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 05199968339329632c2e68e9604e3f5308e8b12b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871664"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualisera interaktiva frågor Apache Hive data med Microsoft Power BI med direkt fråga i HDInsight

Den här artikeln beskriver hur du ansluter Microsoft Power BI till interaktiva Azure HDInsight-frågegrupper och visualiserar Apache Hive data med direkt fråga. Exemplet läser in data från en Hive- `hivesampletable` tabell till Power BI. `hivesampletable`Hive-tabellen innehåller vissa mobil telefon användnings data. Sedan kan du rita användnings data på en världs karta:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI kart rapporten" border="true":::

Du kan utnyttja [Apache Hive ODBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-power-bi.md) för att importera via den allmänna ODBC-anslutningen i Power BI Desktop. Det rekommenderas dock inte för BI-arbetsbelastningar med icke-interaktiv natur för Hive-frågemotor. [Hdinsights interaktiva Query Connector](./apache-hadoop-connect-hive-power-bi-directquery.md) och [HDInsight Apache Spark Connector](/power-bi/spark-on-hdinsight-with-direct-connect) är bättre för deras prestanda.

## <a name="prerequisites"></a>Förutsättningar
Innan du går igenom den här artikeln måste du ha följande objekt:

* **HDInsight-kluster**. Klustret kan antingen vara ett HDInsight-kluster med Apache Hive eller ett nyligen publicerat kluster för interaktiv fråga. Information om hur du skapar kluster finns i [skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Du kan ladda ned en kopia från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Läs in data från HDInsight

`hivesampletable`Hive-tabellen innehåller alla HDInsight-kluster.

1. Starta Power BI Desktop.

2. I meny raden går du till **Start**  >  **Hämta data**  >  **mer.**...

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Power BI hämta data mer" border="true":::

3. I fönstret **Hämta data** anger du **HDInsight** i sökrutan.  

4. Välj **HDInsight interaktiv fråga** i Sök resultaten och välj sedan **Anslut**.  Om du inte ser en **interaktiv HDInsight-fråga** måste du uppdatera Power BI Desktop till den senaste versionen.

5. Välj **Fortsätt** för att stänga dialog rutan **Anslut till en tjänst från tredje part** .

6. I fönstret **HDInsight interaktiv fråga** anger du följande information och väljer sedan **OK**:

    |Egenskap | Värde |
    |---|---|
    |Server |Ange kluster namnet, till exempel *myiqcluster.azurehdinsight.net*.|
    |Databas |Ange **standard** för den här artikeln.|
    |Läge för dataanslutning |Välj **DirectQuery** för den här artikeln.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png" alt-text="Interaktiv HDInsight-fråga Power BI DirectQuery Connect" border="true":::

7. Ange HTTP-autentiseringsuppgifterna och välj sedan **Anslut**. Standard användar namnet är **admin**.

8. I fönstret **navigatör** i det vänstra fönstret väljer du **hivesampletale**.

9. Välj **Läs in** från huvud fönstret.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png" alt-text="Interaktiv HDInsight-fråga Power BI hivesampletable" border="true":::

## <a name="visualize-data-on-a-map"></a>Visualisera data på en karta

Fortsätt från den senaste proceduren.

1. I fönstret visualiseringar väljer du **karta**, ikonen jordglob. En allmän karta visas sedan i huvud fönstret.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png" alt-text="Power BI anpassnings rapport för HDInsight" border="true":::

2. I fönstret fält väljer du **land** och **devicemake**. En världs karta med data punkter visas i huvud fönstret efter en liten stund.

3. Expandera kartan.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du visualiserar data från HDInsight med Microsoft Power BI.  Mer information om data visualisering finns i följande artiklar:

* [Visualisera Apache Hive data med Microsoft Power BI med ODBC i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Använd Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med hjälp av Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Anslut till Azure HDInsight och kör Apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight-verktyget för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).