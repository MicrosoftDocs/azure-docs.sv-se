---
title: Kopiera data från ServiceNow
description: Lär dig hur du kopierar data från ServiceNow till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378459"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiera data från ServiceNow med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från ServiceNow. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här ServiceNow-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från ServiceNow till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning, och du behöver därför inte installera någon driv rutin manuellt med hjälp av den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för ServiceNow-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten ServiceNow:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **ServiceNow** | Ja |
| slutpunkt | Slut punkten för ServiceNow-servern ( `http://<instance>.service-now.com` ).  | Ja |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är: **Basic**, **OAuth2** | Ja |
| användarnamn | Det användar namn som används för att ansluta till ServiceNow-servern för Basic-och OAuth2-autentisering.  | Ja |
| password | Lösen ordet som motsvarar användar namnet för Basic-och OAuth2-autentisering. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| ClientID | Klient-ID för OAuth2-autentisering.  | Inga |
| clientSecret | Klient hemligheten för OAuth2-autentisering. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värd namnet i Server certifikatet måste matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är True.  | Inga |
| usePeerVerification | Anger om du vill verifiera serverns identitet vid anslutning via TLS. Standardvärdet är True.  | Inga |

**Exempel:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow DataSet.

Om du vill kopiera data från ServiceNow anger du egenskapen type för data uppsättningen till **ServiceNowObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **ServiceNowObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-källan.

### <a name="servicenow-as-source"></a>ServiceNow som källa

Om du vill kopiera data från ServiceNow anger du käll typen i kopierings aktiviteten till **ServiceNowSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **ServiceNowSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM Actual.alm_asset"`. | Nej (om "tableName" i data uppsättningen har angetts) |

Tänk på följande när du anger schema och kolumn för ServiceNow i Query och **se [prestanda tips](#performance-tips) för indirekt för kopierings prestanda**.

- **Schema:** ange schemat som `Actual` eller `Display` i ServiceNow-frågan, som du kan titta på som parametern till `sysparm_display_value` true eller false när du anropar [ServiceNow RESTful-API: er](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumn:** kolumn namnet för det faktiska värdet under `Actual` schemat är `[column name]_value` , men för visnings värde under `Display` schema är `[column name]_display_value` . Observera att kolumn namnet måste mappas till det schema som används i frågan.

**Exempel fråga:** 
 `SELECT col_value FROM Actual.alm_asset` ELLER`SELECT col_display_value FROM Display.alm_asset`

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Prestandatips

### <a name="schema-to-use"></a>Schema som ska användas

ServiceNow har två olika scheman, ett är **"faktiskt"** som returnerar faktiska data, den andra är **"Visa"** som returnerar visnings värden för data. 

Om du har ett filter i frågan använder du schemat "faktiskt" som har bättre kopierings prestanda. Vid frågor mot "faktiskt" schema, ServiceNow internt stöd filter vid hämtning av data för att endast returnera den filtrerade resultat uppsättningen, medan du frågar "Display"-schemat, hämtar ADF alla data och tillämpar filtret internt.

### <a name="index"></a>Index

ServiceNow tabell index kan hjälpa till att förbättra prestanda för frågor, se [skapa ett tabell index](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
