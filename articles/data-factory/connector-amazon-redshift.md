---
title: Kopiera data från Amazon RedShift
description: Läs om hur du kopierar data från Amazon RedShift till mottagar data lager som stöds med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364266"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiera data från Amazon RedShift med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuell version](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en Amazon-RedShift. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Amazon RedShift-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Amazon RedShift till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt har den här Amazon RedShift-anslutningen stöd för att hämta data från RedShift med hjälp av fråge-eller inbyggda RedShift-inläsnings stöd.

> [!TIP]
> För att uppnå bästa prestanda vid kopiering av stora mängder data från RedShift bör du överväga att använda den inbyggda RedShift-inläsningen via Amazon S3. Mer information finns i avsnittet [använda Unload för att kopiera data från Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Förutsättningar

* Om du kopierar data till ett lokalt data lager med hjälp av [egen värd integration runtime](create-self-hosted-integration-runtime.md)beviljar du integration Runtime (Använd IP-adressen för datorn) åtkomst till Amazon RedShift-klustret. Mer information finns i [bevilja åtkomst till klustret](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Om du kopierar data till ett Azure-datalager, se [Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) för den BERÄKNINGS-IP-adress och de SQL-intervall som används av Azure Data Center.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Amazon RedShift-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Amazon RedShift-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AmazonRedshift** | Ja |
| server |IP-adressen eller värd namnet för Amazon RedShift-servern. |Ja |
| port |Numret på den TCP-port som Amazon RedShift-servern använder för att lyssna efter klient anslutningar. |Nej, standard är 5439 |
| databas |Namnet på Amazon RedShift-databasen. |Ja |
| användarnamn |Namnet på den användare som har åtkomst till databasen. |Ja |
| password |Lösen ord för användar kontot. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon RedShift-datauppsättningen.

Följande egenskaper stöds för att kopiera data från Amazon RedShift:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **AmazonRedshiftTable** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon RedShift-källan.

### <a name="amazon-redshift-as-source"></a>Amazon-RedShift som källa

Om du vill kopiera data från Amazon RedShift anger du käll typen i kopierings aktiviteten till **AmazonRedshiftSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **AmazonRedshiftSource** | Ja |
| DocumentDB |Använd den anpassade frågan för att läsa data. Exempel: Välj * från tabellen tabell. |Nej (om "tableName" i data uppsättningen har angetts) |
| redshiftUnloadSettings | Egenskaps grupp när Amazon RedShift tas bort från minnet. | Inga |
| s3LinkedServiceName | Avser en Amazon S3 som ska användas som en tillfällig lagring genom att ange ett länkat tjänst namn av typen "AmazonS3". | Ja om du använder inaktivera |
| bucketName | Ange S3-Bucket för att lagra interims data. Om den inte anges genererar Data Factory tjänsten automatiskt.  | Ja om du använder inaktivera |

**Exempel: Amazon RedShift-källa i kopierings aktivitet med Unload**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Läs mer om hur du använder inläsning för att kopiera data från Amazon RedShift effektivt från nästa avsnitt.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd inläsning för att kopiera data från Amazon RedShift

[Borttagning](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) är en mekanism som tillhandahålls av Amazon Redshift, som kan ta bort resultatet från en fråga till en eller flera filer på Amazon Simple Storage Service (Amazon S3). Det rekommenderas av Amazon för att kopiera stor data uppsättning från RedShift.

**Exempel: kopiera data från Amazon RedShift till Azure Synapse Analytics med Unload, mellanlagrad kopiering och PolyBase**

För det här exemplet använder kopierings aktiviteten data från Amazon RedShift till Amazon S3 enligt konfigurationen i "redshiftUnloadSettings". Kopiera sedan data från Amazon S3 till Azure Blob enligt vad som anges i "stagingSettings", slutligen använder PolyBase för att läsa in data i Azure Synapse Analytics. Alla tillfälliga format hanteras av kopierings aktiviteten på rätt sätt.

![RedShift till Azure Synapse Analytics Copy-arbetsflöde](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Data typs mappning för Amazon RedShift

När du kopierar data från Amazon RedShift används följande mappningar från Amazon RedShift-datatyper för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Data typen Amazon RedShift | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLESKT |Sträng |
| CHAR |Sträng |
| DATE |DateTime |
| DECIMAL |Decimal |
| DUBBEL PRECISION |Double |
| INTEGER |Int32 |
| REAL |Enkel |
| SMALLINT |Int16 |
| TEXT |Sträng |
| TIMESTAMP |DateTime |
| VARCHAR |Sträng |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
