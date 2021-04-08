---
title: Avbilda strömmande händelser – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över insamlings funktionen som gör att du kan avbilda händelser som strömmas via Azure Event Hubs.
ms.topic: article
ms.date: 02/16/2021
ms.openlocfilehash: 9f0ec1223c06b908a9aa9f3ac5c5b19ead2fe962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595957"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Avbilda händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage
Med Azure Event Hubs kan du automatiskt samla in strömmande data i Event Hubs i [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Storage gen 1-eller gen 2](https://azure.microsoft.com/services/data-lake-store/) -konto, med den extra flexibiliteten att ange en tid eller ett storleks intervall. Att konfigurera avbildningen är snabbt, det finns inga administrativa kostnader att köra den och skalar automatiskt med Event Hubs [data flödes enheter](event-hubs-scalability.md#throughput-units). Event Hubs Capture är det enklaste sättet att läsa in strömmande data i Azure och du kan fokusera på data bearbetning i stället för på data fångst.

> [!NOTE]
> Att konfigurera Event Hubs avbildning som ska användas Azure Data Lake Storage **gen 2** är detsamma som att konfigurera den för att använda en Azure-Blob Storage. Mer information finns i [konfigurera Event Hubs avbildning](event-hubs-capture-enable-through-portal.md). 

Med Event Hubs Capture kan du bearbeta real tids-och batch-baserade pipeliner på samma ström. Det innebär att du kan bygga lösningar som växer med dina behov över tid. Oavsett om du bygger på batch-baserade system idag med ett öga mot framtida bearbetning i real tid, eller om du vill lägga till en effektiv kall väg till en befintlig real tids lösning, så kan Event Hubs samla ihop med strömmande data.

> [!IMPORTANT]
> Mål lagrings kontot (Azure Storage eller Azure Data Lake Storage) måste finnas i samma prenumeration som händelsehubben. 

## <a name="how-event-hubs-capture-works"></a>Så här fungerar Event Hubs Capture

Event Hubs är en varaktig buffert med tidskvarhållning för telemetri, ungefär som en distribuerad logg. Den nyckel som ska skalas i Event Hubs är den [partitionerade konsument modellen](event-hubs-scalability.md#partitions). Varje partition är ett oberoende data segment och förbrukas oberoende av varandra. Med tiden kan dessa data stängas av, baserat på den konfigurerbara kvarhållningsperioden. Det innebär att en specifik händelsehubben aldrig får "helt".

Med Event Hubs Capture kan du ange ditt eget Azure Blob Storage-konto och-behållare, eller Azure Data Lake Storage konto som används för att lagra insamlade data. De här kontona kan vara i samma region som händelsehubben eller i en annan region, vilket ökar flexibiliteten i funktionen för att samla in Event Hubs.

Insamlade data skrivs i [Apache Avro][Apache Avro] -format: ett kompakt, fast binärformat som ger omfattande data strukturer med infogat schema. Det här formatet används ofta i Hadoop-eko systemet, Stream Analytics och Azure Data Factory. Mer information om hur du arbetar med Avro finns längre fram i den här artikeln.

### <a name="capture-windowing"></a>Avbilda fönster

Med Event Hubs Capture kan du konfigurera ett fönster för att styra avbildningen. Det här fönstret är en minimi storlek och tids konfiguration med en "första WINS-princip", vilket innebär att den första utlösaren påträffade en avbildnings åtgärd. Om du har en uppsamlings period på 15 minuter, 100 MB och skickar 1 MB per sekund, utlöses fönstret storlek före tids perioden. Varje partition fångar oberoende och skriver en slutförd Block-Blob vid tidpunkten för avbildningen, med namnet för den tid då registrerings intervallet påträffades. Namngivnings konventionen för lagring är följande:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Datum värdena fylls med nollor. ett exempel på ett fil namn kan vara:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Om din Azure Storage-BLOB är tillfälligt otillgänglig, kommer Event Hubs fånga att behålla dina data för den data lagrings period som kon figurer ATS på händelsehubben och fylla i data på nytt när ditt lagrings konto är tillgängligt igen.

### <a name="scaling-to-throughput-units"></a>Skala till data flödes enheter

Event Hubs trafik styrs av [data flödes enheter](event-hubs-scalability.md#throughput-units). En enda data flödes enhet tillåter 1 MB per sekund eller 1000 händelser per sekund i ingress och dubbelt så mycket utgående. Standard Event Hubs kan konfigureras med 1-20-dataflödes enheter och du kan köpa mer med en kvot som ökar [support förfrågan][support request]. Användningen utöver dina köpta data flödes enheter är begränsad. Event Hubs samla in data direkt från intern Event Hubs lagrings utrymme, kringgår utgående kvoter för data flödet och spara ditt utgångs värde för andra bearbetnings läsare, till exempel Stream Analytics eller Spark.

När du har konfigurerat Event Hubs Capture körs automatiskt när du skickar din första händelse och fortsätter att köra. För att göra det enklare för din underordnade bearbetning att veta att processen fungerar, Event Hubs skriver tomma filer när det inte finns några data. Den här processen ger ett förutsägbart takt och en markör som kan användas för att mata in batch-processorer.

## <a name="setting-up-event-hubs-capture"></a>Konfigurera Event Hubs avbildning

Du kan konfigurera avbildningen vid skapande tiden för händelsehubben med hjälp av [Azure Portal](https://portal.azure.com)eller med hjälp av Azure Resource Manager mallar. Mer information finns i följande artiklar:

- [Aktivera avbildningsfunktionen i Event Hubs med Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

> [!NOTE]
> Om du aktiverar insamlings funktionen för en befintlig händelsehubben, fångar funktionen händelser som kommer till händelsehubben **när** funktionen är aktive rad. Den fångar inte in händelser som fanns i händelsehubben innan funktionen var aktive rad. 

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Utforska de fångade filerna och arbeta med Avro

Event Hubs avbildningen skapar filer i Avro-format, enligt vad som anges i fönstret konfigurerad tid. Du kan visa dessa filer i alla verktyg som [Azure Storage Explorer][Azure Storage Explorer]. Du kan ladda ned filerna lokalt för att arbeta med dem.

Filerna som skapas av Event Hubs-avbildningen har följande Avro-schema:

![Avro-schema][3]

Ett enkelt sätt att utforska Avro-filer är genom att använda [Avro-verktygen][Avro Tools] jar från Apache. Du kan också använda [Apache-granskning][Apache Drill] för en låg SQL-driven upplevelse eller [Apache Spark][Apache Spark] för att utföra komplex distribuerad bearbetning på inmatade data. 

### <a name="use-apache-drill"></a>Använda Apache-granskning

[Apache-granskning][Apache Drill] är en SQL-frågemotor med öppen källkod för stor data utforskning som kan fråga strukturerade och delvis strukturerade data var de än är. Motorn kan köras som en fristående nod eller som ett stort kluster för fantastisk prestanda.

Ett inbyggt stöd för Azure Blob Storage är tillgängligt, vilket gör det enkelt att fråga efter data i en Avro-fil, enligt beskrivningen i dokumentationen:

[Apache-granskning: Azure Blob Storage-plugin-programmet][Apache Drill: Azure Blob Storage Plugin]

För att enkelt fråga insamlade filer kan du skapa och köra en virtuell dator med Apache-granskning aktiverat via en behållare för att få åtkomst till Azure Blob Storage. Se följande exempel: [strömning i skala med Event Hubs Capture](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-capture).

### <a name="use-apache-spark"></a>Använd Apache Spark

[Apache Spark][Apache Spark] är en "enhetlig analys motor för storskalig data bearbetning". Den har stöd för olika språk, inklusive SQL, och kan enkelt komma åt Azure Blob Storage. Det finns några alternativ att köra Apache Spark i Azure och var och en ger enkel åtkomst till Azure Blob Storage:

- [HDInsight: adressera filer i Azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Använda Avro-verktyg

[Avro-verktyg][Avro Tools] är tillgängliga som jar-paket. När du har hämtat jar-filen kan du se schemat för en speciell avro-fil genom att köra följande kommando:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Det här kommandot returnerar

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Du kan också använda Avro-verktyg för att konvertera filen till JSON-format och utföra annan bearbetning.

Du kan utföra mer avancerad bearbetning genom att ladda ned och installera Avro för ditt val av plattform. När detta skrivs finns det tillgängliga implementeringar för C, C++, C \# , Java, NodeJS, perl, php, python och Ruby.

Apache Avro har slutfört Komma igång guider för [Java][Java] och [python][Python]. Du kan också läsa artikeln [komma igång med Event Hubs Capture](event-hubs-capture-python.md) .

## <a name="how-event-hubs-capture-is-charged"></a>Hur Event Hubs avbildningen debiteras

Event Hubs Capture mäts på samma sätt som data flödes enheter: per timme. Avgiften är direkt proportionell till antalet data flödes enheter som har köpts för namn området. När data flödes enheter höjs och minskar, ökar och minskar Event Hubs Infångnings mätare för att ge matchande prestanda. Mätarna inträffar i tandem. Pris information finns i [Event Hubs prissättning](https://azure.microsoft.com/pricing/details/event-hubs/). 

Capture förbrukar inte utgående kvot eftersom den faktureras separat. 

## <a name="integration-with-event-grid"></a>Integrering med Event Grid 

Du kan skapa en Azure Event Grid-prenumeration med ett Event Hubs-namnområde som källa. Följande själv studie kurs visar hur du skapar en Event Grid-prenumeration med en Event Hub som en källa och en Azure Functions app som mottagare: [bearbeta och migrera insamlade Event Hubs data till en Azure Synapse-analys med event Grid och Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Nästa steg
Event Hubs Capture är det enklaste sättet att hämta data till Azure. Med hjälp av Azure Data Lake, Azure Data Factory och Azure HDInsight kan du utföra batchbearbetning och annan analys med välbekanta verktyg och plattformar som du väljer, i vilken skala du behöver.

Lär dig hur du aktiverar den här funktionen med hjälp av Azure Portal och Azure Resource Manager mall:

- [Använda Azure Portal för att aktivera Event Hubs Capture](event-hubs-capture-enable-through-portal.md)
- [Använd en Azure Resource Manager-mall för att aktivera Event Hubs avbildning](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming at Scale: Event Hubs Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
