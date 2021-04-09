---
title: Kopiera data från och till ODBC-datalager med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från och till ODBC-datalager med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 9b73e10b0ed539879e9a32d3961b6375828cc153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389628"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiera data från och till ODBC-datalager med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [Aktuell version](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till ett ODBC-data lager. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här ODBC-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från ODBC-källa till alla mottagar data lager som stöds, eller kopiera från alla käll data lager som stöds till ODBC-mottagare. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna ODBC-anslutning kopiering av data från/till **ODBC-kompatibla data lager** med **Basic** eller **Anonym** autentisering. En **64-bitars ODBC-drivrutin** krävs. För ODBC-mottagare stöder ADF ODBC version 2,0 standard.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här ODBC-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .
- Installera 64-bitars ODBC-drivrutinen för data lagret på den Integration Runtime datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för ODBC-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för ODBC-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **ODBC** | Ja |
| Begär | Anslutnings strängen exklusive Credential-delen. Du kan ange anslutnings strängen med ett mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , eller använda system-DSN (data källans namn) som du konfigurerade på den integration runtime datorn med `"DSN=<name of the DSN on IR machine>;"` (du behöver fortfarande ange autentiseringsuppgiften i den länkade tjänsten).<br>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) .| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till ODBC-datalagret.<br/>Tillåtna värden är: **Basic** och **Anonymous**. | Ja |
| userName | Ange användar namn om du använder grundläggande autentisering. | Inga |
| password | Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| credential | Delen autentiseringsuppgifter för den anslutnings sträng som anges i drivrutinsspecifika egenskaps värde format. Exempel: `"RefreshToken=<secret refresh token>;"`. Markera det här fältet som en SecureString. | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel 1: använda grundläggande autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Exempel 2: använda anonym autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-datauppsättning.

Följande egenskaper stöds för att kopiera data från/till ODBC-kompatibelt data lager:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **OdbcTable** | Ja |
| tableName | Namnet på tabellen i ODBC-datalagret. | Nej för källa (om "fråga" i aktivitets källan har angetts);<br/>Ja för mottagare |

**Exempel**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-källa.

### <a name="odbc-as-source"></a>ODBC som källa

För att kopiera data från ODBC-kompatibelt data lager, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **OdbcSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

### <a name="odbc-as-sink"></a>ODBC som mottagare

Om du vill kopiera data till ODBC-kompatibelt data lager ställer du in mottagar typen i kopierings aktiviteten till **OdbcSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till: **OdbcSink** | Ja |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br/>Tillåtna värden är: TimeSpan. Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (Standardvärdet är 0 – identifieras automatiskt) |
| preCopyScript |Ange en SQL-fråga för kopierings aktivitet som ska köras innan data skrivs till data lagret i varje körning. Du kan använda den här egenskapen för att rensa förinlästa data. |Inga |

> [!NOTE]
> För "writeBatchSize", om den inte har angetts (identifieras automatiskt), identifierar kopierings aktiviteten först om driv rutinen stöder batch-åtgärder och anger den till 10000 om den gör det, eller så anger du den till 1 om den inte gör det. Om du anger något annat värde än 0, följer kopierings aktiviteten värdet och Miss lyckas vid körning om driv rutinen inte stöder batch-åtgärder.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="troubleshoot-connectivity-issues"></a>Felsöka anslutningsproblem

Använd fliken **diagnostik** i **integration runtime Configuration Manager** för att felsöka anslutnings problem.

1. Starta **Integration Runtime Configuration Manager**.
2. Växla till fliken **diagnostik** .
3. Under avsnittet "Testa anslutning" väljer du **typ** av data lager (länkad tjänst).
4. Ange **anslutnings strängen** som används för att ansluta till data lagret, Välj **autentiseringen** och ange **användar namn**, **lösen ord** och/eller **autentiseringsuppgifter**.
5. Klicka på **Testa anslutning** för att testa anslutningen till data lagret.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
