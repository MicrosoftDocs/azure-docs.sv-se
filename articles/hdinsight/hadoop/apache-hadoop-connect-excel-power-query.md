---
title: Ansluta Excel till Apache Hadoop med Power Query – Azure HDInsight
description: Lär dig hur du utnyttjar Business Intelligence-komponenter och använder Power Query för Excel för att komma åt data som lagras i Hadoop på HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866819"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Ansluta Excel till Apache Hadoop med hjälp av Power Query

En viktig funktion i Microsofts stor data lösning är integreringen av Microsoft Business Intelligence-komponenter (BI) med Apache Hadoop kluster i Azure HDInsight. Ett primärt exempel är möjligheten att ansluta Excel till det Azure Storage konto som innehåller de data som är kopplade till ditt Hadoop-kluster med hjälp av Microsoft Power Query för Excel-tillägget. Den här artikeln beskriver hur du konfigurerar och använder Power Query för att fråga efter data som är associerade med ett Hadoop-kluster som hanteras med HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* En arbets station som kör Windows 10, 7, Windows Server 2008 R2 eller ett senare operativ system.
* Microsoft 365 appar för företag, Office 2016, Office 2013 Professional Plus, Excel 2013 fristående eller Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Installera Microsoft Power Query

Power Query kan importera data som har skrivits ut eller som har genererats av ett Hadoop-jobb som körs på ett HDInsight-kluster.

I Excel 2016 har Power Query integrerats i menyfliksområdet under avsnittet Hämta & Transform. För äldre versioner av Excel laddar du ned Microsoft Power Query för Excel från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) och installerar det.

## <a name="import-hdinsight-data-into-excel"></a>Importera HDInsight-data till Excel

Med Power Query-tillägget för Excel kan du enkelt importera data från HDInsight-klustret till Excel, där BI-verktyg som PowerPivot och Power Map kan användas för att kontrol lera, analysera och presentera data.

1. Starta Excel.

1. Skapa en ny tom arbets bok.

1. Utför följande steg baserat på Excel-versionen:

   * Excel 2016

     * Välj > **data**  >  **Hämta data**  >  **från Azure**  >  **från Azure HDInsight (HDFS)**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI. PowerQuery. SelectHdiSource. 2016" border="true":::

   * Excel 2013/2010

     * Välj **Power Query**  >  **från Azure**  >  **från Microsoft Azure HDInsight**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI. PowerQuery. SelectHdiSource" border="true":::

       **Obs:** Om du inte ser **Power Query** menyn går du till **fil**  >  **alternativ**  >  **tillägg** och väljer **com-tillägg** i list rutan **Hantera** längst ned på sidan. Välj knappen **gå...** och kontrol lera att rutan för tillägget Power Query för Excel har marker ATS.

       **Obs:** Med Power Query kan du också importera data från HDFS genom att välja **från andra källor**.

1. I text rutan **konto namn eller URL** i dialog rutan för **Azure HDInsight (HDFS)** anger du namnet på det Azure Blob Storage-konto som är associerat med klustret. Välj sedan **OK**. Det här kontot kan vara standard lagrings kontot eller ett länkat lagrings konto.  Formatet är `https://StorageAccountName.blob.core.windows.net/`.

1. För **konto nyckel** anger du nyckeln för Blob Storage-kontot och väljer sedan **Anslut**. (Du behöver bara ange konto informationen första gången du öppnar det här arkivet.)

1. I **navigerings** fönstret till vänster i Frågeredigeraren dubbelklickar du på den behållare för Blob Storage-behållare som är kopplad till klustret. Behållarens namn är som standard samma namn som kluster namnet.

1. Leta upp **HiveSampleData.txt** i kolumnen **namn** (mappsökvägen är **... /Hive/Warehouse/hivesampletable/**) och välj sedan **binär** till vänster om HiveSampleData.txt. HiveSampleData.txt levereras med alla kluster. Du kan också använda en egen fil.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="Importera data från HDI Excel Power Query" border="true":::

1. Om du vill kan du byta namn på kolumn namnen. När du är klar väljer du **stäng & belastning**.  Data har lästs in i din arbets bok:

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI Excel Power Query-importerad tabell" border="true":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Power Query för att hämta data från HDInsight till Excel. På samma sätt kan du hämta data från HDInsight till Azure SQL Database. Det går också att ladda upp data till HDInsight. Mer information finns i följande artiklar:

* [Visualisera Apache Hive data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva Hive-frågedata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använd Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut till Azure HDInsight och kör Apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använd Azure HDInsight-verktyget för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).
