---
title: Flytta data från OData-källor
description: Lär dig mer om hur du flyttar data från OData-källor med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fae78459a752d78fe47f189bca67667e917ba561
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380074"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Flytta data från en OData-källa med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-odata-connector.md)
> * [Version 2 (aktuell version)](../connector-odata.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [OData Connector i v2](../connector-odata.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en OData-källa. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från en OData-källa till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från en OData-källa till andra data lager, men inte för att flytta data från andra data lager till en OData-källa.

## <a name="supported-versions-and-authentication-types"></a>Versioner och autentiseringstyper som stöds
Denna OData-anslutning stöder OData version 3,0 och 4,0, och du kan kopiera data från både OData-och lokala OData-källor i molnet. För den senare måste du installera Data Management Gateway. Mer information om Data Management Gateway finns i artikeln [Flytta data mellan lokalt och i molnet](data-factory-move-data-between-onprem-and-cloud.md) .

Under autentiseringstyper stöds:

* För att komma åt **molnbaserad** OData-feed kan du använda Anonym, grundläggande (användar namn och lösen ord) eller Azure Active Directory baserad OAuth-autentisering.
* För att komma åt **den lokala** OData-feeden kan du använda Anonym, grundläggande (användar namn och lösen ord) eller Windows-autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från en OData-källa med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från en OData-källa finns i [JSON-exempel: kopiera data från OData-källa till Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för OData-källan:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för OData-länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OData** |Ja |
| url |URL för OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För Cloud OData är möjliga värden Anonym, Basic och OAuth (Obs Azure Data Factory stöder för närvarande endast Azure Active Directory baserade OAuth). <br/><br/> För lokala OData-enheter är möjliga värden Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Ja (endast om du använder grundläggande autentisering) |
| authorizedCredential |Om du använder OAuth klickar du på knappen **auktorisera** i guiden Data Factory kopiering eller redigeraren och anger dina autentiseringsuppgifter. sedan genereras värdet för den här egenskapen automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala OData-tjänsten. Ange endast om du kopierar data från den lokala OData-källan. |Inga |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Använda Windows-autentisering åtkomst till lokal OData-källa
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Använda OAuth-autentisering åtkomst till molnbaserad OData-källa
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **ODataResource** (som innehåller OData-datauppsättningen) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| path |Sökväg till OData-resursen |Inga |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

Vilka egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten å andra sidan varierar med varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan är av typen **RelationalSource** (som inkluderar OData) finns följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Exempel | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |"? $select = namn, beskrivning&$top = 5" |Inga |

## <a name="type-mapping-for-odata"></a>Typ mappning för OData
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande två stegs metod.

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data från OData används följande mappningar från OData-typer till .NET-typ.

| OData-datatyp | .NET-typ |
| --- | --- |
| EDM. Binary |Byte [] |
| Edm.Boolean |Bool |
| EDM. byte |Byte [] |
| EDM. DateTime |DateTime |
| EDM. decimal |Decimal |
| Edm.Double |Double |
| EDM. Single |Enkel |
| EDM. GUID |GUID |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. SByte |Int16 |
| Edm.String |Sträng |
| EDM. Time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> OData komplexa data typer, t. ex. objekt stöds inte.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-exempel: kopiera data från OData-källa till Azure-Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från en OData-källa till en Azure-Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory. Exemplet har följande Data Factory entiteter:

1. En länkad tjänst av typen [OData](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [ODataResource](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från frågor till en OData-källa till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**OData-länkad tjänst:** I det här exemplet används anonym autentisering. Se avsnittet [OData-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Azure Storage länkad tjänst:**

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

**Data uppsättning för OData-indata:**

Inställningen "extern": "true" informerar den Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Det är valfritt att ange **sökväg** i data uppsättnings definitionen.

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopiera aktivitet i en pipeline med OData-källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **RelationalSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges för egenskapen **query** väljer de senaste (nyaste) data från OData-källan.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Det är valfritt att ange **frågan** i pipeline-definitionen. **URL** : en för den Data Factory tjänsten använder för att hämta data är: URL: en som anges i den länkade tjänsten (krävs) + sökväg som anges i data uppsättningen (valfritt) + fråga i pipelinen (valfritt).

### <a name="type-mapping-for-odata"></a>Typ mappning för OData
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande 2-steg-metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När data flyttas från OData-data källor mappas OData-datatyper till .NET-typer.

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från Relations källor
När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
