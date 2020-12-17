---
title: Azure Stream Analytics partitionering av anpassad BLOB-utdata
description: I den här artikeln beskrivs anpassade patterns Path-mönster och anpassade fält-eller attribut-funktioner för Blob Storage-utdata från Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: 7239c2e3cb42cb17b01904e8fc226ae2408dbb47
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617433"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics partitionering av anpassad BLOB-utdata

Azure Stream Analytics stöder Anpassad partitionering av BLOB-utdata med anpassade fält eller attribut och anpassade mönster för DateTime-sökvägar. 

## <a name="custom-field-or-attributes"></a>Anpassat fält eller attribut

Anpassade fält eller indatavärden förbättrar underordnade data bearbetnings-och rapporterings arbets flöden genom att tillåta mer kontroll över utdata.

### <a name="partition-key-options"></a>Partitionsalternativ

Den partitionsnyckel eller det kolumn namn som används för att partitionera indata kan innehålla alla specialtecken som accepteras för [BLOB-namn](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata). Det går inte att använda kapslade fält som partitionsnyckel om de inte används tillsammans med alias, men du kan använda vissa tecken för att skapa en hierarki av filer. Du kan till exempel använda följande fråga för att skapa en kolumn som kombinerar data från två andra kolumner för att skapa en unik partitionsnyckel.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

Partitionsnyckel måste vara NVARCHAR (MAX), BIGINT, FLOAT eller BIT (1,2 kompatibilitetsnivå eller högre). Typerna DateTime, array och Records stöds inte, men kan användas som diskpartitioner om de konverteras till strängar. Mer information finns i [Azure Stream Analytics data typer](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Exempel

Anta att ett jobb tar indata från Live User-sessioner som är anslutna till en extern video spel tjänst där inmatade data innehåller en kolumn **client_id** för att identifiera sessionerna. Om du vill partitionera data med **client_id** anger du i fältet blobb Sök vägs mönster att innehåller en partitions-token **{client_id}** i egenskaper för BLOB-utdata när du skapar ett jobb. Som data med olika **client_id** värden flödar genom Stream Analytics jobbet, sparas utdata i separata mappar baserat på ett enda **client_id** värde per mapp.

![Sök vägs mönster med klient-ID](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

På samma sätt, om jobb inmatningen var sensor data från miljon tals sensorer där varje sensor hade en **sensor_id**, skulle Sök vägs mönstret vara **{sensor_id}** för att partitionera varje sensor data till olika mappar.  


Med hjälp av REST API kan utmatnings avsnittet i en JSON-fil som används för begäran se ut så här:  

![REST API utdata](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

När jobbet börjar köras kan *klienternas* behållare se ut så här:  

![Behållare för klienter](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Varje mapp kan innehålla flera blobbar där varje BLOB innehåller en eller flera poster. I exemplet ovan finns det en enda BLOB i en mapp med namnet "06000000" med följande innehåll:

![BLOB-innehåll](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observera att varje post i blobben har en **client_id** kolumn som matchar mappnamnet eftersom kolumnen som användes för att partitionera utdata i sökvägen för utdata var **client_id**.

### <a name="limitations"></a>Begränsningar

1. Det går bara att ha en anpassad partitionsnyckel i sökvägen till BLOB-utdata för Sök vägs mönster. Alla följande Sök vägs mönster är giltiga:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{Time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Partitionsnyckel är Skift läges okänsliga, så att partitionstyper som "John" och "John" är likvärdiga. Det går inte heller att använda uttryck som partitionsnyckel. Till exempel fungerar inte **{Columna + columnB}** .  

3. När en indataströmmen består av poster med en partitionsnyckel i 8000, läggs posterna till i befintliga blobbar och skapar bara nya blobbar vid behov. Om kardinalitet är över 8000, finns det ingen garanti för att befintliga blobar ska skrivas till och nya blobbar skapas inte för ett godtyckligt antal poster med samma partitionsnyckel.

4. Om BLOB-utdata har [kon figurer ATS som oföränderliga](../storage/blobs/storage-blob-immutable-storage.md)skapar Stream Analytics en ny BLOB varje gång data skickas.

## <a name="custom-datetime-path-patterns"></a>Anpassade mönster för DateTime-sökväg

Med anpassade mönster för DateTime-sökväg kan du ange ett utdataformat som är anpassat till Hive-konventioner för direkt uppspelning, vilket ger Azure Stream Analytics möjligheten att skicka data till Azure HDInsight och Azure Databricks för bearbetning under bearbetning. Anpassade mönster för datum/tid-sökväg implementeras enkelt med hjälp av `datetime` nyckelordet i fältet Path (prefix) i BLOB-utdata, tillsammans med format specificeraren. Ett exempel är `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Token som stöds

Följande format-token kan användas separat eller i kombination för att uppnå anpassade DateTime-format:

|Format specificerare   |Beskrivning   |Resultat i exempel tid 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime: åååå}|Året som ett fyrsiffrigt tal|2018|
|{datetime: MM}|Månad från 01 till 12|01|
|{datetime: M}|Månad från 1 till 12|1|
|{datetime: DD}|Dag från 01 till 31|02|
|{datetime: d}|Dag från 1 till 31|2|
|{datetime: HH}|Timme med 24-timmarsformat, från 00 till 23|10|
|{datetime: mm}|Minuter från 00 till 60|06|
|{datetime: m}|Minuter från 0 till 60|6|
|{datetime: SS}|Sekunder från 00 till 60|08|

Om du inte vill använda anpassade DateTime-mönster kan du lägga till {date}-och/eller {Time}-token i Path-prefixet för att generera en listruta med inbyggda DateTime-format.

![Stream Analytics gamla DateTime-format](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Utökning och begränsningar

Du kan använda så många tokens, `{datetime:<specifier>}` som du vill i Sök vägs mönstret tills du når tecken gränsen för Path-prefixet. Det går inte att kombinera format specificerare inom en token utöver de kombinationer som redan visas i list rutorna datum och tid. 

För en Path-partition av `logs/MM/dd` :

|Giltigt uttryck   |Ogiltigt uttryck   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Du kan använda samma format specifikation flera gånger i Path-prefixet. Token måste upprepas varje tillfälle.

### <a name="hive-streaming-conventions"></a>Konventioner för direkt uppspelning av Hive

Anpassade Sök vägs mönster för Blob Storage kan användas med registrerings konventionen för Hive, som förväntar sig att mappar etiketteras med `column=` i mappnamnet.

Ett exempel är `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Anpassade utdata eliminerar besväret med att ändra tabeller och manuellt lägga till partitioner i Port data mellan Azure Stream Analytics och Hive. I stället kan många mappar läggas till automatiskt med:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exempel

Skapa ett lagrings konto, en resurs grupp, ett Stream Analytics jobb och en indatakälla enligt snabb starts guiden för [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) . Använd samma exempel data som används i snabb starts guiden, som också finns på [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Skapa en BLOB-utgående mottagare med följande konfiguration:

![Stream Analytics skapa mottagare av BLOB-utdata](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Det fullständiga Sök vägs mönstret ser ut så här:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


När du startar jobbet skapas en mappstruktur som baseras på Sök vägs mönstret i BLOB-behållaren. Du kan öka detalj nivån till dags nivån.

![Stream Analytics BLOB-utdata med anpassade Sök vägs mönster](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
