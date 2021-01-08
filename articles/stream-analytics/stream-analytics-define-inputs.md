---
title: Strömma data som indata till Azure Stream Analytics
description: Lär dig hur du konfigurerar en data anslutning i Azure Stream Analytics. Indata är en data ström från händelser och även referens data.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 5f10fed66475cda8fd700a4737727101e2465870
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019371"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Strömma data som indata till Stream Analytics

Stream Analytics har integration i första klass med Azure data strömmar som indata från tre typer av resurser:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

De här ingångs resurserna kan leva i samma Azure-prenumeration som din Stream Analytics jobb eller en annan prenumeration.

### <a name="compression"></a>Komprimering

Stream Analytics stöder komprimering i alla data Ströms inmatnings källor. Komprimerings typer som stöds är: ingen, GZip och DEFLATE-komprimering. Det finns inte stöd för komprimering för referens data. Om indata-formatet är Avro data som är komprimerade hanteras de transparent. Du behöver inte ange komprimerings typ med Avro-serialisering. 

## <a name="create-edit-or-test-inputs"></a>Skapa, redigera eller testa indata

Du kan använda [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)och [Visual Studio Code](quick-create-visual-studio-code.md) för att lägga till och Visa eller redigera befintliga indata i streaming-jobbet. Du kan också testa inmatnings anslutningar och testa frågor från exempel data från Azure Portal, [Visual Studio](stream-analytics-vs-tools-local-run.md)och [Visual Studio Code](visual-studio-code-local-run.md). När du skriver en fråga listar du InInformationen i from-satsen. Du kan hämta listan med tillgängliga indata från sidan **fråga** i portalen. Om du vill använda flera indata kan du `JOIN` eller skriva flera `SELECT` frågor.


## <a name="stream-data-from-event-hubs"></a>Strömma data med Event Hubs

Azure Event Hubs tillhandahåller mycket skalbara evenemang som publicerar prenumerationer. En Event Hub kan samla in miljon tals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Tillsammans är Event Hubs och Stream Analytics tillhandahålla en heltäckande lösning för real tids analys. Med Event Hubs kan du mata in händelser i Azure i real tid och Stream Analytics jobb kan bearbeta dessa händelser i real tid. Du kan till exempel skicka webb klick, sensor avläsningar eller logg händelser online till Event Hubs. Du kan sedan skapa Stream Analytics jobb för att använda Event Hubs som indata strömmar för real tids filtrering, agg regering och korrelation.

`EventEnqueuedUtcTime` är tidsstämpeln för en händelses ankomst i en Event Hub och är standard tidsstämpeln för händelser som kommer från Event Hubs till Stream Analytics. Om du vill bearbeta data som en data ström med en tidstämpel i händelse nytto lasten måste du använda [tids stämplingen med](/stream-analytics-query/timestamp-by-azure-stream-analytics) nyckelord.

### <a name="event-hubs-consumer-groups"></a>Event Hubs konsument grupper

