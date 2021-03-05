---
title: Förgrunds aktiviteter i Azure Data Factory
description: För varje aktivitet definieras ett upprepat kontroll flöde i din pipeline. Den används för att iterera över en samling och köra angivna aktiviteter.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: aeabd74117f99c7cac9bde0eda02b9627caf0804
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177800"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Förgrunds aktiviteter i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med en förgrunds aktivitet definieras ett upprepat kontroll flöde i din pipeline. Den här aktiviteten används till att iterera över en samling och kör angivna aktiviteter i en loop. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk.

## <a name="syntax"></a>Syntax
Egenskaperna beskrivs längre fram i den här artikeln. Egenskapen Items är samlingen och varje objekt i samlingen kallas genom att använda det `@item()` som visas i följande syntax:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | Namnet på for-each-aktiviteten. | Sträng | Ja
typ | Måste vara inställt **på Sol** | Sträng | Ja
isSequential | Anger om loopen ska köras sekventiellt eller parallellt.  Högst 20 upprepnings iterationer kan köras samtidigt parallellt). Om du till exempel har en titt på en aktivitet med 10 olika käll-och mottagar data uppsättningar med **isSequential** inställt på falskt körs alla kopior samtidigt. Standardvärdet är false. <br/><br/> Om "isSequential" är inställt på false kontrollerar du att det finns en korrekt konfiguration för att köra flera körbara filer. Annars bör den här egenskapen användas med försiktighet för att undvika att skapa Skriv konflikter. Mer information finns i avsnittet om [parallell körning](#parallel-execution) . | Boolesk | Nej. Standardvärdet är false.
batchCount | Antal batchar som ska användas för att kontrol lera antalet parallell körningar (när isSequential är inställt på falskt). Detta är den övre samtidiga gränsen, men för-varje aktivitet kommer inte alltid att köras på det här numret | Heltal (högst 50) | Nej. Standardvärdet är 20.
Poster | Ett uttryck som returnerar en JSON-matris som ska upprepas. | Uttryck (som returnerar en JSON-matris) | Ja
Aktiviteter | De aktiviteter som ska utföras. | Lista med aktiviteter | Ja

## <a name="parallel-execution"></a>Parallell körning
Om **isSequential** har angetts till false, upprepas aktiviteten parallellt med maximalt 20 samtidiga iterationer. Den här inställningen bör användas med försiktighet. Om samtidiga iterationer skrivs till samma mapp, men till olika filer, är den här metoden bra. Om samtidiga iterationer skrivs samtidigt till exakt samma fil, orsakar den här metoden förmodligen ett fel. 

## <a name="iteration-expression-language"></a>Språk för upprepnings uttryck
I förgrunds aktiviteten anger du en matris som ska upprepas för egenskaps **objekt**. " Används `@item()` för att iterera över en enskild uppräkning i en förgrunds aktivitet. Om **objekt** till exempel är en matris: [1, 2, 3], `@item()` returnerar 1 i den första iterationen, 2 i den andra iterationen och 3 i den tredje iterationen.

## <a name="iterating-over-a-single-activity"></a>Iterera över en enskild aktivitet
**Scenario:** Kopiera från samma källfil i Azure blob till flera målfiler i Azure blob.

### <a name="pipeline-definition"></a>Pipeline-definition

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definition av BLOB-datauppsättning

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Kör parameter värden

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterera över flera aktiviteter
Det går att iterera över flera aktiviteter (t. ex. kopiering och webb aktiviteter) i en förgrunds aktivitet. I det här scenariot rekommenderar vi att du sammanfattar flera aktiviteter i en separat pipeline. Sedan kan du använda ExecutePipeline- [aktiviteten](control-flow-execute-pipeline-activity.md) i pipelinen med en förgrunds aktivitet för att anropa den separata pipelinen med flera aktiviteter. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Exempel
**Scenario:** Iterera över en InnerPipeline i en förgrunds aktivitet med utföra pipeline-aktivitet. Den inre pipelinen kopierar med schema definitioner Parameters.

#### <a name="master-pipeline-definition"></a>Huvud pipeline-definition

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definition av inre pipeline

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definition av käll data uppsättning

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definition av Sink-datamängd

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Huvud pipeline-parametrar
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Aggregera utdata

Om du vill aggregera utdata från en __förgrunds__ aktivitet använder du _variabler_ och _lägger till variabel_ aktivitet.

Deklarera först en `array` _variabel_ i pipelinen. Anropa sedan _Lägg till variabel_ aktivitet i varje __förgrunds__ slinga. Sedan kan du hämta agg regeringen från matrisen.

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här följer några begränsningar för den förgrunds aktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Du kan inte kapsla en förgrunds slinga i en annan förgrunds slinga (eller en until-slinga). | Utforma en pipeline på två nivåer där den yttre pipelinen med den yttre slingan upprepas över en inre pipeline med den kapslade slingan. |
| Förgrunds aktiviteten har högst `batchCount` 50 för parallell bearbetning och högst 100 000 objekt. | Utforma en pipeline på två nivåer där den yttre pipelinen med förgrunds aktiviteten upprepas över en inre pipeline. |
| SetVariable kan inte användas i en förgrunds aktivitet som körs parallellt eftersom variablerna är globala för hela pipelinen, de är inte begränsade till en eller flera aktiviteter. | Överväg att använda sekventiella eller använda en pipeline i förloppet (variabel/parameter som hanteras i den underordnade pipelinen).|
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
