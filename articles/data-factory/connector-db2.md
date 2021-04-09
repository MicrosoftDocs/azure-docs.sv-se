---
title: Kopiera data från DB2 med Azure Data Factory
description: Lär dig hur du kopierar data från DB2 till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 642f12386a7695e026eb0c30016acf6f53fc9e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381128"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiera data från DB2 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuell version](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en DB2-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här DB2 Database Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från DB2-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här DB2-anslutaren följande IBM DB2-plattformar och versioner med Distributed Relations databas arkitektur (DRDA) SQL Access Manager (SQLAM) version 9, 10 och 11.  Den använder DDM/DRDA-protokollet.

* IBM DB2 för z/OS 12,1
* IBM DB2 för z/OS 11,1
* IBM DB2 för z/OS 10,1
* IBM DB2 för i 7,3
* IBM DB2 för i 7,2
* IBM DB2 för i 7,1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10,5
* IBM DB2 för LUW 10,1

>[!TIP]
>DB2 Connector är byggd ovanpå Microsoft OLE DB Provider för DB2. Information om hur du felsöker DB2 Connector-fel finns i [felkoder för DataProvider](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime tillhandahåller en inbyggd DB2-drivrutin, och du behöver därför inte installera någon driv rutin manuellt när du kopierar data från DB2.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för DB2 Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för DB2-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **DB2** | Ja |
| Begär | Ange information som krävs för att ansluta till DB2-instansen.<br/> Du kan också ställa in lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

Typiska egenskaper i anslutnings strängen:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| server |Namnet på DB2-servern. Du kan ange port numret som följer Server namnet avgränsat med kolon, t. ex. `server:port` .<br>DB2-anslutaren använder DDM/DRDA-protokollet och använder som standard port 50000 om inget värde anges. Porten som din speciella DB2-databas använder kan vara olika beroende på versionen och dina inställningar, t. ex. för DB2-LUW är standard porten 50000, för AS400 är standard porten 446 eller 448 när TLS är aktiverat. Se följande DB2-dokument på hur porten konfigureras vanligt vis: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 ISERIES](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm)och [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Ja |
| databas |Namnet på DB2-databasen. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen.<br/>Tillåtet värde är: **Basic**. |Ja |
| användarnamn |Ange användar namnet för att ansluta till DB2-databasen. |Ja |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| packageCollection    | Ange under där de nödvändiga paketen skapas automatiskt av ADF när du frågar databasen. Om detta inte anges använder Data Factory {username} som standardvärde. | Inga |
| certificateCommonName | När du använder Secure Sockets Layer (SSL) eller Transport Layer Security kryptering (TLS) måste du ange ett värde för certifikatets egna namn. | Inga |

> [!TIP]
> Om du får ett fel meddelande om att det finns ett fel meddelande `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` , är orsaken till att ett nödvändigt paket inte skapas för användaren. Som standard försöker ADF skapa ett paket under samlingen med namnet som den användare som du använde för att ansluta till DB2. Ange egenskapen för paket samling för att ange under var du vill att ADF ska skapa nödvändiga paket när du frågar databasen.

**Exempel:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
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

Om du använder DB2-länkad tjänst med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

**Föregående nytto last:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-datauppsättningen.

Följande egenskaper stöds för att kopiera data från DB2:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **Db2Table** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-källan.

### <a name="db2-as-source"></a>DB2 som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från DB2:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **Db2Source** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

## <a name="data-type-mapping-for-db2"></a>Data typs mappning för DB2

När du kopierar data från DB2 används följande mappningar från DB2-datatyper för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Typ av DB2-databas | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Binär |Byte [] |
| Blob |Byte [] |
| Char |Sträng |
| CLOB |Sträng |
| Datum |Datumtid |
| DB2DynArray |Sträng |
| DbClob |Sträng |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Infoga |Sträng |
| Integer |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |Sträng |
| LongVarGraphic |Sträng |
| Numerisk |Decimal |
| Verkligen |Enkel |
| SmallInt |Int16 |
| Tid |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte [] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| Xml |Byte [] |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).