Du bör konfigurera varje Stream Analytics Event Hub-inmatare så att de har sin egen konsument grupp. När ett jobb innehåller en själv koppling eller har flera indata kan vissa indata läsas av fler än en läsare. Den här situationen påverkar antalet läsare i en enda konsument grupp. För att undvika att överskrida Event Hubs gränsen på fem läsare per konsument grupp per partition, är det en bra idé att utse en konsument grupp för varje Stream Analytics jobb. Det finns också en gräns på 20 konsument grupper för en Event Hub på standard nivå. Mer information finns i [felsöka Azure Stream Analytics indata](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Skapa inmatade Event Hubs

I följande tabell förklaras varje egenskap på den **nya inmatnings** sidan i Azure Portal att strömma data från en händelsehubben:

| Egenskap | Beskrivning |
| --- | --- |
| **Inmatat alias** |Ett eget namn som du använder i jobbets fråga för att referera till den här indatamängden. |
| **Prenumeration** | Välj den prenumeration där Event Hub-resursen finns. | 
| **Namnområde för händelsehubb** | Event Hub-namnområdet är en behållare för en uppsättning meddelande enheter. När du skapar en ny händelsehubben skapar du även namn området. |
| **Namn på händelsehubb** | Namnet på händelsehubben som ska användas som indatamängd. |
| **Principnamn för Event Hub** | Principen för delad åtkomst som ger åtkomst till Händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Det här alternativet fylls i automatiskt, om du inte väljer alternativet att ange inställningar för Händelsehubben manuellt.|
| **Konsument grupp för Event Hub** (rekommenderas) | Vi rekommenderar starkt att du använder en distinkt konsument grupp för varje Stream Analytics jobb. Den här strängen identifierar den konsument grupp som ska användas för att mata in data från händelsehubben. Om ingen konsument grupp har angetts använder Stream Analytics jobbet $Default konsument gruppen.  |
| **Partitionsnyckel** | Detta är ett valfritt fält som bara är tillgängligt om jobbet är konfigurerat för att använda [kompatibilitetsnivån](./stream-analytics-compatibility-level.md) 1,2 eller högre. Om din inaktuella information har partitionerats av en egenskap kan du lägga till namnet på den här egenskapen här. Detta används för att förbättra prestandan för din fråga om den innehåller en PARTITION BY-eller GROUP BY-sats i den här egenskapen. Om det här jobbet använder kompatibilitetsnivå 1,2 eller högre är det här fältet standardvärdet "PartitionId". |
| **Händelseserialiseringsformat** | Serialization-formatet (JSON, CSV, Avro eller [Other (protobuf, XML, tillverkarspecifika...)](custom-deserializer.md)) för den inkommande data strömmen.  Se till att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimal tal. |
| **Kodning** | UTF-8 är för närvarande det enda kodnings format som stöds. |
| **Händelse komprimerings typ** | Komprimerings typen som används för att läsa inkommande data ström, till exempel ingen (standard), GZip eller DEFLATE. |

När dina data kommer från en Event Hub Stream-indata har du åtkomst till följande metadata-fält i din Stream Analytics fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **EventEnqueuedUtcTime** |Datum och tid då händelsen togs emot av Event Hubs. |
| **Partition** |Det nollbaserade partitions-ID: t för det angivna nätverkskortet. |

Med hjälp av dessa fält kan du till exempel skriva en fråga som följande exempel:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> När du använder Event Hub som en slut punkt för IoT Hub vägar, kan du komma åt IoT Hub metadata med [funktionen GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Strömma data från IoT Hub

Azure IoT Hub är en mycket skalbar händelse för att publicera prenumerationer som är optimerade för IoT-scenarier.

Standard tidsstämpeln för händelser som kommer från en IoT Hub i Stream Analytics är tidsstämpeln som händelsen anlänt i IoT Hub, vilket är `EventEnqueuedUtcTime` . Om du vill bearbeta data som en data ström med en tidstämpel i händelse nytto lasten måste du använda [tids stämplingen med](/stream-analytics-query/timestamp-by-azure-stream-analytics) nyckelord.

### <a name="iot-hub-consumer-groups"></a>Konsument grupper för IoT Hub

Du bör konfigurera varje Stream Analytics IoT Hub inmatat för att ha en egen konsument grupp. När ett jobb innehåller en själv koppling eller när det har flera indata kan vissa indata läsas av fler än en läsare. Den här situationen påverkar antalet läsare i en enda konsument grupp. För att undvika att överskrida Azure IoT Hub-gränsen på fem läsare per konsument grupp per partition, är det en bra idé att utse en konsument grupp för varje Stream Analytics jobb.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurera en IoT Hub som data Ströms inmatning

I följande tabell förklaras varje egenskap på den **nya indata** -sidan i Azure Portal när du konfigurerar en IoT Hub som en data ström.

| Egenskap | Beskrivning |
| --- | --- |
| **Inmatat alias** | Ett eget namn som du använder i jobbets fråga för att referera till den här indatamängden.|
| **Prenumeration** | Välj den prenumeration där IoT Hub resursen finns. | 
| **IoT Hub** | Namnet på IoT Hub som ska användas som indatatyp. |
| **Slutpunkt** | Slut punkten för IoT Hub.|
| **Namn på princip för delad åtkomst** | Principen för delad åtkomst som ger åtkomst till IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
| **Nyckel för delad åtkomst princip** | Den delade åtkomst nyckeln som används för att ge åtkomst till IoT Hub.  Det här alternativet fylls i automatiskt om du inte väljer alternativet att tillhandahålla IoT Hub-inställningarna manuellt. |
| **Konsument grupp** | Vi rekommenderar starkt att du använder en annan konsument grupp för varje Stream Analytics jobb. Konsument gruppen används för att mata in data från IoT Hub. Stream Analytics använder $Default konsument gruppen om du inte anger något annat.  |
| **Partitionsnyckel** | Detta är ett valfritt fält som bara är tillgängligt om jobbet är konfigurerat för att använda [kompatibilitetsnivån](./stream-analytics-compatibility-level.md) 1,2 eller högre. Om din inaktuella information har partitionerats av en egenskap kan du lägga till namnet på den här egenskapen här. Detta används för att förbättra prestandan för din fråga om den innehåller en PARTITION BY-eller GROUP BY-sats i den här egenskapen. Om det här jobbet använder kompatibilitetsnivå 1,2 eller högre är det här fältet standardvärdet "PartitionId". |
| **Händelseserialiseringsformat** | Serialization-formatet (JSON, CSV, Avro eller [Other (protobuf, XML, tillverkarspecifika...)](custom-deserializer.md)) för den inkommande data strömmen.  Se till att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimal tal. |
| **Kodning** | UTF-8 är för närvarande det enda kodnings format som stöds. |
| **Händelse komprimerings typ** | Komprimerings typen som används för att läsa inkommande data ström, till exempel ingen (standard), GZip eller DEFLATE. |


När du använder strömmande data från en IoT Hub har du åtkomst till följande metadata-fält i Stream Analyticss fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **EventProcessedUtcTime** | Datum och tid då händelsen behandlades. |
| **EventEnqueuedUtcTime** | Datum och tid då händelsen togs emot av IoT Hub. |
| **Partition** | Det nollbaserade partitions-ID: t för det angivna nätverkskortet. |
| **IoTHub. MessageId** | Ett ID som används för att korrelera tvåvägs kommunikation i IoT Hub. |
| **IoTHub. CorrelationId** | Ett ID som används i meddelande svar och feedback i IoT Hub. |
| **IoTHub. ConnectionDeviceId** | Det autentiserings-ID som används för att skicka meddelandet. Det här värdet stämplas på servicebound-meddelanden av IoT Hub. |
| **IoTHub. ConnectionDeviceGenerationId** | Generations-ID för den autentiserade enheten som användes för att skicka meddelandet. Det här värdet stämplas på servicebound-meddelanden av IoT Hub. |
| **IoTHub. EnqueuedTime** | Den tidpunkt då meddelandet togs emot av IoT Hub. |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>Strömma data från Blob Storage eller Data Lake Storage Gen2
För scenarier med stora mängder ostrukturerade data som ska lagras i molnet erbjuder Azure Blob Storage eller Azure Data Lake Storage Gen2 (ADLS Gen2) en kostnads effektiv och skalbar lösning. Data i Blob Storage eller ADLS Gen2 betraktas vanligt vis som vilande data. dessa data kan dock bearbetas som en data ström genom Stream Analytics. 

Logg bearbetning är ett scenario som ofta används för att använda sådana indata med Stream Analytics. I det här scenariot har telemetri-datafiler fångats från ett system och måste parsas och bearbetas för att extrahera meningsfulla data.

Standard-tidsstämpeln för ett Blob Storage-eller ADLS Gen2-händelse i Stream Analytics är tidsstämpeln som den senast ändrades, vilket är `BlobLastModifiedUtcTime` . Om en BLOB laddas upp till ett lagrings konto på 13:00 och Azure Stream Analytics jobbet startas med alternativet *nu* vid 13:01, hämtas det inte när dess ändrade tid faller utanför jobb körnings perioden.

Om en BLOB laddas upp till en lagrings konto behållare på 13:00 och Azure Stream Analytics jobbet startas med en *anpassad tid* på 13:00 eller tidigare, hämtas blobben när den ändrade tiden infaller inom jobb körnings perioden.

Om ett Azure Stream Analytics jobb har startats med *nu* vid 13:00 och en BLOB överförs till lagrings konto behållaren vid 13:01, kommer Azure Stream Analytics att hämta bloben. Tidsstämpeln som tilldelas varje BLOB baseras bara på `BlobLastModifiedTime` . Mappen som blobben är i har ingen relation till den tidsstämpel som tilldelats. Om det till exempel finns en BLOB *2019/10-01/00/b1.txt* med en `BlobLastModifiedTime` 2019-11-11, är tidsstämpeln som är kopplad till denna BLOB 2019-11-11.

Om du vill bearbeta data som en data ström med en tidstämpel i händelse nytto lasten måste du använda [tids stämplingen med](/stream-analytics-query/stream-analytics-query-language-reference) nyckelord. Ett Stream Analytics-jobb hämtar data från Azure Blob Storage eller ADLS Gen2 indata varje sekund om BLOB-filen är tillgänglig. Om BLOB-filen inte är tillgänglig finns det en exponentiell backoff med en maximal tids fördröjning på 90 sekunder.

CSV-formaterade indata kräver en rubrik rad för att definiera fält för data uppsättningen och alla rubrik rads fält måste vara unika.

> [!NOTE]
> Stream Analytics har inte stöd för att lägga till innehåll i en befintlig BLOB-fil. Stream Analytics visar varje fil bara en gång, och eventuella ändringar som inträffar i filen efter att jobbet har läst data bearbetas inte. Bästa praxis är att ladda upp alla data för en BLOB-fil samtidigt och sedan lägga till ytterligare nyare händelser till en annan, ny BLOB-fil.

I scenarier där många blobbar läggs till kontinuerligt och Stream Analytics bearbetar Blobbarna när de läggs till, är det möjligt att vissa blobbar hoppas över i sällsynta fall på grund av dess granularitet `BlobLastModifiedTime` . Du kan minska detta genom att ladda upp blobar minst två sekunder från varandra. Om det här alternativet inte är möjligt kan du använda Event Hubs för att strömma stora mängder händelser.

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurera Blob Storage som data ström 

I följande tabell beskrivs varje egenskap på den **nya indata** -sidan i Azure Portal när du konfigurerar Blob Storage som en data ström.

| Egenskap | Beskrivning |
| --- | --- |
| **Inmatat alias** | Ett eget namn som du använder i jobbets fråga för att referera till den här indatamängden. |
| **Prenumeration** | Välj den prenumeration där lagrings resursen finns. | 
| **Lagringskonto** | Namnet på det lagrings konto där BLOB-filerna finns. |
| **Lagrings konto nyckel** | Den hemliga nyckeln som är kopplad till lagrings kontot. Det här alternativet fylls i automatiskt om du inte väljer alternativet för att ange inställningarna manuellt. |
| **Container** | Behållare tillhandahåller en logisk gruppering för blobbar. Du kan välja antingen **Använd befintlig** behållare eller  **Skapa ny** för att skapa en ny behållare.|
| **Sök vägs mönster** (valfritt) | Den fil Sök väg som används för att hitta Blobbarna i den angivna behållaren. Om du vill läsa blobbar från behållarens rot ska du inte ange ett Sök vägs mönster. I sökvägen kan du ange en eller flera instanser av följande tre variabler: `{date}` , `{time}` eller `{partition}`<br/><br/>Exempel 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exempel 2: `cluster1/logs/{date}`<br/><br/>`*`Specialtecknet är inte ett tillåtet värde för Path-prefixet. Endast giltiga <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Azure Blob-tecken</a> tillåts. Lägg inte till behållar namn eller fil namn. |
| **Datum format** (valfritt) | Om du använder date-variabeln i sökvägen är datum formatet där filerna är ordnade. Exempel: `YYYY/MM/DD` <br/><br/> När BLOB-indatatypen har `{date}` eller `{time}` i sin sökväg tittar mapparna i stigande tids ordning.|
| **Tids format** (valfritt) |  Om du använder tids variabeln i sökvägen är det tids formatet som filerna är ordnade i. För närvarande är det enda värde som stöds `HH` för timmar. |
| **Partitionsnyckel** | Detta är ett valfritt fält som bara är tillgängligt om jobbet är konfigurerat för att använda [kompatibilitetsnivån](./stream-analytics-compatibility-level.md) 1,2 eller högre. Om din inaktuella information har partitionerats av en egenskap kan du lägga till namnet på den här egenskapen här. Detta används för att förbättra prestandan för din fråga om den innehåller en PARTITION BY-eller GROUP BY-sats i den här egenskapen. Om det här jobbet använder kompatibilitetsnivå 1,2 eller högre är det här fältet standardvärdet "PartitionId". |
| **Antal inpartitioner** | Det här fältet finns bara när {partition} finns i Sök vägs mönstret. Värdet för den här egenskapen är ett heltal >= 1. Var {partition} visas i pathPattern, ett tal mellan 0 och värdet för det här fältet-1 kommer att användas. |
| **Händelseserialiseringsformat** | Serialization-formatet (JSON, CSV, Avro eller [Other (protobuf, XML, tillverkarspecifika...)](custom-deserializer.md)) för den inkommande data strömmen.  Se till att JSON-formatet överensstämmer med specifikationen och inte innehåller inledande 0 för decimal tal. |
| **Kodning** | För CSV och JSON är UTF-8 för närvarande det enda kodnings format som stöds. |
| **Komprimering** | Komprimerings typen som används för att läsa inkommande data ström, till exempel ingen (standard), GZip eller DEFLATE. |

När dina data kommer från en Blob Storage-källa har du åtkomst till följande metadata-fält i din Stream Analytics fråga:

| Egenskap | Beskrivning |
| --- | --- |
| **BlobName** |Namnet på bloben för inflöde som händelsen kom från. |
| **EventProcessedUtcTime** |Datum och tid då händelsen bearbetades av Stream Analytics. |
| **BlobLastModifiedUtcTime** |Datum och tid då blobben senast ändrades. |
| **Partition** |Det nollbaserade partitions-ID: t för det angivna nätverkskortet. |

Med hjälp av dessa fält kan du till exempel skriva en fråga som följande exempel:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/