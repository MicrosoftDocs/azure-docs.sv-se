---
title: Kopiera data till eller från Oracle med hjälp av Data Factory
description: Lär dig hur du kopierar data till eller från en lokal Oracle-databas med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02fc142a08176aa577250417c0e394218e832f34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387350"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiera data till eller från Oracle lokalt genom att använda Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [Version 2 (aktuell version)](../connector-oracle.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av tjänsten Azure Data Factory, se [Oracle Connector i v2](../connector-oracle.md).


Den här artikeln förklarar hur du använder kopierings aktivitet i Azure Data Factory för att flytta data till eller från en lokal Oracle-databas. Artikeln bygger på [data förflyttnings aktiviteter](data-factory-data-movement-activities.md), som visar en allmän översikt över data flyttningen med hjälp av kopierings aktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data *från en Oracle-databas* till följande data lager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager *till en Oracle-databas*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Förutsättningar

Data Factory stöder anslutning till lokala Oracle-källor med hjälp av Data Management Gateway. Mer information om Data Management Gateway finns i [Data Management Gateway](data-factory-data-management-gateway.md) . Stegvisa instruktioner för hur du konfigurerar gatewayen i en datapipeline för att flytta data finns i [Flytta data från lokalt till molnet](data-factory-move-data-between-onprem-and-cloud.md).

Gatewayen krävs även om Oracle finns i en virtuell IaaS-dator (Azure Infrastructure as a Service). Du kan installera gatewayen på samma virtuella IaaS-dator som data lagret eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.

> [!NOTE]
> Tips på hur du felsöker problem som rör anslutning och Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds

Den här Oracle-anslutaren stöder två versioner av driv rutiner:

- **Microsoft-drivrutin för Oracle (rekommenderas)**: från och med Data Management Gateway version 2,7 installeras en Microsoft-drivrutin för Oracle automatiskt med gatewayen. Du behöver inte installera eller uppdatera driv rutinen för att upprätta en anslutning till Oracle. Du kan också få bättre kopierings prestanda genom att använda den här driv rutinen. Dessa versioner av Oracle-databaser stöds:
  - Oracle 12C R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle-proxyservern stöds inte.

    > [!IMPORTANT]
    > Microsoft-drivrutinen för Oracle stöder för närvarande endast kopiering av data från Oracle. Driv rutinen har inte stöd för skrivning till Oracle. Funktionen testa anslutning på fliken Data Management Gateway **diagnostik** stöder inte den här driv rutinen. Du kan också använda guiden Kopiera för att verifiera anslutningen.
    >

