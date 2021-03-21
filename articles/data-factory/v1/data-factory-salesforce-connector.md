---
title: Flytta data från Salesforce med hjälp av Data Factory
description: Lär dig mer om hur du flyttar data från Salesforce med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ccc20f415d13356de755af5d1d3afc5b29de72f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387061"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Flytta data från Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-salesforce-connector.md)
> * [Version 2 (aktuell version)](../connector-salesforce.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Salesforce Connector i v2](../connector-salesforce.md).

Den här artikeln beskriver hur du kan använda kopierings aktivitet i en Azure-datafabrik för att kopiera data från Salesforce till alla data lager som visas under kolumnen mottagare i tabellen med [källor och mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Den här artikeln bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som innehåller en allmän översikt över data förflyttning med kombinationer kopiera aktivitet och stöd för data lager.

Azure Data Factory stöder för närvarande endast flytt av data från Salesforce till [mottagar data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), men som inte har stöd för att flytta data från andra data lager till Salesforce.

## <a name="supported-versions"></a>Versioner som stöds
Den här anslutningen har stöd för följande utgåvor av Salesforce: Developer Edition, Professional Edition, Enterprise Edition eller Unlimited Edition. Och stöder kopiering från Salesforce-produktion, Sandbox och anpassad domän.

## <a name="prerequisites"></a>Förutsättningar
* API-behörighet måste vara aktive rad. Se [Hur gör jag för att aktivera API-åtkomst i Salesforce enligt behörighets uppsättning?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Om du vill kopiera data från Salesforce till lokala data lager måste du ha minst Data Management Gateway 2,0 installerat i din lokala miljö.

## <a name="salesforce-request-limits"></a>Begränsningar för Salesforce-begäran
Salesforce har gränser för både totalt antal API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begär Anden överskrider gränsen inträffar begränsningen och slumpmässiga fel visas.
- Om det totala antalet begär Anden överskrider gränsen kommer Salesforce-kontot att blockeras i 24 timmar.

Du kan också få fel meddelandet "REQUEST_LIMIT_EXCEEDED" i båda scenarierna. Mer information finns i avsnittet "begränsningar för API-begäranden" i artikeln [begränsningar för Salesforce-utvecklare](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) .

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från Salesforce genom att använda olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från Salesforce finns i [JSON-exempel: kopiera data från Salesforce till Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Salesforce:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller beskrivningar av JSON-element som är speciella för den länkade Salesforce-tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instansen. <br><br> -Standard är "https: \/ /login.Salesforce.com". <br> – Om du vill kopiera data från sandbox anger du " https://test.salesforce.com ". <br> – Om du vill kopiera data från en anpassad domän anger du exempelvis "https://[domän]. My. Salesforce. com". |Inga |
| användarnamn |Ange ett användar namn för användar kontot. |Ja |
| password |Ange ett lösen ord för användar kontot. |Ja |
| securityToken |Ange en säkerhetstoken för användar kontot. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer/hämtar en säkerhetstoken. Mer information om säkerhetstoken i allmänhet finns i [säkerhet och API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table och så vidare).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för en data uppsättning av typen **RelationalTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga** för **RelationalSource** har angetts) |

> [!IMPORTANT]
> En "__c"-del av API-namnet krävs för alla anpassade objekt.

![Skärm bild som visar den anpassade objekt definitions informationen där du kan se namnet P I namn på de anpassade objekten.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och olika principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten, å andra sidan, varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

När källan är av typen **RelationalSource** (som innehåller Salesforce) i kopierings aktiviteten är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |En SQL-92-fråga eller en [SOQL-fråga (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Exempel: `select * from MyTable__c`. |Nej (om **data uppsättningens** **TableName** anges) |

> [!IMPORTANT]
> En "__c"-del av API-namnet krävs för alla anpassade objekt.

![Skärm bild som visar de anpassade fälten &-relationer där du kan se ett P I namn på de anpassade objekten.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tips om frågor
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämtar data med WHERE-satsen i kolumnen DateTime
När du anger SOQL-eller SQL-frågan ska du tänka på DateTime-formatets skillnad. Exempel:

* **SOQL-exempel**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-exempel**:
    * **Använda guiden Kopiera för att ange frågan:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Använd JSON-redigering för att ange frågan (escape-tecken korrekt):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Hämtar data från Salesforce-rapport
Du kan hämta data från Salesforce-rapporter genom att ange fråga som `{call "<report name>"}` till exempel. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämtar borttagna poster från Salesforce-pappers korgen
Om du vill fråga de mjuka borttagna posterna från pappers korgen i Salesforce kan du ange **"IsDeleted = 1"** i frågan. Exempel:

* Om du bara vill fråga de borttagna posterna anger du "Select * from MyTable__c **WHERE IsDeleted = 1**"
* Om du vill fråga alla poster, inklusive befintliga och borttagna, anger du "Select * from MyTable__c **WHERE IsDeleted = 0 eller IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-exempel: kopiera data från Salesforce till Azure-Blob
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från Salesforce till Azure Blob Storage. Data kan dock kopieras till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

Här är de Data Factory artefakter som du måste skapa för att implementera scenariot. De avsnitt som följer med i listan innehåller information om de här stegen.

* En länkad tjänst av typen [Salesforce](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* En [data uppsättning](data-factory-create-datasets.md) för utdata av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Länkad Salesforce-tjänst**

I det här exemplet används den länkade **Salesforce** -tjänsten. Se avsnittet [Salesforce-länkad tjänst](#linked-service-properties) för de egenskaper som stöds av den här länkade tjänsten. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer eller hämtar säkerhetstoken.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Azure Storage länkad tjänst**

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
**Data uppsättning för Salesforce-indata**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
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

Om du anger **external** till **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

> [!IMPORTANT]
> En "__c"-del av API-namnet krävs för alla anpassade objekt.

![Skärm bild som visar detalj information om anpassad objekt definition där du kan se singular etikett, plural, objekt namn och ett P I-namn.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Data uppsättning för Azure Blob-utdata**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline med kopierings aktivitet**

Pipelinen innehåller kopierings aktivitet, som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **RelationalSource** och **mottagar** typen är inställd på **BlobSink**.

Se [RelationalSource typ egenskaper](#copy-activity-properties) för listan över egenskaper som stöds av RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
> [!IMPORTANT]
> En "__c"-del av API-namnet krävs för alla anpassade objekt.

![Skärm bild som visar de anpassade fälten & relationer med namnet P I namn.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Typ mappning för Salesforce

| Salesforce-typ | . NET-baserad typ |
| --- | --- |
| Automatisk numrering |Sträng |
| Checkbox |Boolesk |
| Valuta |Decimal |
| Datum |DateTime |
| Datum/tid |DateTime |
| E-post |Sträng |
| Id |Sträng |
| Sök relation |Sträng |
| Listruta för flera val |Sträng |
| Antal |Decimal |
| Procent |Decimal |
| Telefon |Sträng |
| Picklist (Plocklista) |Sträng |
| Text |Sträng |
| Text områden |Sträng |
| Text områden (långt) |Sträng |
| Text områden (Rich) |Sträng |
| Text (krypterad) |Sträng |
| URL |Sträng |

> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda-och justerings guiden för kopierings aktiviteter](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
