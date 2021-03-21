---
title: Fil format som stöds i Azure Data Factory (bakåtkompatibelt)
description: I det här avsnittet beskrivs de fil format och komprimerings koder som stöds av filbaserade kopplingar i Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d95927a9ea7d3084387a9aedb0dcdd86f84b8e7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384834"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Fil format och komprimerings-codecar som stöds i Azure Data Factory (bakåtkompatibelt)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Den här artikeln gäller följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory introducerade ny formaterad data uppsättnings modell, se motsvarande format artikel med information: <br>- [Avro-format](format-avro.md)<br>- [Binärt format](format-binary.md)<br>- [Avgränsat text format](format-delimited-text.md)<br>- [JSON-format](format-json.md)<br>- [ORC-format](format-orc.md)<br>- [Parquet-format](format-parquet.md)<br>De rest-konfigurationer som nämns i den här artikeln stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt. 

## <a name="text-format-legacy"></a><a name="text-format"></a> Text format (bakåtkompatibelt)

>[!NOTE]
>Lär dig mer om den nya modellen från artikeln [om avgränsat text format](format-delimited-text.md) . Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill läsa från en textfil eller skriva till en textfil anger du `type` egenskapen i `format` avsnittet i data uppsättningen till text format. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [TextFormat-exempel](#textformat-example).

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| columnDelimiter |Det tecken som används för att avgränsa kolumner i en fil. Du kan överväga att använda ett sällsynt icke utskrivbart teckensnitt som kanske inte finns i dina data. Ange till exempel "\u0001" som representerar början av rubrik (SOH). |Endast ett tecken är tillåtet. **Standardvärdet** är **kommatecken (,)**. <br/><br/>Om du vill använda ett Unicode-tecken läser du [Unicode-tecken](https://en.wikipedia.org/wiki/List_of_Unicode_characters) för att hämta motsvarande kod för den. |Inga |
| rowDelimiter |Det tecken som används för att avgränsa rader i en fil. |Endast ett tecken är tillåtet. **Standardvärdet** är något av följande värden vid läsning: **["\r\n", "\r", "\n"]** och **"\r\n"** vid skrivning. |Inga |
| escapeChar |Det specialtecken som används för att undanta en kolumnavgränsare i innehållet i indatafilen. <br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Exempel: om du har kommatecken (,) som kolumn avgränsare, men vill ha ett kommatecken i texten (till exempel: "Hello, World"), kan du definiera "$" som escape-tecken och använda strängen "Hej $, World" i källan. |Inga |
| quoteChar |Det tecken som används för att referera till ett strängvärde. Kolumn- och radavgränsarna innanför citattecknen behandlas som en del av strängvärdet. Den här egenskapen gäller både in- och utdatauppsättningar.<br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Om du till exempel använder kommatecken (,) som kolumnavgränsare, men vill använda ett kommatecken i texten (till exempel <Hello, world>), kan du definiera " (dubbla citattecken) som citattecknet och använda strängen "Hello, world" i källan. |Inga |
| nullValue |Ett eller flera tecken som används för att representera ett null-värde. |Ett eller flera tecken. **Standardvärdena** är **"\N" och "NULL"** vid läsning och **"\N"** vid skrivning. |Inga |
| encodingName |Ange kodningsnamnet. |Ett giltigt kodningsnamn. Se [Egenskapen Encoding.EncodingName](/dotnet/api/system.text.encoding). Exempel: windows-1250 or shift_jis. **Standardvärdet** är **UTF-8**. |Inga |
| firstRowAsHeader |Anger om den första raden ska behandlas som en rubrik. För en indatauppsättning läser Data Factory den första raden som en rubrik. För en utdatauppsättning skriver Data Factory den första raden som en rubrik. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Sant<br/><b>False (standard)</b> |Inga |
| skipLineCount |Anger antalet **icke-tomma** rader som ska hoppas över vid läsning av data från indatafiler. Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Inga |
| treatEmptyAsNull |Anger om du vill hantera null-strängar eller tomma strängar som ett null-värde vid läsning av data från en indatafil. |**True (standard)**<br/>Falskt |Inga |

### <a name="textformat-example"></a>TextFormat-exempel

I följande JSON-definition för en data uppsättning anges några av de valfria egenskaperna.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Om du vill använda ett `escapeChar` i stället för `quoteChar` ersätter du raden med `quoteChar` med följande escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Användningsscenarier för firstRowAsHeader och skipLineCount

* Du kopierar från en källa som inte är filbaserad till en textfil och vill lägga till en rubrikrad som innehåller schemametadata (till exempel: SQL-schema). Ange `firstRowAsHeader` som true i utdatauppsättningen för det här scenariot.
* Du kopierar från en textfil som innehåller en rubrikrad till en mottagare som inte är filbaserad och vill ignorera raden. Ange `firstRowAsHeader` som true i indatauppsättningen.
* Du kopierar från en textfil och vill hoppa över några rader i början som antingen inte innehåller några data eller som innehåller rubrikinformation. Ange `skipLineCount` för att ange antalet rader som ska hoppas över. Om resten av filen innehåller en rubrikrad kan du också ange `firstRowAsHeader`. Om både `skipLineCount` och `firstRowAsHeader` anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen

## <a name="json-format-legacy"></a><a name="json-format"></a> JSON-format (bakåtkompatibelt)

>[!NOTE]
>Läs artikeln ny modell från [JSON-format](format-json.md) . Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill **Importera/exportera en JSON-fil som är i/från Azure Cosmos DB**, se avsnittet Importera/exportera JSON-dokument i [Flytta data till/från Azure Cosmos DB](connector-azure-cosmos-db.md) artikeln.

Om du vill parsa JSON-filerna eller skriva data i JSON-format anger du `type` egenskapen i `format` avsnittet till **JsonFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example).

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| filePattern |Ange mönstret för de data som lagras i varje JSON-fil. Tillåtna värden är: **setOfObjects** och **arrayOfObjects**. **Standardvärdet** är **setOfObjects**. Detaljerad information om dessa mönster finns i avsnittet om [JSON-filmönster](#json-file-patterns). |Inga |
| jsonNodeReference | Om du vill iterera och extrahera data från objekten i ett matrisfält med samma mönster anger du JSON-sökvägen för matrisen. Den här egenskapen stöds endast när du kopierar data **från** JSON-filer. | Inga |
| jsonPathDefinition | Ange JSON-sökvägsuttrycket för varje kolumnmappning med ett anpassat kolumnnamn (inled med liten bokstav). Den här egenskapen stöds endast när du kopierar data **från** JSON-filer och du kan extrahera data från objekt eller matris. <br/><br/> För fält under rotobjektet börjar du med $; för fält inuti matrisen som väljs av egenskapen `jsonNodeReference` börjar du från matriselementet. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example). | Inga |
| encodingName |Ange kodningsnamnet. En lista över giltiga kodningsnamn finns i avsnittet om egenskapen [Encoding.EncodingName](/dotnet/api/system.text.encoding). Exempel: windows-1250 or shift_jis. **Standardvärdet** är: **UTF-8**. |Inga |
| nestingSeparator |Tecken som används för att avgränsa kapslingsnivåer. Standardvärdet är ”.” (punkt). |Inga |

>[!NOTE]
>När det gäller kors användnings data i matrisen i flera rader (fall 1 – > exempel 2 i [JsonFormat-exempel](#jsonformat-example)) kan du bara välja att expandera en enskild matris med egenskap `jsonNodeReference` .

### <a name="json-file-patterns"></a>JSON-filmönster

Kopierings aktiviteten kan parsa följande mönster för JSON-filer:

- **Typ I: setOfObjects**

    Varje fil som innehåller ett enskilt objekt eller flera radavgränsade/sammanfogade objekt. När det här alternativet väljs i en utdatauppsättning genererar kopieringsaktiviteten en enskild JSON-fil med ett objekt per rad (radavgränsade).

    * **Exempel på JSON med enskilda objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exempel med radavgränsad JSON**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exempel med sammanfogad JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Varje fil innehåller en matris med objekt.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat-exempel

**Fall 1: Kopiera data från JSON-filer**

**Exempel 1: hämta data från objektet och matrisen**

I det här exemplet mappas ett JSON-rotobjekt till en enskild post i tabellformat. Om du har en JSON-fil med följande innehåll:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att extrahera data från både objekten och matrisen:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [Mappa käll data uppsättnings kolumner till kolumner med mål data uppsättningar](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. Om du vill kopiera data från matrisen kan du använda `array[x].property` för att extrahera värdet för den aktuella egenskapen från `xth` objektet, eller så kan du använda `array[*].property` för att hitta värdet från alla objekt som innehåller egenskapen.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Exempel 2: korstillämpa flera objekt med samma mönster från en matris**

I det här exemplet omvandlas ett JSON-rotobjekt till flera poster i tabellform. Om du har en JSON-fil med följande innehåll:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
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
    "city": [ { "sanmateo": "No 1" } ]
}
```

som du vill kopiera till en Azure SQL-tabell i följande format genom att förenkla data i matrisen och korskoppla med den gemensamma rotinformationen:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [Mappa käll data uppsättnings kolumner till kolumner med mål data uppsättningar](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` indikerar att iterera och extrahera data från objekt med samma mönster under **matrisen** `orderlines` .
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. I det här exemplet `ordernumber` `orderdate` är, och `city` under rot objekt med JSON-sökväg som börjar med `$.` , medan `order_pd` och `order_price` definieras med en sökväg som härletts från mat ris elementet utan `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Observera följande punkter:**

* Om `structure` och `jsonPathDefinition` inte har definierats i Data Factory-datauppsättningen identifierar kopieringsaktiviteten schemat från det första objektet och förenklar hela objektet.
* Om JSON-indata har en matris konverterar kopieringsaktiviteten som standard hela matrisvärdet till en sträng. Du kan välja att extrahera data från den med hjälp av `jsonNodeReference` och/eller `jsonPathDefinition`, eller hoppa över det genom att inte ange den i `jsonPathDefinition`.
* Om det finns dubblettnamn på samma nivå väljer kopieringsaktiviteten det sista.
* Egenskapsnamn är skiftlägeskänsliga. Två egenskaper med samma namn men med olika skiftlägen behandlas som två olika egenskaper.

**Fall 2: Skriva data till JSON-fil**

Om du har följande tabell i SQL Database:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

och för varje post förväntar du dig att skriva till ett JSON-objekt i följande format:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Utdatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt `structure` definierar avsnittet anpassade egenskaps namn i målfilen, `nestingSeparator` (Standardvärdet är ".") används för att identifiera kapslings lagret från namnet. Det här avsnittet är **valfritt** såvida du inte vill ändra egenskapsnamnet som jämförs med källkolumnnamnet, eller kapsla vissa av egenskaperna.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a> Parquet-format (bakåtkompatibelt)

>[!NOTE]
>Lär dig den nya modellen från artikeln [Parquet format](format-parquet.md) . Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa Parquet-filer eller skriva data i Parquet-format anger du egenskapen `format` `type` till **ParquetFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Observera följande punkter:

* Komplexa data typer stöds inte (MAP, LIST).
* Tomt utrymme i kolumn namnet stöds inte.
* Parquet-filer har följande komprimeringsrelaterade alternativ: NONE, SNAPPY, GZIP och LZO. Data Factory har stöd för att läsa data från Parquet-filen i någon av dessa komprimerade format förutom LZO – den använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en Parquet-fil väljer Data Factory SNAPPY, som är standard för Parquet-formatet. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> Om du inte kopierar Parquet-filer i **befintligt skick som är som är** integration runtime behöriga för den lokala datorn och data lager i molnet, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller OPENJDK** på din IR-dator. Se följande stycke med mer information.

För kopiering som körs på egen värd-IR med Parquet-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabeln *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och Ställ in system miljö variabeln JAVA_HOME därför.

>[!TIP]
>Om du kopierar data till/från Parquet-format med hjälp av självbetjäning Integration Runtime och träffa fel som säger "ett fel uppstod vid anrop till Java, meddelande: **Java. lang. OutOfMemoryError: Java-heap-utrymme**", kan du lägga till en miljö variabel `_JAVA_OPTIONS` på den dator som är värd för den egna IR-enheten för att justera den minsta/högsta heap-storleken för JVM för att få en

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabeln `_JAVA_OPTIONS` med värde `-Xms256m -Xmx16g` . Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda maximalt `Xmx` mängd minne. Som standard använder ADF den minsta 64 MB och Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Data typs mappning för Parquet-filer

| Data fabrikens interimistiska datatyp | Parquet primitiv typ | Ursprunglig typ av Parquet (deserialisering) | Ursprunglig typ av Parquet (serialisera) |
|:--- |:--- |:--- |:--- |
| Boolesk | Boolesk | Saknas | Saknas |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binär | UInt64 | Decimal |
| Enkel | Float | Saknas | Saknas |
| Double | Double | Saknas | Saknas |
| Decimal | Binär | Decimal | Decimal |
| Sträng | Binär | Utf8 | Utf8 |
| DateTime | Int96 | Saknas | Saknas |
| TimeSpan | Int96 | Saknas | Saknas |
| DateTimeOffset | Int96 | Saknas | Saknas |
| ByteArray | Binär | Saknas | Saknas |
| GUID | Binär | Utf8 | Utf8 |
| Char | Binär | Utf8 | Utf8 |
| CharArray | Stöds inte | Saknas | Saknas |

## <a name="orc-format-legacy"></a><a name="orc-format"></a> ORC-format (bakåtkompatibelt)

>[!NOTE]
>Lär dig den nya modellen från artikeln [Orc format](format-orc.md) . Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa ORC-filerna eller skriva data i ORC-format ange du egenskapen `format` `type` till **OrcFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "OrcFormat"
}
```

Observera följande punkter:

* Komplexa data typer stöds inte (STRUCT, MAP, LIST, UNION).
* Tomt utrymme i kolumn namnet stöds inte.
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> Om du inte kopierar ORC-filer i **befintligt skick som är som är** integration runtime behöriga för den lokala datorn och data lager i molnet, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller OPENJDK** på din IR-dator. Se följande stycke med mer information.

För kopiering som körs på egen värd-IR med ORC-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabeln *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och Ställ in system miljö variabeln JAVA_HOME därför.

### <a name="data-type-mapping-for-orc-files"></a>Data typs mappning för ORC-filer

| Data fabrikens interimistiska datatyp | ORC-typer |
|:--- |:--- |
| Boolesk | Boolesk |
| SByte | Byte |
| Byte | Blank |
| Int16 | Blank |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Lång |
| Int64 | Lång |
| UInt64 | Sträng |
| Enkel | Float |
| Double | Double |
| Decimal | Decimal |
| Sträng | Sträng |
| DateTime | Timestamp |
| DateTimeOffset | Timestamp |
| TimeSpan | Timestamp |
| ByteArray | Binär |
| GUID | Sträng |
| Char | Char (1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a> AVRO-format (bakåtkompatibelt)

>[!NOTE]
>Lär dig den nya modellen från artikeln [Avro format](format-avro.md) . Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa Avro-filerna eller skriva data i Avro-format anger du egenskapen `format` `type` till **AvroFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "AvroFormat",
}
```

Om du vill använda Avro-format i en Hive-tabell kan du läsa [Apache Hive själv studie kursen](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observera följande punkter:

* [Komplexa data typer](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, unioner och fasta).

## <a name="compression-support-legacy"></a><a name="compression-support"></a> Komprimerings stöd (bakåtkompatibelt)

Azure Data Factory stöder komprimering/expandering av data under kopiering. När du anger `compression` en egenskap i en indata-datauppsättning läser kopierings aktiviteten komprimerade data från källan och dekomprimerar den. När du anger egenskapen i en data uppsättning för utdata, komprimerar kopierings aktiviteten sedan data till mottagaren. Här följer några exempel scenarier:

* Läs GZIP-komprimerade data från en Azure-Blob, expandera den och skriv resultat data till Azure SQL Database. Du definierar indata-Azure Blob-datauppsättningen med `compression` `type` egenskapen gzip.
* Läs data från en oformaterad textfil från det lokala fil systemet, komprimera den med GZip-format och skriv komprimerade data till en Azure-blob. Du definierar en data uppsättning för Azure Blob-utdata med `compression` `type` egenskapen som gzip.
* Läs. zip-fil från FTP-server, expandera den för att hämta filerna i och landa filerna i Azure Data Lake Store. Du definierar en inkommande FTP-datauppsättning med `compression` `type` egenskapen som ZipDeflate.
* Läs en GZIP-komprimerad data från en Azure-Blob, komprimera den, komprimera den med BZIP2 och skriv resultat data till en Azure-blob. Du definierar indata-Azure Blob-datauppsättningen med `compression` `type` inställningen gzip och data uppsättningen för utdata med `compression` `type` värdet bzip2.

Om du vill ange komprimering för en data uppsättning använder du **komprimerings** egenskapen i data uppsättnings-JSON som i följande exempel:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Komprimerings** avsnittet har två egenskaper:

* **Typ:** komprimerings-codecen, som kan vara **gzip**, **DEFLATE**, **bzip2** eller **ZipDeflate**. Obs! när du använder kopierings aktivitet för att expandera ZipDeflate-filer och skriva till filbaserat mottagar data lager extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/` .
* **Nivå:** komprimerings förhållandet, som kan vara **optimalt** eller **snabbast**.

  * **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.
  * **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra.

    Mer information finns i avsnittet [komprimerings nivå](/dotnet/api/system.io.compression.compressionlevel) .

> [!NOTE]
> Komprimerings inställningar stöds inte för data i **AvroFormat**, **OrcFormat** eller **ParquetFormat**. När du läser filer i dessa format kan Data Factory identifiera och använda komprimerings-codecen i metadata. När du skriver till filer i dessa format, väljer Data Factory Standard-komprimerings-codec för formatet. Till exempel ZLIB för OrcFormat och fästning för ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Filtyper och komprimerings format som inte stöds

Du kan använda utöknings funktionerna i Azure Data Factory för att transformera filer som inte stöds.
Två alternativ är Azure Functions och anpassade aktiviteter med hjälp av Azure Batch.

Du kan se ett exempel som använder en Azure-funktion för att [extrahera innehållet i en tar-fil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Mer information finns i [Azure Functions-aktivitet](./control-flow-azure-function-activity.md).

Du kan också bygga den här funktionen med en anpassad dotNet-aktivitet. Ytterligare information finns [här](./transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Nästa steg

Lär dig de senaste fil formaten och komprimeringarna från [fil format och komprimering som stöds](supported-file-formats-and-compression-codecs.md).