---
title: Kopiera och transformera data i snö
description: Lär dig hur du kopierar och transformerar data i snö med Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/16/2021
ms.openlocfilehash: c18a48f8e72c28fd39f839566b18528806e7245d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561608"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Kopiera och transformera data i snö genom att använda Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till snö flingor och använda data flöden för att transformera data i snö. Mer information om Data Factory finns i [introduktions artikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här snö-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med en [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

För kopierings aktiviteten har den här snö-anslutningen stöd för följande funktioner:

- Kopiera data från snö flingor som använder flingor: s [kopia i [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) -kommandot för att uppnå bästa möjliga prestanda.
- Kopiera data till snö flingor som drar nytta av en kopia av snö flingor [i [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) -kommando för att uppnå bästa möjliga prestanda. Den stöder snö flingor på Azure. 

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory entiteter som är speciella för en snö-koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en snö-länkad tjänst.

| Egenskap         | Beskrivning                                                  | Krävs |
| :--------------- | :----------------------------------------------------------- | :------- |
| typ             | Egenskapen Type måste anges till **snö**.              | Ja      |
| Begär | Anger den information som krävs för att ansluta till snö-instansen. Du kan välja att ange ett lösen ord eller en hel anslutnings sträng i Azure Key Vault. Mer information finns i exemplen under tabellen, samt autentiseringsuppgifterna för [lagring i Azure Key Vault](store-credentials-in-key-vault.md) artikeln.<br><br>Några vanliga inställningar:<br>- **Konto namn:** Det  [fullständiga konto namnet](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) för ditt snö flingor-konto (inklusive ytterligare segment som identifierar region och moln plattform), t. ex. xy12345. öst-US-2. Azure.<br/>- **Användar namn:** Inloggnings namnet för användaren för anslutningen.<br>- **Lösen ord:** Användarens lösen ord.<br>- **Databas:** Standard databasen som ska användas när den är ansluten. Det bör vara en befintlig databas för vilken den angivna rollen har behörighet.<br>- **Lager:** Det virtuella lager som ska användas när det är anslutet. Det bör vara ett befintligt lager som den angivna rollen har behörighet för.<br>- **Roll:** Standard rollen för åtkomst kontroll som används i den snö-sessionen. Den angivna rollen ska vara en befintlig roll som redan har tilldelats till den angivna användaren. Standard rollen är offentlig. | Ja      |
| connectVia       | [Integrerings körningen](concepts-integration-runtime.md) som används för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure integration Runtime. | Inga       |

**Exempel:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Lösen ord i Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

Följande egenskaper stöds för den snö data uppsättningen.

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Data uppsättningens typ-egenskap måste anges till **SnowflakeTable**. | Ja                         |
| schema | Schemats namn. Observera att schema namnet är Skift läges känsligt i ADF. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. Observera att tabell namnet är Skift läges känsligt i ADF. |Nej för källa, Ja för mottagare  |

**Exempel:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av snö källa och mottagare.

### <a name="snowflake-as-the-source"></a>Snö flingor som källa

Snö-anslutningsprogrammet använder snö flingor [i [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) -kommandot för att uppnå bästa möjliga prestanda.

Om Sink-datalagret och-formatet stöds internt av den snö KOPIERINGs kommandot kan du använda kopierings aktiviteten för att kopiera från snö till mottagare direkt. Mer information finns i [direkt kopiering från snö flingor](#direct-copy-from-snowflake). Annars använder du inbyggd [mellanlagrad kopia från snö flingor](#staged-copy-from-snowflake).

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från snö flingor.

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Typ egenskapen för kopierings aktivitets källan måste anges till **SnowflakeSource**. | Ja      |
| DocumentDB          | Anger SQL-frågan för att läsa data från snö. Om namnet på schemat, tabellen och kolumnerna innehåller gemener, så citera objekt identifieraren i frågan t. ex. `select * from "schema"."myTable"` .<br>Det finns inte stöd för att köra den lagrade proceduren. | Inga       |
| exportSettings | Avancerade inställningar som används för att hämta data från snö flingor. Du kan konfigurera de som stöds av kommandot Kopiera till som Data Factory skickas när du anropar instruktionen. | Inga       |
| ***Under `exportSettings` :*** |  |  |
| typ | Typ av export kommando, anges till **SnowflakeExportCopyCommand**. | Ja |
| additionalCopyOptions | Ytterligare kopierings alternativ, som är en ord lista med nyckel/värde-par. Exempel: MAX_FILE_SIZE, Skriv över. Mer information finns i [alternativ för snö kopiering](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Inga |
| additionalFormatOptions | Ytterligare alternativ för fil format som ges för att kopiera kommando som en ord lista med nyckel/värde-par. Exempel: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Mer information finns i [alternativ för snö format typ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Inga |

#### <a name="direct-copy-from-snowflake"></a>Direkt kopiering från snö

Om ditt data lager och format för din mottagare uppfyller de kriterier som beskrivs i det här avsnittet kan du använda kopierings aktiviteten för att kopiera från snö till mottagare direkt. Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om följande villkor inte är uppfyllda:

- Den **länkade mottagar tjänsten** är [**Azure Blob Storage**](connector-azure-blob-storage.md) med autentisering för **signatur för delad åtkomst** . Om du vill kopiera data direkt till Azure Data Lake Storage Gen2 i följande format, kan du skapa en länkad Azure blob-tjänst med SAS-autentisering mot ditt ADLS Gen2-konto för att undvika att använda [mellanlagrad kopia från snö flingor](#staged-copy-from-snowflake).

- **Data formatet Sink** är av **Parquet**, **avgränsad text** eller **JSON** med följande konfigurationer:

    - För **Parquet** -format är komprimerings-codecen **ingen**, **fästfunktionen** eller **LZO**.
    - För **avgränsat text** format:
        - `rowDelimiter` är **\r\n** eller valfritt enskilt.
        - `compression` kan vara **Ingen komprimering**, **gzip**, **bzip2** eller **DEFLATE**.
        - `encodingName` är kvar som standard eller anges till **UTF-8**.
        - `quoteChar` är **dubbelt citat** tecken, **enkelt citat** tecken eller en **tom sträng** (inget citat tecken).
    - För **JSON** -format stöder direkt kopiering endast det fall som käll tabellen för snö flingor eller frågeresultatet bara har en kolumn och data typen för den här kolumnen är **variant**, **objekt** eller **matris**.
        - `compression` kan vara **Ingen komprimering**, **gzip**, **bzip2** eller **DEFLATE**.
        - `encodingName` är kvar som standard eller anges till **UTF-8**.
        - `filePattern` i kopierings aktivitetens mottagare lämnas som standard eller anges till **setOfObjects**.
- I kopierings aktivitets källan `additionalColumns` har inte angetts.
- Ingen kolumn mappning har angetts.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Mellanlagrad kopiering från snö flingor

Om ditt data lager eller format för din mottagare inte är internt kompatibelt med kommandot snö kopiering, som vi nämnt i det sista avsnittet, aktiverar du den inbyggda mellanlagrade kopian med en tillfällig Azure Blob Storage-instans. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Data Factory exporterar data från snö till mellanlagring, kopierar sedan data till Sink och rensar slutligen tillfälliga data från mellanlagringen. Se [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy) för information om hur du kopierar data med hjälp av mellanlagring.

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-kontot som tillfällig mellanlagring. Ange sedan `enableStaging` egenskaperna och `stagingSettings` i kopierings aktiviteten.

> [!NOTE]
> Den länkade Azure Blob Storage-tjänsten måste använda autentisering för delad åtkomst-signatur, enligt vad som krävs av kommandot snö kopiering. 

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Snö flingor som mottagare

Snö-anslutningsprogrammet använder snö flingor [i [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) -kommando för att uppnå bästa möjliga prestanda. Det stöder skrivning av data till snö flingor på Azure.

Om käll data lagret och formatet stöds internt av en snö COPY-kommando, kan du använda kopierings aktiviteten för att kopiera från källan till snö. Mer information finns i [direkt kopiering till snö flingor](#direct-copy-to-snowflake). Annars kan du använda inbyggd [mellanlagrad kopia till snö flingor](#staged-copy-to-snowflake).

Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** för att kopiera data till snö flingor.

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Egenskapen Type för kopierings aktivitetens Sink, anges till **SnowflakeSink**. | Ja                                           |
| preCopyScript     | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till snö flingor i varje körning. Använd den här egenskapen för att rensa de förinstallerade data. | Inga                                            |
| importSettings | Avancerade inställningar som används för att skriva data till snö flingor. Du kan konfigurera de som stöds av kommandot Kopiera till som Data Factory skickas när du anropar instruktionen. | Inga |
| ***Under `importSettings` :*** |                                                              |  |
| typ | Typ av import kommando, anges till **SnowflakeImportCopyCommand**. | Ja |
| additionalCopyOptions | Ytterligare kopierings alternativ, som är en ord lista med nyckel/värde-par. Exempel: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Mer information finns i [alternativ för snö kopiering](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Inga |
| additionalFormatOptions | Ytterligare fil format alternativ som ges till kommandot COPY, som en ord lista med nyckel/värde-par. Exempel: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Mer information finns i [alternativ för snö format typ](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Inga |

#### <a name="direct-copy-to-snowflake"></a>Direkt kopiering till snö flingor

Om käll data lagret och formatet uppfyller de kriterier som beskrivs i det här avsnittet kan du använda kopierings aktiviteten för att kopiera från källan till snö. Azure Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om följande villkor inte är uppfyllda:

- Den **länkade käll tjänsten** är [**Azure Blob Storage**](connector-azure-blob-storage.md) med autentisering med **signatur för delad åtkomst** . Om du vill kopiera data direkt från Azure Data Lake Storage Gen2 i följande format, kan du skapa en länkad Azure blob-tjänst med SAS-autentisering mot ditt ADLS Gen2-konto för att undvika att använda  [mellanlagrad kopiering till snö flingor](#staged-copy-to-snowflake).

- **Käll data formatet** är **Parquet**, **avgränsad text** eller **JSON** med följande konfigurationer:

    - För **Parquet** -format är komprimerings-codecen **ingen** eller **Fäst**.

    - För **avgränsat text** format:
        - `rowDelimiter` är **\r\n** eller valfritt enskilt. Om rad avgränsaren inte är "\r\n" `firstRowAsHeader` måste vara **false** och `skipLineCount` har inte angetts.
        - `compression` kan vara **Ingen komprimering**, **gzip**, **bzip2** eller **DEFLATE**.
        - `encodingName` är kvar som standard eller inställd på "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "Windows-1253", "WINDOWS-1254".
        - `quoteChar` är **dubbelt citat** tecken, **enkelt citat** tecken eller en **tom sträng** (inget citat tecken).
    - För **JSON** -format stöder direkt kopiering endast det fall som bara innehåller en kolumn och data typen för den här kolumnen är **variant**, **objekt** eller **matris**.
        - `compression` kan vara **Ingen komprimering**, **gzip**, **bzip2** eller **DEFLATE**.
        - `encodingName` är kvar som standard eller anges till **UTF-8**.
        - Ingen kolumn mappning har angetts.

- I kopierings aktivitets källan: 

   -  `additionalColumns` har inte angetts.
   - Om din källa är en mapp `recursive` anges till sant.
   - `prefix`, `modifiedDateTimeStart` ,, `modifiedDateTimeEnd` och `enablePartitionDiscovery` har inte angetts.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Mellanlagrad kopiering till snö flingor

Om käll data lagret eller formatet inte är internt kompatibelt med kommandot snö kopiering, som vi nämnt i det sista avsnittet, aktiverar du den inbyggda mellanlagrade kopian med en tillfällig Azure Blob Storage-instans. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Data Factory automatiskt konvertera data så att de uppfyller kraven för data format för snö flingor. Sedan anropas KOPIERINGs kommandot för att läsa in data till snö flingor. Slutligen rensar den temporära data från blob-lagringen. Mer information om hur du kopierar data med mellanlagring finns i [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy) .

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-kontot som tillfällig mellanlagring. Ange sedan `enableStaging` egenskaperna och `stagingSettings` i kopierings aktiviteten.

> [!NOTE]
> Den mellanlagrade Azure Blob Storage-tjänsten måste använda autentisering med signaturer för delad åtkomst som krävs av den snö COPY kommandot.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa från och skriva till tabeller i snö. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden. Du kan välja att använda en snö data uppsättning eller en [infogad data uppsättning](data-flow-source.md#inline-datasets) som källa och mottagar typ.

### <a name="source-transformation"></a>Käll omvandling

I tabellen nedan visas de egenskaper som stöds av snö Source. Du kan redigera dessa egenskaper på fliken **käll alternativ** . Anslutningen använder vinter [intern data överföring](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer).

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabell | Om du väljer tabell som indata hämtar data flödet alla data från tabellen som anges i den snö data uppsättningen eller i käll alternativen när du använder en infogad data uppsättning. | Inga | Sträng | *(endast för infogad data uppsättning)*<br>tableName<br>schemaName |
| Fråga | Om du väljer fråga som indata anger du en fråga för att hämta data från snö. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen.<br>Om namnet på schemat, tabellen och kolumnerna innehåller gemener, så citera objekt identifieraren i frågan t. ex. `select * from "schema"."myTable"` . | Inga | Sträng | DocumentDB |

#### <a name="snowflake-source-script-examples"></a>Skript exempel för snö-källa

När du använder data typen snö data uppsättning som källtyp är det associerade data flödes skriptet:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Om du använder en infogad data uppsättning är det associerade data flödes skriptet:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Omvandling av mottagare

I tabellen nedan visas de egenskaper som stöds av snö handfat. Du kan redigera dessa egenskaper på fliken **Inställningar** . När du använder en infogad data uppsättning visas ytterligare inställningar, som är samma som de egenskaper som beskrivs i avsnittet [Egenskaper för data mängd](#dataset-properties) . Anslutningen använder vinter [intern data överföring](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer).

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Uppdaterings metod | Ange vilka åtgärder som tillåts på ditt snö flingor-mål.<br>Om du vill uppdatera, upsert eller ta bort rader krävs en [Alter Row-omvandling](data-flow-alter-row.md) för att tagga rader för dessa åtgärder. | Ja | `true` eller `false` | bort <br/>infognings bara <br/>uppdaterings bara <br/>upsertable |
| Nyckel kolumner | För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras. | Inga | Matris | keys |
| Tabell åtgärd | Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.<br>- **Ingen**: ingen åtgärd utförs i tabellen.<br>- **Återskapa**: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.<br>- **Trunkera**: alla rader från mål tabellen tas bort. | Inga | `true` eller `false` | återskapa<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>Skript exempel för snö handfat

När du använder snö data uppsättning som mottagar typ är det associerade data flödes skriptet:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Om du använder en infogad data uppsättning är det associerade data flödes skriptet:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns i [Lookup Activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare efter kopierings aktivitet i Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
