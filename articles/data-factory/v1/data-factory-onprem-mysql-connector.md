---
title: Flytta data från MySQL med Azure Data Factory
description: Lär dig mer om hur du flyttar data från MySQL-databasen med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83c39435d2249981a45798ffe0717054fa7b0717
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387333"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Flytta data från MySQL med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-mysql-connector.md)
> * [Version 2 (aktuell version)](../connector-mysql.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [MySQL Connector i v2](../connector-mysql.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal MySQL-databas. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från ett lokalt MySQL-datalager till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från ett MySQL-datalager till andra data lager, men inte för att flytta data från andra data lager till ett MySQL-datalager. 

## <a name="prerequisites"></a>Förutsättningar
Data Factory tjänsten stöder anslutning till lokala MySQL-källor med hjälp av Data Management Gateway. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar om du vill lära dig mer om data Management Gateway och stegvisa anvisningar för hur du konfigurerar gatewayen.

Gateway krävs även om MySQL-databasen finns på en virtuell Azure IaaS-dator (VM). Du kan installera gatewayen på samma virtuella dator som data lagret eller på en annan virtuell dator så länge som gatewayen kan ansluta till databasen.

> [!NOTE]
> Mer information om fel sökning av problem med anslutning/Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
För att Data Management Gateway ansluta till MySQL-databasen måste du installera [MySQL Connector/net för Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (version mellan 6.6.5 och 6.10.7) på samma system som data Management Gateway. Den här 32-bitars driv rutinen är kompatibel med 64-bitars Data Management Gateway. MySQL version 5,1 och senare stöds.

> [!TIP]
> Om du trycker på fel på "Autentiseringen misslyckades eftersom fjärrparten har stängt transport data strömmen", bör du uppgradera MySQL Connector/NET till en senare version.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt Cassandra data lager med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. 
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett lokalt MySQL-datalager finns i [JSON-exempel: kopiera data från MySQL till Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för ett MySQL-data lager:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för MySQL-länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OnPremisesMySql** |Ja |
| server |Namnet på MySQL-servern. |Ja |
| databas |Namnet på MySQL-databasen. |Ja |
| schema |Namnet på schemat i databasen. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till MySQL-databasen. Möjliga värden är: `Basic` . |Ja |
| userName |Ange användar namnet för att ansluta till MySQL-databasen. |Ja |
| password |Ange lösen ordet för det användar konto som du har angett. |Ja |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala MySQL-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **RelationalTable** (som innehåller MySQL-datauppsättningen) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i MySQL-databasen som den länkade tjänsten refererar till. |Nej (om **fråga** för **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utgående tabeller är principer tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan i kopierings aktiviteten är av typen **RelationalSource** (som innehåller MySQL) är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: Välj * från tabellen tabell. |Nej (om **TableName** för **data uppsättningen** har angetts) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-exempel: kopiera data från MySQL till Azure-Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det visar hur du kopierar data från en lokal MySQL-databas till en Azure-Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Det innehåller inga steg-för-steg-instruktioner för att skapa data fabriken. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner.

Exemplet har följande data Factory-entiteter:

1. En länkad tjänst av typen [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i MySQL-databasen till en BLOB per timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Det första steget är att konfigurera data Management Gateway. Anvisningarna finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

**MySQL-länkad tjänst:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Azure Storage länkad tjänst:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**Data uppsättning för MySQL:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i MySQL och innehåller en kolumn med namnet "timestampcolumn" för Time Series-data.

Inställningen "extern": "true" informerar den Data Factory tjänsten att tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **RelationalSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges för egenskapen **fråga** väljer data under den senaste timmen som ska kopieras.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Typ mappning för MySQL
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande två stegs metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till MySQL används följande mappningar från MySQL-typer till .NET-typer.

| MySQL-databas typ | .NET Framework typ |
| --- | --- |
| bigint har inte signerats |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob |Byte [] |
| boolesk |Boolesk |
| char |Sträng |
| date |Datumtid |
| datetime |Datumtid |
| decimal |Decimal |
| dubbel precision |Double |
| double |Double |
| räkning |Sträng |
| flyt |Enkel |
| int-signering |Int64 |
| int |Int32 |
| heltal ej signerat |Int64 |
| heltal |Int32 |
| långt varbinary |Byte [] |
| lång varchar |Sträng |
| longblob |Byte [] |
| longtext |Sträng |
| mediumblob |Byte [] |
| mediumint osignerad |Int64 |
| mediumint |Int32 |
| mediumtext |Sträng |
| numeric |Decimal |
| real |Double |
| set |Sträng |
| smallint är osignerad |Int32 |
| smallint |Int16 |
| text |Sträng |
| time |TimeSpan |
| timestamp |Datumtid |
| tinyblob |Byte [] |
| tinyint ej signerat |Int16 |
| tinyint |Int16 |
| tinytext |Sträng |
| varchar |Sträng |
| år |Int |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
