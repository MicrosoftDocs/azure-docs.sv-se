---
title: Sök aktivitet i Azure Data Factory
description: Lär dig hur du använder lookup-aktivitet för att leta upp ett värde från en extern källa. Det går att referera till utdata ytterligare genom att utföra aktiviteter.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.openlocfilehash: 4b2fb49899b6a676520fe0912dd122dd72cce023
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712920"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Sök aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Söknings aktiviteten kan hämta en data uppsättning från någon av de Azure Data Factory data källor som stöds. Du kan använda den för att dynamiskt avgöra vilka objekt som ska användas i en efterföljande aktivitet, i stället för att hårdkoda objekt namnet. Några exempel på objekt är filer och tabeller.

Lookup-aktiviteten läser och returnerar innehållet i en konfigurations fil eller tabell. Det returnerar också resultatet av att köra en fråga eller lagrad procedur. Utdata kan vara ett singleton-värde eller en matris med attribut som kan förbrukas i en efterföljande kopierings-, omvandlings-eller kontroll flödes aktivitet som till exempel aktiviteter.

## <a name="supported-capabilities"></a>Funktioner som stöds

. Tänk på följande:

- Sök aktiviteten kan returnera upp till **5000 rader**. om resultat uppsättningen innehåller fler poster returneras de första 5000 raderna.
- Utdata för uppslags aktivitet har stöd för upp till **4 MB** och det går inte att utföra aktiviteten om storleken överskrider gränsen. 
- Den längsta varaktigheten för söknings aktiviteten före timeout är **24 timmar**.
- När du använder en fråga eller lagrad procedur för att söka efter data, se till att returnera en och exakt en resultat uppsättning. Annars Miss lyckas söknings aktiviteten.

Följande data källor stöds för lookup-aktivitet. 

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Typ egenskaper

Name | Beskrivning | Typ | Obligatoriskt?
---- | ----------- | ---- | --------
data uppsättning | Tillhandahåller data uppsättnings referensen för sökningen. Hämta information från avsnittet **Egenskaper för data mängd** i varje motsvarande kopplings artikel. | Nyckel/värde-par | Ja
källa | Innehåller data uppsättnings bara käll egenskaper, samma som för kopierings aktivitets källan. Hämta information från avsnittet **Kopiera aktivitets egenskaper** i varje motsvarande kopplings artikel. | Nyckel/värde-par | Ja
firstRowOnly | Anger om bara den första raden eller alla rader ska returneras. | Boolesk | Nej. Standardvärdet är `true`.

> [!NOTE]
> 
> * Käll kolumner med **ByteArray** -typ stöds inte.
> * **Strukturen** stöds inte i data uppsättnings definitioner. För textformat-filer använder du rubrik raden för att ange kolumn namnet.
> * Om din uppslags källa är en JSON-fil `jsonPathDefinition` stöds inte inställningen för att OMFORMA JSON-objektet. Hela objekten kommer att hämtas.

## <a name="use-the-lookup-activity-result"></a>Använd Sök aktivitets resultatet

Sök resultatet returneras i `output` avsnittet i aktivitets körnings resultatet.

* **När `firstRowOnly` är inställt på `true` (standard)** visas utdataformatet som i följande kod. Uppslags resultatet är under en fast `firstRow` nyckel. Använd mönstret i om du vill använda resultatet i efterföljande aktivitet  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **När `firstRowOnly` är inställt på `false`** visas utdataformatet som i följande kod. Ett `count` fält indikerar hur många poster som returneras. Detaljerade värden visas under en fast `value` matris. I sådana fall följs söknings aktiviteten av en [förgrunds aktivitet](control-flow-for-each-activity.md). Du skickar `value` matrisen till fältet för förgrunds aktiviteter `items` med hjälp av mönstret `@activity('MyLookupActivity').output.value` . Använd följande syntax för att få åtkomst till element i `value` matrisen: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Ett exempel är `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exempel

I det här exemplet innehåller pipelinen två aktiviteter: **Lookup** och **copy**. Kopierings aktiviteten kopierar data från en SQL-tabell i Azure SQL Database-instansen till Azure Blob Storage. Namnet på SQL-tabellen lagras i en JSON-fil i Blob Storage. Sök aktiviteten söker efter tabell namnet vid körning. JSON ändras dynamiskt med hjälp av den här metoden. Du behöver inte distribuera pipeliner eller data uppsättningar. 

I det här exemplet visas endast sökning efter den första raden. Om du vill söka efter alla rader och kedja resultaten med förgrunds aktiviteter, se exemplen i [Kopiera flera tabeller i bulk med hjälp av Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Pipeline

- Uppslags aktiviteten konfigureras att använda **LookupDataset**, som refererar till en plats i Azure Blob Storage. Sök aktiviteten läser namnet på SQL-tabellen från en JSON-fil på den här platsen. 
- Kopierings aktiviteten använder resultatet från söknings aktiviteten, vilket är namnet på SQL-tabellen. Egenskapen **TableName** i **SourceDataset** har kon figurer ATS för att använda utdata från lookup-aktiviteten. Kopierings aktiviteten kopierar data från SQL-tabellen till en plats i Azure Blob Storage. Platsen anges av egenskapen **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Sök efter data uppsättning

**Sök** data uppsättningen är **sourcetable.js** i filen i mappen Azure Storage sökning som anges av **AzureBlobStorageLinkedService** -typen. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Käll** data uppsättning för kopierings aktivitet

**Käll** data uppsättningen använder utdata från söknings aktiviteten, vilket är namnet på SQL-tabellen. Kopierings aktiviteten kopierar data från den här SQL-tabellen till en plats i Azure Blob Storage. Platsen anges av **Sink** -datamängden. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Mottagar** data uppsättning för kopierings aktivitet

Kopierings aktiviteten kopierar data från SQL-tabellen till den **filebylookup.csv** filen i **CSV** -mappen i Azure Storage. Filen anges av egenskapen **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jspå

Du kan använda följande två typer av format för **sourcetable.jsi** filen.

#### <a name="set-of-objects"></a>Uppsättning objekt

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Objekt mat ris

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här följer några begränsningar för söknings aktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Sök aktiviteten har högst 5 000 rader och en maximal storlek på 4 MB. | Utforma en pipeline på två nivåer där den yttre pipelinen upprepas över en inre pipeline, som hämtar data som inte överskrider maximalt antal rader eller storlek. |
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
