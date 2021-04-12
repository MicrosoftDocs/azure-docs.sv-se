---
title: Använda Apache Hive som ETL-verktyg – Azure HDInsight
description: Använd Apache Hive för att extrahera, transformera och läsa in data (ETL) i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: bcf2f39423f033ccd5bfdb6bf51ebc89e254f802
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867822"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Använd Apache Hive som ett ETL-verktyg (Extract, Transform och Load)

Du måste vanligt vis rensa och transformera inkommande data innan du läser in dem i ett mål som passar för analys. Åtgärder för att extrahera, transformera och läsa in (ETL) används för att förbereda data och läsa in dem i ett data mål.  Apache Hive på HDInsight kan läsa ostrukturerade data, bearbeta data efter behov och sedan läsa in data i ett relationellt informations lager för system för besluts support. I den här metoden extraheras data från källan. Lagras sedan i anpassningsbart lagrings utrymme, till exempel Azure Storage blobbar eller Azure Data Lake Storage. Data omvandlas sedan med en sekvens av Hive-frågor. Mellanlagras sedan i Hive som förberedelse för Mass inläsning till mål data lagret.

## <a name="use-case-and-model-overview"></a>Översikt över användnings fall och modell

Följande bild visar en översikt över användnings fallet och modellen för ETL-Automation. Indata omvandlas för att generera lämpliga utdata.  Under omvandlingen ändras form, data typ och även språk.  ETL-processer kan konvertera Imperial till mått, ändra tids zoner och förbättra precisionen för att korrekt justera med befintliga data i målet. ETL-processer kan också kombinera nya data med befintliga data för att hålla rapporter aktuella, eller för att ge mer insikt i befintliga data. Program som rapporterings verktyg och tjänster kan sedan använda dessa data i det önskade formatet.

:::image type="content" source="./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png" alt-text="Apache Hive som ETL-arkitektur" border="false":::

Hadoop används vanligt vis i ETL-processer som importerar antingen ett massivt antal textfiler (som CSV: er). Eller en mindre men ofta ändrade antal textfiler, eller både och.  Hive är ett utmärkt verktyg som används för att förbereda data innan de läses in i data målet.  Med Hive kan du skapa ett schema över CSV och använda ett SQL-liknande språk för att generera MapReduce-program som interagerar med data.

De vanligaste stegen för att använda Hive för att göra ETL är följande:

1. Läs in data i Azure Data Lake Storage eller Azure Blob Storage.
2. Skapa en databas för metadatalagret (med Azure SQL Database) som ska användas av Hive för att lagra dina scheman.
3. Skapa ett HDInsight-kluster och Anslut data lagret.
4. Definiera schemat som ska användas vid Läs tid över data i data lagret:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformera data och Läs in dem i målet.  Det finns flera sätt att använda Hive under omvandlingen och inläsning:

    * Fråga och Förbered data med Hive och spara den som en CSV i Azure Data Lake Storage eller Azure Blob Storage.  Använd sedan ett verktyg som SQL Server Integration Services (SSIS) för att hämta dessa CSV: er och läsa in data i en mål Relations databas, till exempel SQL Server.
    * Fråga data direkt från Excel eller C# med hjälp av Hive ODBC-drivrutinen.
    * Använd [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) för att läsa de för beredda enkla CSV-filerna och läsa in dem i mål Relations databasen.

## <a name="data-sources"></a>Datakällor

Data källor är vanligt vis externa data som kan matchas med befintliga data i data lagret, till exempel:

* Data för sociala medier, loggfiler, sensorer och program som genererar datafiler.
* Data uppsättningar som hämtats från dataproviders, till exempel väder statistik eller säljar nummer.
* Strömmande data som har registrerats, filtrerats och bearbetats via ett lämpligt verktyg eller ramverk.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Mål för utdata

Du kan använda Hive för att mata ut data till olika typer av mål, inklusive:

* En Relations databas, till exempel SQL Server eller Azure SQL Database.
* Ett informations lager, till exempel Azure Synapse Analytics.
* Excel.
* Azure Table och Blob Storage.
* Program eller tjänster som kräver att data bearbetas i vissa format eller som filer som innehåller vissa typer av informations strukturer.
* Ett JSON-dokument lagrar som Azure Cosmos DB.

## <a name="considerations"></a>Överväganden

ETL-modellen används vanligt vis när du vill:

`*` Läs in strömmande data eller stora volymer av halv strukturerade eller ostrukturerade data från externa källor till ett befintligt databas-eller informations system.
`*` Rensa, transformera och verifiera data innan du läser in dem, kanske genom att använda mer än ett omvandlings pass genom klustret.
`*` Generera rapporter och visualiseringar som uppdateras regelbundet. Om rapporten till exempel tar för lång tid att generera under dagen kan du schemalägga rapporten så att den körs på natten. Om du vill köra en Hive-fråga automatiskt kan du använda [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) och PowerShell.

Om målet för data inte är en databas kan du skapa en fil i rätt format i frågan, till exempel en CSV. Den här filen kan sedan importeras till Excel eller Power BI.

Om du behöver köra flera åtgärder på data som en del av ETL-processen bör du fundera på hur du hanterar dem. Med åtgärder som styrs av ett externt program, i stället för ett arbets flöde i lösningen, bestämmer du om vissa åtgärder kan utföras parallellt. Och för att identifiera när varje jobb slutförs. Användning av en arbets flödes mekanism som Oozie i Hadoop kan vara enklare än att försöka dirigera en sekvens med åtgärder med hjälp av externa skript eller anpassade program.

## <a name="next-steps"></a>Nästa steg

* [ETL i skala](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
