---
title: Flytta data från DB2 med hjälp av Azure Data Factory
description: Lär dig hur du flyttar data från en lokal DB2-databas med hjälp av Azure Data Factory kopierings aktivitet
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bcd1e6f9f9be1765e76399707ae36776a9f1987d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387367"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Flytta data från DB2 med Azure Data Factory kopierings aktivitet
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-db2-connector.md)
> * [Version 2 (aktuell version)](../connector-db2.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [DB2 Connector i v2](../connector-db2.md).


Den här artikeln beskriver hur du kan använda kopierings aktivitet i Azure Data Factory för att kopiera data från en lokal DB2-databas till ett data lager. Du kan kopiera data till alla butiker som visas som en mottagare som stöds i artikeln [Data Factory data förflyttnings aktiviteter](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Det här avsnittet bygger på Data Factory artikeln, som visar en översikt över data flyttningen med hjälp av kopierings aktiviteten och listar de kombinationer av data lager som stöds. 

Data Factory stöder för närvarande endast flytt av data från en DB2-databas till ett [mottagar data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Det finns inte stöd för att flytta data från andra data lager till en DB2-databas.

## <a name="prerequisites"></a>Förutsättningar
Data Factory stöder anslutning till en lokal DB2-databas med hjälp av [Data Management Gateway](data-factory-data-management-gateway.md). Stegvisa instruktioner för hur du konfigurerar Gateway-datapipeline för att flytta dina data finns i artikeln [Flytta data från en lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) .

En gateway krävs även om DB2 finns på den virtuella Azure IaaS-datorn. Du kan installera gatewayen på samma virtuella IaaS-dator som data lagret. Om gatewayen kan ansluta till databasen kan du installera gatewayen på en annan virtuell dator.

Data Management Gateway tillhandahåller en inbyggd DB2-drivrutin, så du behöver inte installera en driv rutin manuellt för att kopiera data från DB2.

> [!NOTE]
> Tips om fel sökning av anslutnings-och gateway-problem finns i artikeln [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="supported-versions"></a>Versioner som stöds
Data Factory DB2-anslutaren stöder följande IBM DB2-plattformar och versioner med Distributed Relations databas arkitektur (DRDA) SQL Access Manager version 9, 10 och 11:

* IBM DB2 för z/OS, version 11,1
* IBM DB2 för z/OS, version 10,1
* IBM DB2 för i (AS400) version 7,2
* IBM DB2 för i (AS400) version 7,1
* IBM DB2 för Linux, UNIX och Windows (LUW) version 11
* IBM DB2 för LUW version 10,5
* IBM DB2 för LUW version 10,1

> [!TIP]
> Om du får fel meddelandet "Det gick inte att hitta paketet som motsvarar en körnings förfrågan för SQL-instruktionen. SQLSTATE = 51002 SQLCODE =-805, "orsaken är att ett nödvändigt paket inte skapas för den normala användaren på operativ systemet. Lös problemet genom att följa de här instruktionerna för din DB2-Server Typ:
> - DB2 för i (AS400): Låt en privilegie rad användare skapa samlingen för den normala användaren innan kopierings aktiviteten körs. Använd kommandot för att skapa samlingen: `create collection <username>`
> - DB2 för z/OS eller LUW: Använd ett konto med hög behörighet – en privilegie rad användare eller administratör som har paket utfärdare och BIND, BINDADD, bevilja körning till offentliga behörigheter – för att köra kopian en gång. Det nödvändiga paketet skapas automatiskt under kopieringen. Efteråt kan du växla tillbaka till den normala användaren för din efterföljande kopierings körning.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet för att flytta data från ett lokalt DB2-data lager med hjälp av olika verktyg och API: er: 

- Det enklaste sättet att skapa en pipeline är att använda guiden Azure Data Factory kopiering. En snabb genom gång av hur du skapar en pipeline med hjälp av kopierings guiden finns i [självstudien: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md). 
- Du kan också använda verktyg för att skapa en pipeline, inklusive Visual Studio, Azure PowerShell, en Azure Resource Manager mall, .NET-API: et och REST API. Stegvisa instruktioner för att skapa en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa länkade tjänster för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data uppsättningar som representerar indata och utdata för kopierings åtgärden. 
3. Skapa en pipeline med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. 

När du använder guiden Kopiera skapas JSON-definitioner för Data Factory länkade tjänster, data uppsättningar och pipeline-entiteter automatiskt åt dig. När du använder verktyg eller API: er (förutom .NET-API: et) definierar du Data Factory entiteter med hjälp av JSON-formatet. JSON-exemplet: kopiera data från DB2 till Azure Blob Storage visar JSON-definitionerna för de Data Factory entiteter som används för att kopiera data från ett lokalt DB2-datalager.

Följande avsnitt innehåller information om de JSON-egenskaper som används för att definiera de Data Factory entiteter som är speciella för ett DB2-datalager.

## <a name="db2-linked-service-properties"></a>Egenskaper för länkad DB2-tjänst
I följande tabell visas de JSON-egenskaper som är speciella för en DB2-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **bastyp** |Den här egenskapen måste anges till **OnPremisesDb2**. |Ja |
| **servernamn** |Namnet på DB2-servern. |Ja |
| **databas** |Namnet på DB2-databasen. |Ja |
| **schema** |Namnet på schemat i DB2-databasen. Den här egenskapen är Skift läges känslig. |Inga |
| **authenticationType** |Den typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| **användar** |Namnet på användar kontot om du använder Basic-eller Windows-autentisering. |Inga |
| **lösenord** |Lösen ordet för användar kontot. |Inga |
| **gatewayName** |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En lista över de avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt, till exempel **struktur**, **tillgänglighet** och **principen** för en data uppsättnings-JSON, liknar alla typer av data uppsättningar (Azure SQL, Azure Blob Storage, Azure Table Storage och så vidare).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för en data uppsättning av typen **RelationalTable**, som innehåller DB2-datauppsättningen, har följande egenskap:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **tableName** |Namnet på tabellen i DB2-databas instansen som den länkade tjänsten refererar till. Den här egenskapen är Skift läges känslig. |Nej (om egenskapen **fråga** för en kopierings aktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper
En lista över de avsnitt och egenskaper som är tillgängliga för att definiera kopierings aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Kopierings aktivitets egenskaperna, till exempel **namn**, **Beskrivning**, **indata** -tabell, **utdata** -tabell och **policy**, är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i **typeProperties** -avsnittet av aktiviteten varierar för varje aktivitets typ. För kopierings aktiviteten varierar egenskaperna beroende på typerna av data källor och mottagare.

För kopierings aktiviteten, när källan är av typen **RelationalSource** (som innehåller DB2), är följande egenskaper tillgängliga i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| **frågeterm** |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempelvis: `"query": "select * from "MySchema"."MyTable""` |Nej (om egenskapen **TableName** för en data uppsättning anges) |

> [!NOTE]
> Schema-och tabell namn är Skift läges känsliga. I frågeuttrycket omger du egenskaps namnen med hjälp av "" (dubbla citat tecken).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-exempel: kopiera data från DB2 till Azure Blob Storage
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Exemplet visar hur du kopierar data från en DB2-databas till Blob Storage. Data kan dock kopieras till [en typ av data lager mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Azure Data Factory kopierings aktivitet.

Exemplet har följande Data Factory entiteter:

- En länkad DB2-tjänst av typen [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- En länkad Azure Blob Storage-tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder egenskaperna [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från ett frågeresultat i en DB2-databas till en Azure-Blob per timme. De JSON-egenskaper som används i exemplet beskrivs i avsnitten som följer enhets definitionerna.

Det första steget är att installera och konfigurera en datagateway. Anvisningar finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

**DB2-länkad tjänst**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Länkad Azure Blob Storage-tjänst**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Data uppsättning för DB2-indata**

Exemplet förutsätter att du har skapat en tabell i DB2 med namnet "Tabell" som innehåller en kolumn med etiketten "timestamp" för tids serie data.

Egenskapen **external** har angetts till "true". Den här inställningen informerar Data Factory tjänsten om att den här data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken. Observera att egenskapen **Type** har angetts till **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny BLOB varje timme genom att ange **frekvens** egenskapen till "timme" och egenskapen **Interval** till 1. **FolderPath** -egenskapen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder året, månaden, dagen och timvärdet i Start tiden.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline för kopierings aktiviteten**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda angivna data uppsättningar och utdata och som är schemalagda att köras varje timme. I JSON-definitionen för pipelinen anges **käll** typen till **RelationalSource** och **mottagar** typen är inställd på **BlobSink**. SQL-frågan som angetts för egenskapen **fråga** väljer data från tabellen "Orders".

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Typ mappning för DB2
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från källtyp till mottagar typ med hjälp av följande två stegs metod:

1. Konvertera från en ursprunglig källtyp till en .NET-typ
2. Konvertera från en .NET-typ till en typ av intern mottagare

Följande mappningar används när kopierings aktiviteten konverterar data från en DB2-typ till en .NET-typ:

| Typ av DB2-databas | .NET Framework typ |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Verkligen |Enkel |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Datum |DateTime |
| Tid |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte [] |
| Char |Sträng |
| VarChar |Sträng |
| LongVarChar |Sträng |
| DB2DynArray |Sträng |
| Binär |Byte [] |
| VarBinary |Byte [] |
| LongVarBinary |Byte [] |
| Infoga |Sträng |
| VarGraphic |Sträng |
| LongVarGraphic |Sträng |
| CLOB |Sträng |
| Blob |Byte [] |
| DbClob |Sträng |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Verkligen |Enkel |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numerisk |Decimal |
| Datum |DateTime |
| Tid |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte [] |
| Char |Sträng |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i mottagar data uppsättningen finns i avsnittet [mappa data uppsättnings kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Upprepnings bara läsningar från Relations källor
När du kopierar data från ett Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera **princip** egenskapen för återförsök för en data uppsättning för att köra en sektor igen när ett fel uppstår. Se till att samma data är Läs oavsett hur många gånger som sektorn körs igen och oavsett hur du kör om sektorn. Mer information finns i [repeterbara läsningar från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestandan för kopierings aktiviteten och hur du optimerar prestanda i [guiden Kopiera aktivitet prestanda och justering](data-factory-copy-activity-performance.md).
