---
title: Kopiera data från Hive med Azure Data Factory
description: Lär dig hur du kopierar data från Hive till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367035"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Kopiera och transformera data från Hive med Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Hive. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Hive-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Hive till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning, och du behöver därför inte installera någon driv rutin manuellt med hjälp av den här anslutningen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Hive-anslutningsprogram.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Hive-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Hive** | Ja |
| värd | IP-adressen eller värd namnet för registrerings data filen, avgränsat med ";" för flera värdar (endast när serviceDiscoveryMode har Aktiver ATS).  | Ja |
| port | TCP-porten som Hive-servern använder för att lyssna efter klient anslutningar. Om du ansluter till Azure HDInsights anger du port som 443. | Ja |
| serverType | Typ av Hive-Server. <br/>Tillåtna värden är: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Inga |
| thriftTransportProtocol | Transport protokollet som ska användas i Thrift-skiktet. <br/>Tillåtna värden är: **Binary**, **sasl**, **http** | Inga |
| authenticationType | Autentiseringsmetoden som används för att få åtkomst till Hive-servern. <br/>Tillåtna värden är: **Anonym**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. Kerberos-autentisering stöds inte nu. | Ja |
| serviceDiscoveryMode | sant om du vill ange att använda ZooKeeper-tjänsten, falskt.  | Inga |
| zooKeeperNameSpace | Namn området på ZooKeeper under vilka Hive-Server 2 noder läggs till.  | Inga |
| useNativeQuery | Anger om driv rutinen använder interna HiveQL-frågor eller konverterar dem till ett motsvarande formulär i HiveQL.  | Inga |
| användarnamn | Det användar namn som du använder för att komma åt Hive-servern.  | Inga |
| password | Lösen ordet som motsvarar användaren. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| httpPath | Den partiella URL som motsvarar Hive-servern.  | Inga |
| enableSsl | Anger om anslutningarna till servern är krypterade med TLS. Standardvärdet är false.  | Inga |
| trustedCertPath | Den fullständiga sökvägen till. pem-filen som innehåller certifikat från betrodda certifikat utfärdare för att verifiera servern vid anslutning via TLS. Den här egenskapen kan bara anges när du använder TLS på IR med egen värd. Standardvärdet är den cacerts. PEM-fil som installeras med IR.  | Inga |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från systemets betrodda lager eller från en angiven PEM-fil. Standardvärdet är false.  | Inga |
| allowHostNameCNMismatch | Anger om ett CA-utfärdat TLS/SSL-certifikat namn ska matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är false.  | Inga |
| allowSelfSignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |
| storageReference | En referens till den länkade tjänsten för det lagrings konto som används för att mellanlagra data i mappnings data flödet. Detta krävs endast när du använder den länkade Hive-tjänsten i mappnings data flödet | Inga |

**Exempel:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-datauppsättningen.

Om du vill kopiera data från Hive anger du egenskapen type för data uppsättningen till **HiveObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **HiveObject** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen inklusive schema del. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-källan.

### <a name="hivesource-as-source"></a>HiveSource som källa

Om du vill kopiera data från Hive anger du käll typen i kopierings aktiviteten till **HiveSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **HiveSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Hive-anslutningen stöds som en [inbäddad data uppsättnings](data-flow-source.md#inline-datasets) källa i mappnings data flöden. Läs med en fråga eller direkt från en Hive-tabell i HDInsight. Hive-data samlas in i ett lagrings konto som Parquet-filer innan de blir omvandlade som en del av ett data flöde. 

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en Hive-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Lagringsplats | Arkivet måste vara `hive` | ja |  `hive` | butik | 
| Format | Om du läser från en tabell eller fråga | ja | `table` eller `query` | format |
| Schemanamn | Om du läser från en tabell används schemat för käll tabellen |  Ja, om formatet är `table` | Sträng | schemaName |
| Tabellnamn | Om du läser från en tabell används tabell namnet |   Ja, om formatet är `table` | Sträng | tableName |
| Fråga | Om format är `query` , käll frågan på den länkade Hive-tjänsten | Ja, om formatet är `query` | Sträng | DocumentDB |
| Mellanlagrad | Hive-tabellen kommer alltid att mellanlagras. | ja | `true` | mellanlagrad |
| Lagrings behållare | Lagrings behållare som används för att mellanlagra data innan den läses från Hive eller skrivs till Hive. Hive-klustret måste ha åtkomst till den här behållaren. | ja | Sträng | storageContainer |
| Mellanlagringsdatabas | Schemat/databasen där det användar konto som anges i den länkade tjänsten har åtkomst till. Den används för att skapa externa tabeller under mellanlagringen och tas bort efteråt | nej | `true` eller `false` | stagingDatabaseName |
| SQL-skript | SQL-kod som ska köras i Hive-tabellen innan data läses | nej | Sträng | preSQLs |

#### <a name="source-example"></a>Käll exempel

Nedan visas ett exempel på en konfiguration av Hive-Källa:

![Exempel på Hive-källa](media/data-flow/hive-source.png "[Exempel på Hive-källa")

Dessa inställningar översätts till följande data flödes skript:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Kända begränsningar

* Komplexa typer som matriser, kartor, structs och unioner stöds inte för läsning. 
* Hive-koppling stöder endast Hive-tabeller i Azure HDInsight med version 4,0 eller senare (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
