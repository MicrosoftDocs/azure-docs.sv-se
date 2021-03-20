---
title: Data scenarier som involverar Data Lake Storage Gen1 | Microsoft Docs
description: Förstå de olika scenarier och verktyg som använder vilka data som kan matas in, bearbetas, hämtas och visualiseras i Data Lake Storage Gen1 (tidigare kallat Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702505"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Använda Azure Data Lake Storage Gen1 för Big data-krav

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Det finns fyra viktiga steg i stor data bearbetning:

* Mata in stora mängder data i ett data lager i real tid eller i batchar
* Bearbetar data
* Hämtar data
* Visualisera data

I den här artikeln tittar vi på de här stegen för Azure Data Lake Storage Gen1 för att förstå de alternativ och verktyg som är tillgängliga för att uppfylla dina Big data behov.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Mata in data i Data Lake Storage Gen1
I det här avsnittet beskrivs de olika data källorna och de olika sätt som data kan matas in i ett Data Lake Storage Gen1-konto.

![Mata in data i Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Mata in data i Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc-data
Detta representerar mindre data uppsättningar som används för prototypering av stor data program. Det finns olika sätt att mata in ad hoc-data beroende på data källan.

| Datakälla | Mata in den med |
| --- | --- |
| Lokal dator |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Använda Data Lake verktyg för Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy-verktyg](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp som körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Strömmade data
Detta representerar data som kan genereras av olika källor, till exempel program, enheter, sensorer osv. Dessa data kan matas in i Data Lake Storage Gen1 av flera olika verktyg. Dessa verktyg samlar vanligt vis in och bearbetar data baserat på händelse i real tid och skriver sedan händelserna i batchar till Data Lake Storage Gen1 så att de kan bearbetas ytterligare.

Följande är verktyg som du kan använda:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) -händelser som matas in i Event Hubs kan skrivas till Azure Data Lake Storage gen1 med hjälp av en Azure Data Lake Storage gen1 utdata.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – du kan skriva data direkt till data Lake Storage gen1 från Storm-klustret.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) – du kan ta emot händelser från Event Hubs och sedan skriva den till data Lake Storage gen1 med hjälp av [data Lake Storage gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationsdata
Du kan också källdata från Relations databaser. Under en viss tids period samlar Relations databaser in enorma data mängder som kan tillhandahålla viktiga insikter om de bearbetas via en stor datapipeline. Du kan använda följande verktyg för att flytta sådana data till Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Logg data för webb server (Ladda upp med anpassade program)
Den här typen av data uppsättning kallas särskilt för att analysera webb serverns loggdata är ett vanligt användnings fall för stora data program och kräver att stora mängder loggfiler laddas upp till Data Lake Storage Gen1. Du kan använda något av följande verktyg för att skriva egna skript eller program för att överföra sådana data.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

För att överföra data från webb server loggar och även för överföring av andra typer av data (t. ex. sociala sentiment-data), är det en lämplig metod för att skriva egna anpassade skript/program eftersom det ger dig flexibiliteten att inkludera data överförings komponenten som en del av ditt större stora data program. I vissa fall kan den här koden ha formen av ett skript eller ett enkelt kommando rads verktyg. I andra fall kan koden användas för att integrera stor data bearbetning i ett affärs program eller en lösning.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data som är associerade med Azure HDInsight-kluster
De flesta typer av HDInsight-kluster (Hadoop, HBase, Storm) stöder Data Lake Storage Gen1 som lagrings plats för data lagring. HDInsight-kluster får åtkomst till data från Azure Storage blobbar (WASB). För bättre prestanda kan du kopiera data från WASB till ett Data Lake Storage Gen1-konto som är kopplat till klustret. Du kan använda följande verktyg för att kopiera data.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-tjänst](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Data som lagras i lokala eller IaaS Hadoop-kluster
Stora mängder data kan lagras i befintliga Hadoop-kluster, lokalt på datorer som använder HDFS. Hadoop-klustren kan finnas i en lokal distribution eller vara i ett IaaS-kluster på Azure. Det kan finnas krav på att kopiera sådana data till Azure Data Lake Storage Gen1 för ett engångs sätt eller på ett återkommande sätt. Det finns olika alternativ som du kan använda för att åstadkomma detta. Nedan visas en lista med alternativ och tillhör ande kompromisser.

| Metod | Information | Fördelar | Överväganden |
| --- | --- | --- | --- |
| Använd Azure Data Factory (ADF) för att kopiera data direkt från Hadoop-kluster till Azure Data Lake Storage Gen1 |[ADF stöder HDFS som en data Källa](../data-factory/connector-hdfs.md) |ADF tillhandahåller direkt support för HDFS och den första klassens slut punkt till slut punkt-hantering och övervakning |Kräver att Data Management Gateway distribueras lokalt eller i IaaS-klustret |
| Exportera data från Hadoop som filer. Kopiera sedan filerna till Azure Data Lake Storage Gen1 med lämplig mekanism. |Du kan kopiera filer till Azure Data Lake Storage Gen1 med: <ul><li>[Azure PowerShell för Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Anpassad app med valfri Data Lake Storage Gen1-SDK</li></ul> |Kom igång snabbt. Kan göra anpassade uppladdningar |Multi-Step-process som omfattar flera tekniker. Hantering och övervakning kommer att växa för att bli en utmaning över tiden med hänsyn till verktygets anpassade karaktär |
| Använd Distcp för att kopiera data från Hadoop till Azure Storage. Kopiera sedan data från Azure Storage till Data Lake Storage Gen1 med lämplig mekanism. |Du kan kopiera data från Azure Storage till Data Lake Storage Gen1 med: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy-verktyg](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache-DistCp som körs på HDInsight-kluster](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Du kan använda verktyg med öppen källkod. |Multi-Step-process som omfattar flera tekniker |

### <a name="really-large-datasets"></a>Faktiskt stora data uppsättningar
För att ladda upp data uppsättningar som sträcker sig över flera terabyte, kan det ibland vara långsamt och kostsamt att använda de metoder som beskrivs ovan. I sådana fall kan du använda alternativen nedan.

* **Använda Azure-ExpressRoute**. Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azure-datacenter och infrastrukturen i dina lokaler. Detta ger ett tillförlitligt alternativ för överföring av stora mängder data. Mer information finns i [dokumentationen om Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **"Frånkopplad" överföring av data**. Om du inte är genomförbart att använda Azure-ExpressRoute kan du använda [Azure import/export-tjänsten](../import-export/storage-import-export-service.md) för att leverera hård diskar med dina data till ett Azure-datacenter. Dina data laddas först upp till Azure Storage blobbar. Du kan sedan använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) -eller [AdlCopy-verktyget](data-lake-store-copy-data-azure-storage-blob.md) för att kopiera data från Azure Storage blobbar till data Lake Storage gen1.

  > [!NOTE]
  > När du använder import/export-tjänsten bör fil storlekarna på diskarna som du levererar till Azure Data Center inte vara större än 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Bearbeta data som lagras i Data Lake Storage Gen1
När data är tillgängliga i Data Lake Storage Gen1 kan du köra analyser av dessa data med hjälp av Big data-program som stöds. För närvarande kan du använda Azure HDInsight och Azure Data Lake Analytics för att köra data analys jobb på data som lagras i Data Lake Storage Gen1.

![Analysera data i Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analysera data i Data Lake Storage Gen1")

Du kan titta på följande exempel.

* [Skapa ett HDInsight-kluster med Data Lake Storage Gen1 som lagring](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Hämta data från Data Lake Storage Gen1
Du kanske också vill hämta eller flytta data från Azure Data Lake Storage Gen1 för scenarier som:

* Flytta data till andra databaser till gränssnitt med dina befintliga pipeliner för data bearbetning. Du kanske till exempel vill flytta data från Data Lake Storage Gen1 till Azure SQL Database eller SQL Server.
* Ladda ned data till den lokala datorn för bearbetning i IDE-miljöer när du skapar program prototyper.

![Utgående data från Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Utgående data från Data Lake Storage Gen1")

I sådana fall kan du använda något av följande alternativ:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Du kan också använda följande metoder för att skriva ett eget skript/program för att hämta data från Data Lake Storage Gen1.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualisera data i Data Lake Storage Gen1
Du kan använda en blandning av tjänster för att skapa visuella representationer av data som lagras i Data Lake Storage Gen1.

![Visualisera data i Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisera data i Data Lake Storage Gen1")

* Du kan börja med [att använda Azure Data Factory för att flytta data från data Lake Storage gen1 till Azure Synapse Analytics](../data-factory/copy-activity-overview.md)
* Därefter kan du [integrera Power BI med Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) för att skapa visuell representation av data.