- **Oracle Data Provider för .net**: du kan använda Oracle-dataleverantören för att kopiera data från eller till Oracle. Den här komponenten ingår i [Oracle Data Access Components för Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Installera den relevanta versionen (32-bitars eller 64-bitars) på den dator där gatewayen är installerad. [Oracle Data Provider .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) har åtkomst till Oracle Database 10g version 2 och senare versioner.

    Om du väljer **xcopy-installation** slutför du stegen som beskrivs i readme.htm-filen. Vi rekommenderar att du väljer det installations program som har användar gränssnittet (inte installations programmet för XCopy).

    När du har installerat providern startar du om Data Management Gateway värd tjänsten på datorn med hjälp av tjänst-appleten eller Data Management Gateway Configuration Manager.

Om du använder kopierings guiden för att redigera kopierings pipelinen, är driv rutins typen autobestämd. Microsoft-drivrutinen används som standard, om inte Gateway-versionen är tidigare än version 2,7 eller om du väljer Oracle som mottagare.

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline som har en kopierings aktivitet. Pipelinen flyttar data till eller från en lokal Oracle-databas med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera. Se [Självstudier: skapa en pipeline med hjälp av kopierings guiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda något av följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, en **Azure Resource Manager mall**, .net- **API** eller **REST API**. I [självstudien om kopierings aktiviteten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finns stegvisa anvisningar om hur du skapar en pipeline med en kopierings aktivitet.

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du t. ex. kopierar data från en Oracle-databas till Azure Blob Storage, skapar du två länkade tjänster för att länka din Oracle-databas och Azure Storage-konto till data fabriken. För länkade tjänst egenskaper som är speciella för Oracle, se [länkade tjänst egenskaper](#linked-service-properties).
3. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet i föregående steg skapar du en data uppsättning för att ange den tabell i Oracle-databasen som innehåller indata. Du skapar en annan data uppsättning för att ange BLOB-behållaren och mappen som innehåller data som kopieras från Oracle-databasen. För data uppsättnings egenskaper som är speciella för Oracle, se [data uppsättnings egenskaper](#dataset-properties).
4. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I föregående exempel använder du **OracleSource** som källa och **BlobSink** som mottagare för kopierings aktiviteten. På samma sätt kan du använda **BlobSource** och **OracleSink** i kopierings aktiviteten om du kopierar från Azure Blob Storage till en Oracle-databas. Information om kopiera aktivitets egenskaper som är speciella för en Oracle-databas finns i [Kopiera aktivitets egenskaper](#copy-activity-properties). Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare väljer du länken för data lagret i föregående avsnitt.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter automatiskt åt dig: länkade tjänster, data uppsättningar och pipelinen. När du använder verktyg eller API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel som innehåller JSON-definitioner för Data Factory entiteter som du använder för att kopiera data till eller från en lokal Oracle-databas finns i JSON-exempel.

I följande avsnitt finns information om JSON-egenskaper som du använder för att definiera Data Factory entiteter.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

I följande tabell beskrivs JSON-element som är speciella för den länkade Oracle-tjänsten:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen **Type** måste anges till **OnPremisesOracle**. |Ja |
| driverType | Ange vilken driv rutin som ska användas för att kopiera data från eller till en Oracle-databas. Tillåtna värden är **Microsoft** och **ODP** (standard). Se [version och installation som stöds](#supported-versions-and-installation) för driv rutins information. | Inga |
| Begär | Ange den information som krävs för att ansluta till Oracle Database-instansen för egenskapen **ConnectionString** . | Ja |
| gatewayName | Namnet på den gateway som används för att ansluta till den lokala Oracle-servern. |Ja |

**Exempel: använda Microsoft-drivrutinen**

> [!TIP]
> Om du ser ett fel med texten "ORA-01025: UPI-parameter utanför intervallet" och din Oracle är version 8i, lägger du till `WireProtocolMode=1` i anslutnings strängen och försöker igen:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exempel: använda ODP-drivrutinen**

Mer information om tillåtna format finns i [Oracle Data Provider för .net ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [skapa data uppsättningar](data-factory-create-datasets.md).

Avsnitten i en data mängds JSON-fil, till exempel struktur, tillgänglighet och princip, liknar alla typer av data uppsättningar (till exempel för Oracle, Azure Blob Storage och Azure Table Storage).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för data uppsättningen av typen **OracleTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databasen som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** eller **OracleSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipeliner](data-factory-create-pipelines.md).

Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopierings aktiviteten tar bara en indata och producerar bara ett resultat.

Egenskaper som är tillgängliga i **typeProperties** -avsnittet av aktiviteten varierar beroende på varje aktivitets typ. Egenskaperna för kopierings aktivitet varierar beroende på typ av källa och mottagare.

### <a name="oraclesource"></a>OracleSource

När källan är av typen **OracleSource** i kopierings aktivitet är följande egenskaper tillgängliga i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd den anpassade frågan för att läsa data. |En SQL-frågesträng. Till exempel "Välj \* från **tabellen**". <br/><br/>Om detta inte anges körs SQL-instruktionen: "Välj \* från **tabellen**" |Inga<br />(om **TableName** för **dataset** har angetts) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tiden för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |**tidsintervall**<br/><br/> Exempel: 00:30:00 (30 minuter) |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når värdet för **writeBatchSize**. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Anger en fråga för kopierings aktivitet som ska köras så att data för en speciell sektor rensas. |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Anger kolumn namnet för kopierings aktiviteten så att den fyller ett automatiskt genererat segment-ID. Värdet för **sliceIdentifierColumnName** används för att rensa data i en angiven sektor när den körs igen. |Kolumn namnet för en kolumn med data typen **Binary (32)**. |Inga |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-exempel för att kopiera data till och från Oracle-databasen

I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). I exemplen visas hur du kopierar data från eller till en Oracle-databas och till eller från Azure Blob Storage. Data kan dock kopieras till någon av de handfat som anges i [data lager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten i Azure Data Factory.

**Exempel: kopiera data från Oracle till Azure Blob Storage**

Exemplet har följande Data Factory entiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) som källa och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en tabell i en lokal Oracle-databas till en BLOB per timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracle-länkad tjänst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Länkad Azure Blob Storage-tjänst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Data uppsättning för Oracle-indata**

Exemplet förutsätter att du har skapat en tabell med namnet min **tabell** i Oracle. Den innehåller en kolumn med namnet **timestampcolumn** för Time Series-data.

Inställningen **external**: **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och att data uppsättningen inte produceras av en aktivitet i data fabriken.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

Data skrivs till en ny BLOB varje timme (**frekvens**: **timme**, **intervall**: **1**). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden, dagen och Tim delen av start tiden.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline med en kopieringsaktivitet**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **OracleSource** och **mottagar** typen är inställd på **BlobSink**. SQL-frågan som du anger med hjälp av egenskapen **oracleReaderQuery** väljer data under den senaste timmen som ska kopieras.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

**Exempel: kopiera data från Azure Blob Storage till Oracle**

Det här exemplet visar hur du kopierar data från ett Azure Blob Storage-konto till en lokal Oracle-databas. Du kan dock kopiera data *direkt* från någon av källorna som anges i [data lager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten i Azure Data Factory.

Exemplet har följande Data Factory entiteter:

* En länkad tjänst av typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) som har en kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) som [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) som mottagare.

Exemplet kopierar data från en blob till en tabell i en lokal Oracle-databas varje timme. Mer information om olika egenskaper som används i exemplet finns i avsnitten som följer exemplen.

**Oracle-länkad tjänst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Länkad Azure Blob Storage-tjänst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Data uppsättning för Azure Blob-indata**

Data hämtas från en ny BLOB varje timme (**frekvens**: **timme**, **intervall**: **1**). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden. Fil namnet använder Tim delen av start tiden. Inställningen **external**: **True** informerar Data Factory tjänsten som den här tabellen är extern för data fabriken och produceras inte av en aktivitet i data fabriken.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Data uppsättning för Oracle-utdata**

Exemplet förutsätter att du har skapat en tabell med namnet min **tabell** i Oracle. Skapa tabellen i Oracle med samma antal kolumner som du förväntar sig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline med en kopieringsaktivitet**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och som är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typen är inställd på **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Felsökningstips

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework data leverantör

**Felmeddelande**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Möjliga orsaker**

* .NET Framework Data Provider för Oracle har inte installerats.
* .NET Framework Data Provider för Oracle installerades på .NET Framework 2,0 och finns inte i .NET Framework 4,0-mapparna.

**Lösning**

* Om du inte har installerat .NET-providern för Oracle [installerar du den](https://www.oracle.com/technetwork/topics/dotnet/downloads/)och försöker sedan igen.
* Om du ser fel meddelandet även efter att du har installerat providern utför du följande steg:
    1. Öppna dator konfigurations filen för .NET 2,0 från mappen <system disk \>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Sök efter **Oracle Data Provider för .net**. Du bör kunna hitta en post som visas i följande exempel under **system. data**  >  **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopiera den här posten till machine.config-filen i följande .NET 4,0-mapp: <system disk \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ändra sedan versionen till 4. xxx. x.x.
* Installera <ODP.NET \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll i GAC (Global Assembly Cache) genom att köra **Gacutil/i [sökväg för provider]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: formatering av datum/tid

**Felmeddelande**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Lösning**

Du kan behöva justera frågesträngen i kopierings aktiviteten baserat på hur datum har kon figurer ATS i Oracle-databasen. Här är ett exempel (med hjälp av funktionen **to_date** ):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Typ mappning för Oracle

Som nämnts i [data förflyttnings aktiviteter](data-factory-data-movement-activities.md)utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med hjälp av följande två stegs metod:

1. Konvertera från interna käll typer till .NET-typen.
2. Konvertera från .NET-typen till den interna mottagar typen.

När du flyttar data från Oracle används följande mappningar från data typen Oracle till .NET-typen och vice versa:

| Oracle-datatyp | .NET Framework data typ |
| --- | --- |
| BFILE |Byte [] |
| BLOB |Byte []<br/>(stöds endast på Oracle 10g och senare versioner när du använder en Microsoft-drivrutin) |
| CHAR |Sträng |
| CLOB |Sträng |
| DATE |DateTime |
| FLYTA |Decimal, sträng (om precision > 28) |
| INTEGER |Decimal, sträng (om precision > 28) |
| INTERVALL ÅR TILL MÅNAD |Int32 |
| INTERVALL, DAG TILL SEKUND |TimeSpan |
| SOM |Sträng |
| LÅNG RAW |Byte [] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| MÅNGA |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |Sträng |
| OUTSPÄDD |Byte [] |
| ROWID |Sträng |
| TIMESTAMP |DateTime |
| TIDSSTÄMPEL MED LOKAL TIDSZON |DateTime |
| TIDSSTÄMPEL MED TIDSZON |DateTime |
| OSIGNERAT HELTAL |Antal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Data typerna **Interval år till månad** och **intervall dag till sekund** stöds inte när du använder en Microsoft-drivrutin.

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner

Mer information om att mappa kolumner i käll data uppsättningen till kolumner i mottagar data uppsättningen finns i avsnittet [mappa data uppsättnings kolumner i Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor

När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om en sektor manuellt. Du kan också konfigurera en princip för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor kör om, antingen manuellt eller av en princip för återförsök, se till att samma data är läst oavsett hur många gånger en sektor körs. Mer information finns i [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering

Se [prestanda-och justerings guiden för kopierings aktivitet](data-factory-copy-activity-performance.md) och lär dig mer om viktiga faktorer som påverkar prestandan för data flytten (kopierings aktivitet) i Azure Data Factory. Du kan också lära dig om olika sätt att optimera det.
