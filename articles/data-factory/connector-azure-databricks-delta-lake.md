---
title: Kopiera data till och från Azure Databricks delta Lake
description: Lär dig hur du kopierar data till och från Azure Databricks delta sjö genom att använda en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: bdf71276d59dec9a19e29ae7f49cb92a0512c05a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364247"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Kopiera data till och från Azure Databricks delta Lake med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till och från Azure Databricks delta Lake. Den bygger på [kopierings aktiviteten i Azure Data Factory](copy-activity-overview.md) artikel, som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Databricks delta Lake Connector stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med en [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

I allmänhet stöder Azure Data Factory delta sjö med följande funktioner för att uppfylla dina olika behov.

- Kopierings aktiviteten stöder Azure Databricks delta Lake Connector för att kopiera data från alla käll data lager som stöds till Azure Databricks delta Lake-tabell och från delta Lake-tabell till alla mottagar data lager som stöds. Det utnyttjar ditt Databricks-kluster för att utföra data flyttningen. mer information finns i [avsnittet krav](#prerequisites).
- [Mappnings data flödet](concepts-data-flow-overview.md) stöder allmänt [delta-format](format-delta.md) på Azure Storage som källa och mottagare för att läsa och skriva delta-filer för kod fri ETL, och körs på hanterade Azure integration Runtime.
- [Databricks-aktiviteter](transform-data-databricks-notebook.md) har stöd för att dirigera din kodbaserade ETL-eller Machine Learning-arbetsbelastning ovanpå delta Lake.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här Azure Databricks delta Lake Connector måste du konfigurera ett kluster i Azure Databricks.

- Om du vill kopiera data till delta Lake anropar kopierings aktiviteten Azure Databricks kluster för att läsa data från en Azure Storage, vilket är antingen den ursprungliga källan eller ett mellanlagringsområde till där Data Factory i första hand skriver käll data via inbyggd mellanlagrad kopia. Läs mer från [delta Lake som mottagare](#delta-lake-as-sink).
- Om du vill kopiera data från delta Lake anropar kopierings aktiviteten Azure Databricks kluster för att skriva data till en Azure Storage, som antingen är din ursprungliga mottagare eller ett mellanlagringsområde från där Data Factory fortsätter att skriva data till den slutliga mottagaren via inbyggd mellanlagrad kopia. Läs mer från [delta Lake som källa](#delta-lake-as-source).

Databricks-klustret måste ha åtkomst till Azure Blob eller Azure Data Lake Storage Gen2 konto, både lagrings behållare/fil system som används för källa/mottagare/mellanlagring och det behållare/fil system där du vill skriva de delta Lake-tabeller.

- Om du vill använda **Azure Data Lake Storage Gen2** kan du konfigurera **tjänstens huvud namn** eller **lagrings kontots åtkomst nyckel** i Databricks-klustret som en del av Apache Spark-konfigurationen. Följ stegen i [åtkomst direkt med tjänstens huvud namn](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) eller [åtkomst direkt med hjälp av lagrings kontots åtkomst nyckel](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Om du vill använda **Azure Blob Storage** kan du konfigurera en **åtkomst nyckel för lagrings kontot** eller **SAS-token** i Databricks-klustret som en del av Apache Spark-konfigurationen. Följ stegen i [Access Azure Blob Storage med RDD-API: et](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Om klustret som du har konfigurerat har avbrutits startar Data Factory automatiskt vid kopiering av aktivitets körningen. Om du skapar pipeline med Data Factory redigerings gränssnitt för åtgärder som till exempel för data för hands version måste du ha ett aktivt kluster, Data Factory inte starta klustret åt dig.

#### <a name="specify-the-cluster-configuration"></a>Ange kluster konfigurationen

1. I list rutan **kluster läge** väljer du **standard**.

2. I list rutan **Databricks runtime version** väljer du en Databricks runtime-version.

3. Aktivera [automatisk optimering](/azure/databricks/delta/optimizations/auto-optimize) genom att lägga till följande egenskaper i [Spark-konfigurationen](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Konfigurera ditt kluster beroende på dina behov av integrering och skalning.

Information om kluster konfiguration finns i [Konfigurera kluster](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som definierar Data Factory entiteter som är speciella för en Azure Databricks delta Lake-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en länkad Azure Databricks delta Lake-tjänst.

| Egenskap    | Beskrivning                                                  | Krävs |
| :---------- | :----------------------------------------------------------- | :------- |
| typ        | Egenskapen Type måste anges till **AzureDatabricksDeltaLake**. | Ja      |
| domän      | Ange Azure Databricks arbets ytans URL, t `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . ex.. |          |
| clusterId   | Ange kluster-ID för ett befintligt kluster. Det bör vara ett interaktivt kluster som redan skapats. <br>Du hittar kluster-ID: t för ett interaktivt kluster på Databricks-arbetsytan – > kluster – > interaktivt kluster namn-> konfigurations > taggar. [Läs mer](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Åtkomsttoken krävs för att Data Factory ska kunna autentisera till Azure Databricks. Åtkomsttoken måste genereras från arbets ytan databricks. Mer detaljerade anvisningar om hur du hittar åtkomsttoken finns [här](/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | [Integrerings körningen](concepts-integration-runtime.md) som används för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure integration Runtime. | Inga       |

**Exempel:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

Följande egenskaper stöds för Azure Databricks delta Lake-datauppsättningen.

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Data uppsättningens typ-egenskap måste anges till **AzureDatabricksDeltaLakeDataset**. | Ja                         |
| databas | Namnet på databasen. |Nej för källa, Ja för mottagare  |
| tabell | Namn på delta tabellen. |Nej för källa, Ja för mottagare  |

**Exempel:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Databricks delta Lake-källa och mottagare.

### <a name="delta-lake-as-source"></a>Delta Lake som källa

Om du vill kopiera data från Azure Databricks delta Lake stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** .

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Typ egenskapen för kopierings aktivitets källan måste anges till **AzureDatabricksDeltaLakeSource**. | Ja      |
| DocumentDB          | Ange SQL-frågan för att läsa data. För tids resans kontroll följer du mönstret nedan:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | Inga       |
| exportSettings | Avancerade inställningar som används för att hämta data från delta tabell. | Inga       |
| ***Under `exportSettings` :*** |  |  |
| typ | Typ av export kommando, anges till **AzureDatabricksDeltaLakeExportCommand**. | Ja |
| dateFormat | Formatera datum typ till sträng med datum format. Anpassade datum format följer format vid [datetime-mönster](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Om inget värde anges används standardvärdet `yyyy-MM-dd` . | Inga |
| timestampFormat | Formatera timestamp-typ till en sträng med ett tidsstämpel-format. Anpassade datum format följer format vid [datetime-mönster](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Om inget värde anges används standardvärdet `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Inga |

#### <a name="direct-copy-from-delta-lake"></a>Direkt kopiering från delta Lake

Om ditt data lager och format för din mottagare uppfyller de kriterier som beskrivs i det här avsnittet, kan du använda kopierings aktiviteten för att kopiera från Azure Databricks delta tabellen direkt till mottagaren. Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om följande villkor inte är uppfyllda:

- Den **länkade mottagar tjänsten** är [Azure Blob Storage](connector-azure-blob-storage.md) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Autentiseringsuppgifterna för kontot bör vara förkonfigurerade i Azure Databricks kluster konfiguration, Läs mer från [krav](#prerequisites).

- **Data formatet Sink** är av **Parquet**, **avgränsad text** eller **Avro** med följande konfigurationer och pekar på en mapp i stället för filen.

    - För **Parquet** -format är komprimerings-codecen **ingen**, **fästfunktionen** eller **gzip**.
    - För **avgränsat text** format:
        - `rowDelimiter` är ett valfritt enskilt Character.
        - `compression` kan vara **ingen**, **bzip2**, **gzip**.
        - `encodingName` UTF-7 stöds inte.
    - För **Avro** -format är komprimerings-codecen **ingen**, **deflat** eller **Fäst**.

- I kopierings aktivitets källan `additionalColumns` har inte angetts.
- Om du kopierar data till avgränsad text måste `fileExtension` du vara ". csv" i kopierings aktivitets mottagare.
- I kopierings aktivitets mappningen är typ konvertering inte aktiverat.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Mellanlagrad kopiering från delta Lake

När ditt data lager eller format för din mottagare inte matchar kriterierna för direkt kopiering, som vi nämnt i det sista avsnittet, aktiverar du den inbyggda mellanlagrade kopian med en tillfällig Azure Storage-instans. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Data Factory exporterar data från Azure Databricks delta Lake till mellanlagringsplatsen, kopierar sedan data till Sink och rensar slutligen tillfälliga data från mellanlagringen. Se [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy) för information om hur du kopierar data med hjälp av mellanlagring.

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2 länkad tjänst](connector-azure-data-lake-storage.md#linked-service-properties) som refererar till lagrings kontot som tillfällig mellanlagring. Ange sedan `enableStaging` egenskaperna och `stagingSettings` i kopierings aktiviteten.

>[!NOTE]
>Autentiseringsuppgifterna för mellanlagrings kontot bör vara förkonfigurerade i Azure Databricks kluster konfiguration, Läs mer från [krav](#prerequisites).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake som mottagare

Om du vill kopiera data till Azure Databricks delta Lake stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap      | Beskrivning                                                  | Krävs |
| :------------ | :----------------------------------------------------------- | :------- |
| typ          | Egenskapen Type för kopierings aktivitetens Sink, anges till **AzureDatabricksDeltaLakeSink**. | Ja      |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till Databricks delta-tabellen i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data eller lägga till en Trunkerande tabell eller vakuum-instruktion. | Inga       |
| importSettings | Avancerade inställningar som används för att skriva data till en delta tabell. | Inga |
| ***Under `importSettings` :*** |                                                              |  |
| typ | Typ av import kommando, anges till **AzureDatabricksDeltaLakeImportCommand**. | Ja |
| dateFormat | Formatera strängen till Date-typ med ett datum format. Anpassade datum format följer format vid [datetime-mönster](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Om inget värde anges används standardvärdet `yyyy-MM-dd` . | Inga |
| timestampFormat | Formatera sträng till timestamp-typ med ett tidsstämpel-format. Anpassade datum format följer format vid [datetime-mönster](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Om inget värde anges används standardvärdet `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Inga |

#### <a name="direct-copy-to-delta-lake"></a>Direkt kopiering till delta Lake

Om käll data lagret och formatet uppfyller de kriterier som beskrivs i det här avsnittet kan du använda kopierings aktiviteten för att kopiera från källan direkt till Azure Databricks delta Lake. Azure Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om följande villkor inte är uppfyllda:

- Den **länkade käll tjänsten** är [Azure Blob Storage](connector-azure-blob-storage.md) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Autentiseringsuppgifterna för kontot bör vara förkonfigurerade i Azure Databricks kluster konfiguration, Läs mer från [krav](#prerequisites).

- **Käll data formatet** är av **Parquet**, **avgränsad text** eller **Avro** med följande konfigurationer och pekar på en mapp i stället för filen.

    - För **Parquet** -format är komprimerings-codecen **ingen**, **fästfunktionen** eller **gzip**.
    - För **avgränsat text** format:
        - `rowDelimiter` är standard eller valfritt enskilt.
        - `compression` kan vara **ingen**, **bzip2**, **gzip**.
        - `encodingName` UTF-7 stöds inte.
    - För **Avro** -format är komprimerings-codecen **ingen**, **deflat** eller **Fäst**.

- I kopierings aktivitets källan: 

    - `wildcardFileName` innehåller bara jokertecken `*` men inte `?` , och `wildcardFolderName` har inte angetts.
    - `prefix`, `modifiedDateTimeStart` ,, `modifiedDateTimeEnd` och `enablePartitionDiscovery` har inte angetts.
    - `additionalColumns` har inte angetts.

- I kopierings aktivitets mappningen är typ konvertering inte aktiverat.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Mellanlagrad kopiering till delta Lake

Om käll data lagret eller formatet inte matchar kriterierna för direkt kopiering, som vi nämnt i det sista avsnittet, aktiverar du den inbyggda mellanlagrade kopian med en tillfällig Azure Storage-instans. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Data Factory automatiskt konvertera data så att de uppfyller data format kraven i mellanlagringen och läser sedan in data i delta Lake därifrån. Slutligen rensar den temporära data från lagrings platsen. Mer information om hur du kopierar data med mellanlagring finns i [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy) .

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2 länkad tjänst](connector-azure-data-lake-storage.md#linked-service-properties) som refererar till lagrings kontot som tillfällig mellanlagring. Ange sedan `enableStaging` egenskaperna och `stagingSettings` i kopierings aktiviteten.

>[!NOTE]
>Autentiseringsuppgifterna för mellanlagrings kontot bör vara förkonfigurerade i Azure Databricks kluster konfiguration, Läs mer från [krav](#prerequisites).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Övervakning

Azure Data Factory ger samma [kopierings aktivitets övervakning](copy-activity-monitoring.md) som andra anslutningar. Eftersom inläsning av data från/till delta Lake körs i Azure Databricks-klustret kan du dessutom ytterligare [Visa detaljerade kluster loggar](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) och [övervaka prestanda](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns i [Lookup Activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare efter kopierings aktivitet i Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
