---
title: Schema-och data typs mappning i kopierings aktiviteten
description: Lär dig mer om hur kopierings aktivitet i Azure Data Factory mappar scheman och data typer från källdata till Sink-data.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 0aee6030e5608b5413864d6a32dc8442dd346f42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392790"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Schema-och data typs mappning i kopierings aktiviteten
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur Azure Data Factory kopierings aktivitet utför schema mappning och data typs mappning från källdata till Sink-data.

## <a name="schema-mapping"></a>Schemamappning

### <a name="default-mapping"></a>Standard mappning

Som standard mappar kopierings aktiviteten käll data till Sink **efter kolumn namn** i skift läges känsliga sätt. Om Sink inte finns, till exempel genom att skriva till filer, sparas käll fält namnen som mottagar namn. Sådan standard mappning stöder flexibla scheman och schema avvikelser från källa till Sink från körning till körning – alla data som returneras av käll data lagret kan kopieras till Sink.

Om källan är en textfil utan rubrik rad krävs [explicit mappning](#explicit-mapping) eftersom källan inte innehåller kolumn namn.

### <a name="explicit-mapping"></a>Explicit mappning

Du kan också ange explicit mappning för att anpassa kolumn-och fält mappningen från källa till mottagare utifrån dina behov. Med explicit mappning kan du bara kopiera delar av data till Sink eller mappa källdata till Sink med olika namn eller ändra formens tabell/hierarkiska data. Kopierings aktivitet:

1. Läser data från källan och bestämmer käll schemat.
2. Använder din definierade mappning.
3. Skriver data till Sink.

Läs mer om:

- [Tabell källa till tabell mottagare](#tabular-source-to-tabular-sink)
- [Hierarkisk källa till tabell mottagare](#hierarchical-source-to-tabular-sink)
- [Tabell/hierarkisk källa till hierarkiskt handfat](#tabularhierarchical-source-to-hierarchical-sink)

Du kan konfigurera mappningen på Data Factory redigerings gränssnitt – > kopiera aktivitet-> Mapping-fliken, eller ange mappningen i aktiviteten Kopiera aktivitet-> `translator` . Följande egenskaper stöds i `translator`  ->  `mappings` array-> Objects-> `source` och `sink` , som pekar på den angivna kolumnen/fältet för att mappa data.

| Egenskap | Beskrivning                                                  | Obligatorisk |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Namn på kolumn/fält för källa eller mottagare. Använd för tabell källa och mottagare. | Ja      |
| numret  | Kolumn index. Starta från 1. <br>Använd och krävs när du använder avgränsad text utan rubrik rad. | Inga       |
| path     | Uttryck för JSON-sökvägar för varje fält som ska extraheras eller mappas. Ansök om hierarkisk källa och mottagare, till exempel Cosmos DB, MongoDB eller REST-anslutningar.<br>För fält under rotobjektet börjar JSON-sökvägen med roten `$` , för fält inuti matrisen som valts av `collectionReference` egenskap, JSON-sökväg börjar från mat ris elementet utan `$` . | Inga       |
| typ     | Data Factory data typen datatyp för kolumnen källa eller mottagare. I allmänhet behöver du inte ange eller ändra den här egenskapen. Läs mer om [data typs mappning](#data-type-mapping). | Inga       |
| substrat  | Kultur för kolumnen källa eller mottagare. Använd när typen är `Datetime` eller `Datetimeoffset` . Standardvärdet är `en-us`.<br>I allmänhet behöver du inte ange eller ändra den här egenskapen. Läs mer om [data typs mappning](#data-type-mapping). | Inga       |
| format   | Format strängen som ska användas när typen är `Datetime` eller `Datetimeoffset` . Referera till [anpassade datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings) för hur du formaterar DateTime. I allmänhet behöver du inte ange eller ändra den här egenskapen. Läs mer om [data typs mappning](#data-type-mapping). | Inga       |

Följande egenskaper stöds under `translator` förutom `mappings` :

| Egenskap            | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Använd när du kopierar data från hierarkisk källa, till exempel Cosmos DB, MongoDB eller REST-kopplingar.<br>Om du vill iterera och extrahera data från objekten **inuti ett mat ris fält** med samma mönster och konvertera till per rad per objekt, anger du JSON-sökvägen för den matrisen för att göra kors koppling. | Inga       |

#### <a name="tabular-source-to-tabular-sink"></a>Tabell källa till tabell mottagare

Om du till exempel vill kopiera data från Salesforce till Azure SQL Database och explicit mappa tre kolumner:

1. På fliken Kopiera aktivitet – > mappning klickar du på knappen **Importera scheman** för att importera både käll-och mottagar scheman.

2. Mappa nödvändiga fält och Uteslut/ta bort resten.

![Mappa tabell till tabell](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Samma mappning kan konfigureras enligt följande i aktiviteten för kopierings aktivitet (se `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Om du vill kopiera data från avgränsade textfiler utan rubrik rad representeras kolumnerna av ordnings tal i stället för namn. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Hierarkisk källa till tabell mottagare

När du kopierar data från hierarkisk källa till tabell mottagare stöder kopierings aktiviteten följande funktioner:

- Extrahera data från objekt och matriser.
- Kors Använd flera objekt med samma mönster från en matris, i vilket fall att konvertera ett JSON-objekt till flera poster i tabell resultatet.

För mer avancerad transformering av hierarkisk till tabell kan du använda [data flödet](concepts-data-flow-overview.md). 

Om du till exempel har ett käll MongoDB-dokument med följande innehåll:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

Och du vill kopiera den till en textfil i följande format med rubrik rad genom att förenkla data i matrisen *(order_pd och order_price)* och korsa kopplingen med den gemensamma rot informationen *(nummer, datum och ort)*:

| orderNumber | Datum | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Du kan definiera en sådan mappning på Data Factory redigerings gränssnitt:

1. På fliken Kopiera aktivitet – > mappning klickar du på knappen **Importera scheman** för att importera både käll-och mottagar scheman. Som Data Factory exempel på de mest populära objekten när du importerar schemat, om något fält inte visas, kan du lägga till det i rätt lager i hierarkin-hovring på ett befintligt fält namn och välja att lägga till en nod, ett objekt eller en matris.

2. Välj den matris som du vill iterera och extrahera data från. Den fylls i automatiskt som **samlings referens**. Observera att endast en enskild matris stöds för den här åtgärden.

3. Mappa de fält som behövs till Sink. Data Factory identifierar automatiskt motsvarande JSON-sökvägar för den hierarkiska sidan.

> [!NOTE]
> För poster där matrisen som marker ATS som samlings referens är tom och kryss rutan är markerad, hoppas hela posten över.

![Mappa hierarkiskt till tabell med hjälp av användar gränssnittet](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Du kan också växla till **avancerad redigerare**, i så fall kan du direkt se och redigera fälten JSON-sökvägar. Om du väljer att lägga till en ny mappning i den här vyn anger du JSON-sökvägen.

![Mappa hierarkiskt till tabell med avancerad redigerare](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Samma mappning kan konfigureras enligt följande i aktiviteten för kopierings aktivitet (se `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Tabell/hierarkisk källa till hierarkiskt handfat

Användar upplevelsens flöde liknar [hierarkisk källa till tabell mottagare](#hierarchical-source-to-tabular-sink). 

När du kopierar data från tabell källa till hierarkiskt handfat stöds inte skrivning till matris inuti objekt.

När du kopierar data från hierarkisk källa till hierarkisk mottagare kan du även bevara hela lagrets hierarki genom att välja objektet/matrisen och mappa till Sink utan att röra de inre fälten.

För mer avancerade omvandlingar av dataformat kan du använda [data flöden](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Parameterisera-mappning

Om du vill skapa en mallbaserade-pipeline för att kopiera ett stort antal objekt dynamiskt, avgör du om du kan använda [standard mappningen](#default-mapping) eller så måste du definiera [explicit mappning](#explicit-mapping) för respektive objekt.

Om explicit mappning krävs kan du:

1. Definiera en parameter med objekt typ på pipeline-nivån, till exempel `mapping` .

2. Parameterisera mappningen: på fliken Kopiera aktivitet – > mappning väljer du för att lägga till dynamiskt innehåll och väljer parametern ovan. Aktivitetens nytto Last skulle vara följande:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Konstruera värdet för att skicka till mappnings parametern. Det ska vara hela `translator` definitionen av definitionen, se avsnittet exempel i [explicit mappning](#explicit-mapping) . För tabell källa till till exempel kopia av tabell mottagare ska värdet vara `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Datatypmappning

Kopierings aktiviteten utför käll typer för att avbilda typ mappningar med följande flöde: 

1. Konvertera från ursprungliga ursprungs data typer till Azure Data Factory interimistiska data typer.
2. Konvertera automatiskt interimistisk datatyp efter behov för att matcha motsvarande mottagar typer, tillämpligt för både [standard mappningen](#default-mapping) och [explicit mappning](#explicit-mapping).
3. Konvertera från Azure Data Factory interimistiska data typer för att sinka interna data typer.

Kopierings aktiviteten stöder för närvarande följande Interimistiska data typer: Boolean, byte, byte matris, DateTime, DatetimeOffset, decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, TimeSpan, UInt16, UInt32 och UInt64.

Följande data typs konverteringar stöds mellan de tillfälliga typerna från källan till Sink.

| Source\Sink | Boolesk | Byte mat ris | Decimal | Datum/tid <small>(1)</small> | Flytt ALS läge <small>(2)</small> | GUID | Heltal <small>(3)</small> | Sträng | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boolesk     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Byte mat ris  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Datum/tid   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Flytt ALS | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Sträng      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) datum/tid inkluderar DateTime och DateTimeOffset.

(2) float-Point innehåller enkel och dubbel.

(3) heltal inkluderar SByte, byte, Int16, UInt16, Int32, UInt32, Int64 och UInt64.

> [!NOTE]
> - För närvarande stöds denna konvertering av data typen vid kopiering mellan tabell data. Hierarkiska källor/mottagare stöds inte, vilket innebär att det inte finns någon systemdefinierad data typs omvandling mellan typerna källa och handfat.
> - Den här funktionen fungerar med den senaste data uppsättnings modellen. Om du inte ser det här alternativet från användar gränssnittet kan du försöka skapa en ny data uppsättning.

Följande egenskaper stöds i kopierings aktivitet för konvertering av data typer (under `translator` avsnitt för redigering av program mässigt):

| Egenskap                         | Beskrivning                                                  | Krävs |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Aktivera den nya konverterings upplevelsen för data typer. <br>Standardvärdet är false på grund av bakåtkompatibilitet.<br><br>För nya kopierings aktiviteter som skapats via Data Factory redigerings gränssnittet sedan den senaste juni 2020, aktive ras den här data typ konverteringen som standard för bästa möjliga upplevelse och du kan se följande typ konverterings inställningar på fliken Kopiera aktivitet – > mappning för tillämpliga scenarier. <br>Om du vill skapa pipeline program mässigt måste du uttryckligen ange `typeConversion` Property till true för att aktivera den.<br>För befintliga kopierings aktiviteter som skapats innan den här funktionen släpps visas inte typ konverterings alternativ i Data Factory redigerings gränssnitt för bakåtkompatibilitet. | Inga       |
| typeConversionSettings           | En grupp av typ konverterings inställningar. Använd när `typeConversion` är inställt på `true` . Följande egenskaper är alla under den här gruppen. | Inga       |
| *Ramen `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Tillåt datatrunkering när källdata konverteras till Sink med en annan typ under kopiering, till exempel från decimal till heltal, från DatetimeOffset till DateTime. <br>Standardvärdet är True. | Inga       |
| treatBooleanAsNumber             | Behandla booleska värden som tal, till exempel sant som 1.<br>Standardvärdet är false. | Inga       |
| dateTimeFormat                   | Format sträng vid konvertering mellan datum utan tids zons förskjutning och strängar, till exempel `yyyy-MM-dd HH:mm:ss.fff` .  Se [anpassade datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings) för detaljerad information. | Inga       |
| dateTimeOffsetFormat             | Format sträng vid konvertering mellan datum med tids zons förskjutning och strängar, till exempel `yyyy-MM-dd HH:mm:ss.fff zzz` .  Se [anpassade datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings) för detaljerad information. | Inga       |
| timeSpanFormat                   | Format sträng vid konvertering mellan tids perioder och strängar, till exempel `dd\.hh\:mm` . Se [anpassade format strängar för TimeSpan](/dotnet/standard/base-types/custom-timespan-format-strings) för detaljerad information. | Inga       |
| substrat                          | Kultur information som ska användas vid konvertering av typer, till exempel `en-us` eller `fr-fr` . | Inga       |

**Exempel:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Äldre modeller

> [!NOTE]
> Följande modeller för att mappa käll kolumner/fält till Sink stöds fortfarande som är för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen som anges i [schema mappning](#schema-mapping). Data Factory redigerings gränssnittet har växlat till att generera den nya modellen.

### <a name="alternative-column-mapping-legacy-model"></a>Alternativ kolumn – mappning (äldre modell)

Du kan ange kopierings aktivitet – > `translator`  ->  `columnMappings` för att mappa mellan tabellbaserade data. I det här fallet krävs avsnittet "struktur" för både data uppsättningar för indata och utdata. Kolumn mappning stöder **Mappning av alla eller delmängd av kolumner i käll data uppsättningens struktur till alla kolumner i data uppsättningen för Sink-datauppsättningen**. Följande är fel villkor som resulterar i ett undantag:

- Frågeresultatet för käll data lagret har inget kolumn namn som är angivet i avsnittet struktur för indata-DataSet.
- Sink-datalagret (om med fördefinierat schema) inte har något kolumn namn som anges i "struktur"-avsnittet för utdata-datauppsättningen.
- Antingen färre kolumner eller fler kolumner i "struktur" för mottagar data uppsättningen än vad som anges i mappningen.
- Duplicera mappning.

I följande exempel har indata-datauppsättningen en struktur och den pekar på en tabell i en lokal Oracle-databas.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

I det här exemplet har data uppsättningen för utdata en struktur och den pekar på en tabell i Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Följande JSON definierar en kopierings aktivitet i en pipeline. Kolumnerna från källa som har mappats till kolumner i Sink med hjälp av egenskapen **Translator**  ->  **columnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Om du använder syntaxen för `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` för att ange kolumn mappning, stöds den fortfarande.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternativ schema mappning (äldre modell)

Du kan ange kopierings aktivitets-> `translator`  ->  `schemaMapping` för att mappa mellan hierarkiskt data och tabellbaserade data, till exempel kopiera från MongoDB/rest till textfil och kopiera från Oracle till Azure Cosmos DB s API för MongoDB. Följande egenskaper stöds i avsnittet Kopiera aktivitet `translator` :

| Egenskap            | Beskrivning                                                  | Krävs |
| :------------------ | :----------------------------------------------------------- | :------- |
| typ                | Typ egenskapen för kopierings aktivitets översättaren måste anges till: **TabularTranslator** | Ja      |
| schemaMapping       | En samling nyckel/värde-par som representerar mappnings relationen **från käll sidan till mottagar sidan**.<br/>- **Nyckel:** representerar källa. För **tabell källa** anger du kolumn namnet som definierats i data uppsättnings strukturen. för **hierarkisk källa** anger du JSON-sökvägar för varje fält som ska extraheras och mappas.<br>- **Värde:** representerar mottagare. För **tabell mottagare** anger du kolumn namnet enligt definitionen i data uppsättnings strukturen. för **hierarkisk mottagare** anger du JSON-sökvägar för varje fält som ska extraheras och mappas. <br>Om det finns hierarkiska data för fält under rot objekt börjar JSON-sökvägen med roten $; för fält i matrisen som valts av `collectionReference` egenskap börjar JSON-sökvägen från mat ris elementet. | Ja      |
| collectionReference | Om du vill iterera och extrahera data från objekten **inuti ett mat ris fält** med samma mönster och konvertera till per rad per objekt, anger du JSON-sökvägen för den matrisen för att göra kors koppling. Den här egenskapen stöds endast när hierarkiska data är källa. | Inga       |

**Exempel: kopiera från MongoDB till Oracle:**

Om du till exempel har MongoDB-dokument med följande innehåll:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att förenkla data i matrisen *(order_pd och order_price)* och korsa anslutning med den gemensamma rot informationen *(nummer, datum och ort)*:

| orderNumber | Datum | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Konfigurera schema mappnings regeln som följande JSON-exempel för kopierings aktiviteter:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
