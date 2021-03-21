---
title: Kopiera data från MongoDB med hjälp av äldre
description: Lär dig hur du kopierar data från Mongo DB till mottagar data lager med stöd för en kopierings aktivitet i en äldre Azure Data Factory pipeline.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: e13a1a5a939d314bdf4500c0827fa13201505016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368854"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Kopiera data från MongoDB med Azure Data Factory (bakåtkompatibelt)

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuell version](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!IMPORTANT]
>ADF lansera en ny MongoDB-anslutning som ger bättre inbyggda MongoDB-stöd som jämförs med den här ODBC-baserade implementeringen, se [MongoDB Connector](connector-mongodb.md) -artikeln om information. Den här äldre MongoDB-anslutningen behålls i befintligt skick, och för nya arbets belastningar kan du använda den nya anslutningen.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna MongoDB-anslutning:

- MongoDB- **versionerna 2,4, 2,6, 3,0, 3,2, 3,4 och 3,6**.
- Kopiera data med **Basic** eller **Anonym** autentisering.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime innehåller en inbyggd MongoDB-drivrutin, och du behöver därför inte installera någon driv rutin manuellt när du kopierar data från MongoDB.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för MongoDB-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten MongoDB:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **MongoDB** |Ja |
| server |IP-adressen eller värd namnet för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klient anslutningar. |Nej (standard är 27017) |
| Databas |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| authenticationType | Typ av autentisering som används för att ansluta till MongoDB-databasen.<br/>Tillåtna värden är: **Basic** och **Anonymous**. |Ja |
| användarnamn |Användar konto för åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrol lera autentiseringsuppgifterna för autentisering. |Nej. För grundläggande autentisering är standardvärdet att använda administratörs kontot och den databas som anges med egenskapen databaseName. |
| enableSsl | Anger om anslutningarna till servern är krypterade med TLS. Standardvärdet är false.  | Inga |
| allowSelfSignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **MongoDbCollection** | Ja |
| Samling |Namnet på samlingen i MongoDB-databasen. |Ja |

**Exempel:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källan.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **MongoDbSource** | Ja |
| DocumentDB |Använd den anpassade SQL-92-frågan för att läsa data. Exempel: Välj * från tabellen tabell. |Nej (om "samlings namn" i dataset har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> När du anger SQL-frågan ska du tänka på DateTime-formatet. Till exempel: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` eller för att använda parameter `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema efter Data Factory

Azure Data Factory tjänst härleder schemat från en MongoDB-samling med de **senaste 100 dokumenten** i samlingen. Om dessa 100-dokument inte innehåller fullständigt schema kan vissa kolumner ignoreras under kopieringen.

## <a name="data-type-mapping-for-mongodb"></a>Data typs mappning för MongoDB

När du kopierar data från MongoDB används följande mappningar från MongoDB data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Data typen MongoDB | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| Binär |Byte [] |
| Boolesk |Boolesk |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sträng |
| Sträng |Sträng |
| UUID |GUID |
| Objekt |Förnormaliserad till att förenkla kolumner med "_" som kapslad avgränsare |

> [!NOTE]
> Information om stöd för matriser som använder virtuella tabeller finns i avsnittet [stöd för komplexa typer med hjälp av virtuella tabeller](#support-for-complex-types-using-virtual-tables) .
>
> För närvarande stöds inte följande MongoDB-data typer: DBPointer, Java Script, max/min-nyckel, reguljärt uttryck, symbol, tidsstämpel, odefinierad.

## <a name="support-for-complex-types-using-virtual-tables"></a>Stöd för komplexa typer med hjälp av virtuella tabeller

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från MongoDB-databasen. För komplexa typer, till exempel matriser eller objekt med olika typer i dokument, normaliserar driv rutinen data till motsvarande virtuella tabeller. Mer specifikt, om en tabell innehåller sådana kolumner, genererar driv rutinen följande virtuella tabeller:

* En **bas tabell** som innehåller samma data som den verkliga tabellen förutom de komplexa typ kolumnerna. Bas tabellen använder samma namn som den verkliga tabell som den representerar.
* En **virtuell tabell** för varje komplex typ kolumn som utökar de kapslade data. De virtuella tabellerna namnges med hjälp av namnet på den verkliga tabellen, avgränsaren "_" och namnet på matrisen eller objektet.

Virtuella tabeller refererar till datan i den verkliga tabellen, vilket gör att driv rutinen kan komma åt denormaliserade data. Du kan komma åt innehållet i MongoDB-matriser genom att fråga och ansluta till de virtuella tabellerna.

### <a name="example"></a>Exempel

Till exempel är ExampleTable här en MongoDB-tabell med en kolumn med en matris med objekt i varje cell – fakturor och en kolumn med en matris av skalära typer – klassificeringar.

| _id | Kundnamn | Fakturor | Servicenivå | Klassificeringar |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "toaster", Price: "456", Discount: "0,2"}, {invoice_id: "124", item: "ugn", pris: "1235", rabatt: "0,2"}] |Silver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", item: "kyl skåp", Price: "12543", Discount: "0,0"}] |Guld |[1, 2] |

Driv rutinen skulle generera flera virtuella tabeller som representerar den här enskilda tabellen. Den första virtuella tabellen är bas tabellen med namnet "ExampleTable", som visas i exemplet. Bas tabellen innehåller alla data i den ursprungliga tabellen, men data från matriserna har utelämnats och expanderats i de virtuella tabellerna.

| _id | Kundnamn | Servicenivå |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Guld |

Följande tabeller visar de virtuella tabeller som representerar de ursprungliga matriserna i exemplet. Tabellerna innehåller följande:

* En referens tillbaka till den ursprungliga primär nyckel kolumnen som motsvarar raden i den ursprungliga matrisen (via kolumnen _id)
* En indikation på positionen för data i den ursprungliga matrisen
* De expanderade data för varje element i matrisen

**Tabell ExampleTable_Invoices:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | objekt | price | Rabatt |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0,2 |
| 1111 |1 |124 |ugnen |1235 |0,2 |
| 2222 |0 |135 |kyl skåp |12543 |0.0 |

**Tabell ExampleTable_Ratings:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
