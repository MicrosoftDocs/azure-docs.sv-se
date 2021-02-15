---
title: Data scenarier som involverar Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå de olika scenarier och verktyg som använder vilka data som kan matas in, bearbetas, hämtas och visualiseras i Data Lake Storage Gen2 (tidigare kallat Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1d22a992ac4ae69c8541b6efbaee58340f48caa4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517883"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Använda Azure Data Lake Storage Gen2 för Big data-krav

Det finns fyra viktiga steg i stor data bearbetning:

> [!div class="checklist"]
> * Mata in stora mängder data i ett data lager i real tid eller i batchar
> * Bearbetar data
> * Hämtar data
> * Visualisera data

I den här artikeln beskrivs alternativen och verktygen för varje bearbetnings fas.

En fullständig lista över Azure-tjänster som du kan använda med Azure Data Lake Storage Gen2 finns i [integrera Azure Data Lake Storage med Azure-tjänster](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Mata in data i Data Lake Storage Gen2

I det här avsnittet beskrivs de olika data källorna och de olika sätt som data kan matas in i ett Data Lake Storage Gen2-konto.

![Mata in data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Mata in data i Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-data

Detta representerar mindre data uppsättningar som används för prototypering av stor data program. Det finns olika sätt att mata in ad hoc-data beroende på data källan. 

Här är en lista med verktyg som du kan använda för att mata in ad hoc-data.

| Datakälla | Mata in den med |
| --- | --- |
| Lokal dator |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Verktyget AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Verktyget AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Strömmade data

Detta representerar data som kan genereras av olika källor, till exempel program, enheter, sensorer osv. Dessa data kan matas in i Data Lake Storage Gen2 av flera olika verktyg. Dessa verktyg samlar vanligt vis in och bearbetar data baserat på händelse i real tid och skriver sedan händelserna i batchar till Data Lake Storage Gen2 så att de kan bearbetas ytterligare.

Här är en lista med verktyg som du kan använda för att mata in strömmade data.

|Verktyg | Vägledning |
|---|--|
|Azure Stream Analytics|[Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Utgående till Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Skriv till Apache Hadoop HDFS från Apache Storm på HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Relationsdata

Du kan också källdata från Relations databaser. Under en viss tids period samlar Relations databaser in enorma data mängder som kan tillhandahålla viktiga insikter om de bearbetas via en stor datapipeline. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen2.

Här är en lista med verktyg som du kan använda för att mata in relations data.

|Verktyg | Vägledning |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Logg data för webb server (Ladda upp med anpassade program)

Den här typen av data uppsättning kallas särskilt för att analysera webb serverns loggdata är ett vanligt användnings fall för stora data program och kräver att stora mängder loggfiler laddas upp till Data Lake Storage Gen2. Du kan använda något av följande verktyg för att skriva egna skript eller program för att överföra sådana data.

Här är en lista med verktyg som du kan använda för att mata in data från webb server loggar.

|Verktyg | Vägledning |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

För att överföra data från webb server loggar och även för överföring av andra typer av data (t. ex. sociala sentiment-data), är det en lämplig metod för att skriva egna anpassade skript/program eftersom det ger dig flexibiliteten att inkludera data överförings komponenten som en del av ditt större stora data program. I vissa fall kan den här koden ha formen av ett skript eller ett enkelt kommando rads verktyg. I andra fall kan koden användas för att integrera stor data bearbetning i ett affärs program eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster

De flesta typer av HDInsight-kluster (Hadoop, HBase, Storm) stöder Data Lake Storage Gen2 som lagrings plats för data lagring. HDInsight-kluster får åtkomst till data från Azure Storage blobbar (WASB). För bättre prestanda kan du kopiera data från WASB till ett Data Lake Storage Gen2-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

Här är en lista med verktyg som du kan använda för att mata in data som är associerade med HDInsight-kluster.

|Verktyg | Vägledning |
|---|--|
|Apache DistCp | [Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Verktyget AzCopy | [Överföra data med AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala eller IaaS Hadoop-kluster

Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer som använder HDFS. Hadoop-klustren kan finnas i en lokal distribution eller vara i ett IaaS-kluster på Azure. Det kan finnas krav på att kopiera sådana data till Azure Data Lake Storage Gen2 för ett engångs sätt eller på ett återkommande sätt. Det finns olika alternativ som du kan använda för att åstadkomma detta. Nedan visas en lista med alternativ och tillhör ande kompromisser.

| Metod | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen2 |[ADF stöder HDFS som en data Källa](../../data-factory/connector-hdfs.md) |ADF tillhandahåller direkt support för HDFS och den första klassens slut punkt till slut punkt-hantering och övervakning |Kräver att Data Management Gateway distribueras lokalt eller i IaaS-klustret |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen2 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen2 med: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Verktyget AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache-DistCp som körs på HDInsight-kluster](data-lake-storage-use-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Multi-Step-process som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Faktiskt stora data uppsättningar

För att ladda upp data uppsättningar som sträcker sig över flera terabyte, kan det ibland vara långsamt och kostsamt att använda de metoder som beskrivs ovan. I sådana fall kan du använda Azure-ExpressRoute.  

Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azures Data Center och infrastruktur på din plats. Detta ger ett tillförlitligt alternativ för överföring av stora mängder data. Läs mer i [Azure ExpressRoute-dokumentationen](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Bearbeta data

När data är tillgängliga i Data Lake Storage Gen2 kan du köra analyser av dessa data med hjälp av Big data-program som stöds. 

![Analysera data i Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analysera data i Data Lake Storage Gen2")

Här är en lista med verktyg som du kan använda för att köra data analys jobb på data som lagras i Data Lake Storage Gen2.

|Verktyg | Vägledning |
|---|--|
|Azure HDInsight | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[Snabb start: analysera data i Azure Data Lake Storage Gen2 med Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Självstudie: Extrahera, transformera och läsa in data med hjälp av Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisera datan

Använd Power BI-anslutningen för att skapa visuella representationer av data som lagras i Data Lake Storage Gen2. Se [analysera data i Azure Data Lake Storage Gen2 med Power BI](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Ladda ned data

Du kanske också vill hämta eller flytta data från Azure Data Lake Storage Gen2 för scenarier som:

* Flytta data till andra databaser till gränssnitt med dina befintliga pipeliner för data bearbetning. Du kanske till exempel vill flytta data från Data Lake Storage Gen2 till Azure SQL Database eller en SQL Server instans.

* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Utgående data från Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Utgående data från Data Lake Storage Gen2")

Här är en lista med verktyg som du kan använda för att hämta data från Data Lake Storage Gen2.

|Verktyg | Vägledning |
|---|--|
|Azure Data Factory | [Kopieringsaktivitet i Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Använd DistCp för att kopiera data mellan Azure Storage blobbar och Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Azure Storage Explorer|[Använda Azure Storage Explorer till att hantera kataloger, filer och åtkomstkontrollistor i Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Verktyget AzCopy|[Överföra data med AzCopy och Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data)|
