---
title: Flytta data från PostgreSQL med hjälp av Azure Data Factory
description: Lär dig mer om hur du flyttar data från PostgreSQL Database med Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 8ca8770944a12c6e0dd3e4f95d286758ebee5f9e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387282"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Flytta data från PostgreSQL med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-postgresql-connector.md)
> * [Version 2 (aktuell version)](../connector-postgresql.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [postgresql Connector i v2](../connector-postgresql.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal PostgreSQL-databas. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från ett lokalt PostgreSQL-data lager till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory har för närvarande stöd för att flytta data från en PostgreSQL-databas till andra data lager, men inte för att flytta data från andra data lager till en PostgreSQL-databas.

## <a name="prerequisites"></a>Förutsättningar

Data Factory-tjänsten stöder anslutning till lokala PostgreSQL-källor med hjälp av Data Management Gateway. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar om du vill lära dig mer om data Management Gateway och stegvisa anvisningar för hur du konfigurerar gatewayen.

Gateway krävs även om PostgreSQL-databasen finns i en virtuell Azure IaaS-dator. Du kan installera gateway på samma IaaS-VM som data lagret eller på en annan virtuell dator så länge som gatewayen kan ansluta till databasen.

> [!NOTE]
> Mer information om fel sökning av problem med anslutning/Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
För att Data Management Gateway ansluta till PostgreSQL-databasen installerar du [Ngpsql-dataprovidern för postgresql](https://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på samma system som data Management Gateway. PostgreSQL version 7,4 och senare stöds.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt PostgreSQL data lager med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-mall
  - .NET-API
  - REST-API

    Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett lokalt PostgreSQL data lager finns i [JSON-exempel: kopiera data från postgresql till Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för ett PostgreSQL-data lager:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för PostgreSQL-länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OnPremisesPostgreSql** |Ja |
| server |Namnet på PostgreSQL-servern. |Ja |
| databas |Namnet på PostgreSQL-databasen. |Ja |
| schema |Namnet på schemat i databasen. Schema namnet är Skift läges känsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder Basic-eller Windows-autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar samma för alla data uppsättnings typer.

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **RelationalTable** (som innehåller postgresql-datauppsättningen) har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell i PostgreSQL-databas instansen som den länkade tjänsten refererar till. TableName är Skift läges känsligt. |Nej (om **fråga** för **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan är av typen **RelationalSource** (som innehåller postgresql) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nej (om **TableName** för **data uppsättningen** har angetts) |

> [!NOTE]
> Schema-och tabell namn är Skift läges känsliga. Omge dem med `""` (dubbla citat tecken) i frågan.

**Exempel:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-exempel: kopiera data från PostgreSQL till Azure-Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från PostgreSQL Database till Azure Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Det innehåller inga steg-för-steg-instruktioner för att skapa data fabriken. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner.

Exemplet har följande data Factory-entiteter:

1. En länkad tjänst av typen [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopierings aktivitet som använder [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i PostgreSQL-databasen till en BLOB varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Som ett första steg konfigurerar du gatewayen för data hantering. Anvisningarna finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

**PostgreSQL länkad tjänst:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
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
**Länkad Azure Blob Storage-tjänst:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL för data uppsättning:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i PostgreSQL och att den innehåller en kolumn med namnet "timestamp" för Time Series-data.

Inställningen `"external": true` informerar Data Factory tjänsten om att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline med kopierings aktivitet:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **RelationalSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges för egenskapen **fråga** väljer data från tabellen Public. Usstates i PostgreSQL-databasen.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Typ mappning för PostgreSQL
Som vi nämnt i artikel kopierings [aktiviteterna för data förflyttning](data-factory-data-movement-activities.md) utförs automatiska typ konverteringar från käll typer till mottagar typer med följande 2-stegs metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till PostgreSQL används följande mappningar från PostgreSQL-typ till .NET-typ.

| Typ av PostgreSQL-databas | PostgresSQL-alias | .NET Framework typ |
| --- | --- | --- |
| abstime | |Datumtid |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], sträng |
| bit varierande [(n)] |varbit |Byte [], sträng |
| boolean |boolesk |Boolesk |
| fältet | |Byte [], sträng |
| bytea | |Byte [], sträng |
| Character [(n)] |char [(n)] |Sträng |
| Character varierande [(n)] |varchar [(n)] |Sträng |
| Cid | |Sträng |
| cidr | |Sträng |
| cirkel | |Byte [], sträng |
| date | |Datumtid |
| daterange | |Sträng |
| dubbel precision |float8 |Double |
| inet | |Byte [], sträng |
| intarry | |Sträng |
| int4range | |Sträng |
| int8range | |Sträng |
| heltal |int, int4 |Int32 |
| intervall [fält] [(p)] | |Tidsintervall |
| json | |Sträng |
| jsonb | |Byte [] |
| linje | |Byte [], sträng |
| lseg | |Byte [], sträng |
| macaddr | |Byte [], sträng |
| money | |Decimal |
| numeriskt [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |Sträng |
| OID | |Int32 |
| path | |Byte [], sträng |
| pg_lsn | |Int64 |
| pekaren | |Byte [], sträng |
| Polygonlasso | |Byte [], sträng |
| real |float4 |Enkel |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| nummer |serial4 |Int32 |
| text | |Sträng |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
