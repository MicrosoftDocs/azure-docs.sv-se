---
title: Kopiera data från Netezza med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Netezza till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374362"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiera data från Netezza med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från Netezza. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

>[!TIP]
>För data migrations scenario från Netezza till Azure kan du läsa mer i [använda Azure Data Factory för att migrera data från den lokala Netezza-servern till Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Netezza-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)


Du kan kopiera data från Netezza till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Netezza-anslutaren stöder parallell kopiering från källan. Mer information finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning. Du behöver inte installera någon driv rutin manuellt för att använda den här anslutningen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline som använder en kopierings aktivitet med hjälp av .NET SDK, python SDK, Azure PowerShell, REST API eller en Azure Resource Manager mall. I [självstudien om kopierings aktiviteten](quickstart-create-data-factory-dot-net.md) finns stegvisa anvisningar om hur du skapar en pipeline med en kopierings aktivitet.

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Netezza-anslutningen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Netezza:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **Netezza**. | Ja |
| Begär | En ODBC-anslutningssträng för att ansluta till Netezza. <br/>Du kan också ställa in lösen ord i Azure Key Vault och hämta `pwd` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

En typisk anslutnings sträng är `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . I följande tabell beskrivs fler egenskaper som du kan ange:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| SecurityLevel | Den säkerhets nivå som driv rutinen använder för att ansluta till data lagret. Driv rutinen stöder SSL-anslutningar med enkelriktad autentisering med hjälp av SSL version 3. <br>Exempel: `SecurityLevel=preferredSecured`. De värden som stöds är:<br/>- **Endast oskyddade** (**onlyUnSecured**): driv rutinen använder inte SSL.<br/>- **Föredra oskyddad (preferredUnSecured) (standard)**: om servern tillhandahåller ett alternativ använder driv rutinen inte SSL. <br/>- **Preferred Secure (preferredSecured)**: om servern tillhandahåller ett alternativ använder driv rutinen SSL. <br/>- **Endast skyddat (onlySecured)**: driv rutinen ansluter inte om en SSL-anslutning är tillgänglig. | Inga |
| CaCertFile | Den fullständiga sökvägen till det SSL-certifikat som används av servern. Exempel: `CaCertFile=<cert path>;`| Ja, om SSL är aktiverat |

**Exempel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Det här avsnittet innehåller en lista över egenskaper som Netezza-datauppsättningen stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

Om du vill kopiera data från Netezza anger du egenskapen **Type** för data uppsättningen till **NetezzaTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **NetezzaTable** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som Netezza-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza som källa

>[!TIP]
>Om du vill läsa in data från Netezza effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från Netezza](#parallel-copy-from-netezza) -avsnittet.

Om du vill kopiera data från Netezza anger du **käll** typen i kopierings aktivitet till **NetezzaSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **NetezzaSource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om "tableName" i data uppsättningen har angetts) |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Netezza. <br>Tillåtna värden är: **ingen** (standard), **DataSlice** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), styrs graden av parallellitet för data från en Netezza-databas genom [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) att ställa in på kopierings aktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när partition alternativet inte är `None` . | Inga |
| partitionColumnName | Ange namnet på den käll kolumn **i Integer-typ** som ska användas av intervall partitionering för parallell kopiering. Om den inte anges identifieras primär nyckeln för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionColumnName` i WHERE-satsen. Se exempel i [parallell kopiering från avsnittet Netezza](#parallel-copy-from-netezza) . | Inga |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när partition alternativet är `DynamicRange` . Om du använder Query för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) . | Inga |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) . | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Parallell kopiering från Netezza

Data Factory Netezza-anslutningsprogrammet tillhandahåller inbyggd data partitionering för att kopiera data från Netezza parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-netezza/connector-netezza-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Netezza-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från Netezza-databasen.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din Netezza-databas. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                                   | **Partitions alternativ**: data sektor. <br><br/>Under körningen partitionerar Data Factory automatiskt data baserat på de [inbyggda data sektorerna i Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partitions alternativ**: data sektor.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>Under körningen ersätts Data Factory `?AdfPartitionCount` (med parallell kopians nummer som angetts för kopierings aktiviteten) och `?AdfDataSliceCondition` med data sektorns partition logik och skickas till Netezza. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltals kolumn med jämnt distribuerat värde för intervall partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partition nedre gränser**: Ange om du vill filtrera mot kolumnen partition för att endast hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Netezza. <br>Om t. ex. partitionens kolumn "ID" har angetts med den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med 4 partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

**Exempel: fråga med data sektor partition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
