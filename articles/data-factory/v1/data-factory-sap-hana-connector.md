---
title: Flytta data från SAP HANA med Azure Data Factory
description: Lär dig mer om hur du flyttar data från SAP HANA med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c08aeca8e7aaa71e5a2a80cbdece762b989e28c2
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510149"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Flytta data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sap-hana-connector.md)
> * [Version 2 (aktuell version)](../connector-sap-hana.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [SAP HANA Connector i v2](../connector-sap-business-warehouse.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal SAP HANA. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från ett lokalt SAP HANA data lager till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från en SAP HANA till andra data lager, men inte för att flytta data från andra data lager till en SAP HANA.

## <a name="supported-versions-and-installation"></a>Versioner och installation som stöds
Den här anslutningen har stöd för alla versioner av SAP HANA Database. Det stöder kopiering av data från HANA-informations modeller (till exempel analys-och beräknings visningar) och rad/kolumn-tabeller med SQL-frågor.

Om du vill aktivera anslutningen till SAP HANA-instansen installerar du följande komponenter:
- **Data Management Gateway**: Data Factory tjänsten stöder anslutning till lokala data lager (inklusive SAP HANA) med hjälp av en komponent som kallas data Management Gateway. Mer information om Data Management Gateway och stegvisa anvisningar för hur du konfigurerar gatewayen finns i avsnittet [Flytta data mellan lokala data lager till moln data lager](data-factory-move-data-between-onprem-and-cloud.md) . Gateway krävs även om SAP HANA finns i en virtuell Azure IaaS-dator (VM). Du kan installera gatewayen på samma virtuella dator som data lagret eller på en annan virtuell dator så länge som gatewayen kan ansluta till databasen.
- **SAP HANA ODBC-drivrutin** på gateway-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**. 

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från ett lokalt SAP HANA data lager med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. 
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från en lokal SAP HANA finns i [JSON-exempel: kopiera data från SAP HANA till Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) i den här artikeln. 

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för ett SAP HANA data lager:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för SAP HANA länkade tjänsten.

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server där SAP HANA-instansen finns. Om servern använder en anpassad port anger du `server:port` . | sträng | Ja
authenticationType | Typ av autentisering. | nollängd. "Basic" eller "Windows" | Ja 
användarnamn | Namnet på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala SAP HANA-instansen. | sträng | Ja
encryptedCredential | Krypterad Credential-sträng. | sträng | No

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Det finns inga typ-/regionsspecifika egenskaper som stöds för SAP HANA-datauppsättningen av typen **RelationalTable**. 


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utgående tabeller är principer tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan i kopierings aktiviteten är av typen **RelationalSource** (som innehåller SAP HANA) finns följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan för att läsa data från SAP HANA-instansen. | SQL-fråga. | Ja |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-exempel: kopiera data från SAP HANA till Azure-Blob
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det här exemplet visar hur du kopierar data från en lokal SAP HANA till en Azure-Blob Storage. Data kan dock kopieras **direkt** till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.  

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Det innehåller inga steg-för-steg-instruktioner för att skapa data fabriken. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner.

Exemplet har följande data Factory-entiteter:

1. En länkad tjänst av typen [SapHana](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SAP HANA instans till en Azure-Blob per timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Det första steget är att konfigurera data Management Gateway. Anvisningarna finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

### <a name="sap-hana-linked-service"></a>SAP HANA länkad tjänst
Den här länkade tjänsten länkar SAP HANA-instansen till data fabriken. Egenskapen Type har angetts till **SapHana**. Avsnittet typeProperties innehåller anslutnings information för SAP HANA-instansen.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Den här länkade tjänsten länkar ditt Azure Storage-konto till data fabriken. Egenskapen Type har angetts till **AzureStorage**. Avsnittet typeProperties innehåller anslutnings information för det Azure Storage kontot.

```json
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

### <a name="sap-hana-input-dataset"></a>SAP HANA indata-datauppsättning

Den här data uppsättningen definierar SAP HANA data uppsättningen. Du anger typ av Data Factory-datauppsättning till **RelationalTable**. För närvarande anger du inga typspecifika egenskaper för en SAP HANA data uppsättning. Frågan i definitionen för kopierings aktiviteten anger vilka data som ska läsas från SAP HANA-instansen. 

Om du anger en extern egenskap till True informerar den Data Factory tjänsten som tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

Egenskaperna för frekvens och intervall definierar schemat. I det här fallet läses data från SAP HANA instansen varje timme. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb
Den här data uppsättningen definierar den utgående Azure Blob-datauppsättningen. Egenskapen Type har angetts till AzureBlob. Avsnittet typeProperties innehåller var data som kopieras från SAP HANA-instansen lagras. Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline med kopierings aktivitet

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **RelationalSource** (för SAP HANA källa) och **mottagar** typen har angetts till **BlobSink**. SQL-frågan som anges för egenskapen **fråga** väljer data under den senaste timmen som ska kopieras.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Typ mappning för SAP HANA
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande två stegs metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data från SAP HANA används följande mappningar från SAP HANA typer till .NET-typer.

SAP HANA typ | .NET-baserad typ
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Enskilt
DOUBLE | Enskilt
DECIMAL | Decimal
BOOLESKT | Byte
VARCHAR | Sträng
NVARCHAR | Sträng
CLOB | Byte []
ALPHANUM | Sträng
BLOB | Byte []
DATE | DateTime
TIME | TimeSpan
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Kända begränsningar
Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR-strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899-12-30 och 9999-12-31

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
