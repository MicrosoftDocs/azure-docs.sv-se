---
title: Vänta-aktivitet i Azure Data Factory
description: Aktiviteten vänta pausar körningen av pipelinen för den angivna perioden.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 9271723e68e5edd6805f0197bca9cdefc3c5e921
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388336"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Kör vänta-aktivitet i Azure Data Factory
När du använder en Wait-aktivitet i en pipeline väntar pipelinen under den angivna tidsperioden innan körningen återupptas med alla efterföljande aktiviteter. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | `Wait`Aktivitetens namn. | Sträng | Ja
typ | Måste vara inställd på **wait**. | Sträng | Ja
waitTimeInSeconds | Antalet sekunder som pipelinen väntar innan bearbetningen fortsätter. | Integer | Ja

## <a name="example"></a>Exempel

> [!NOTE]
> Det här avsnittet innehåller JSON-definitioner och exempel på PowerShell-kommandon för att köra pipelinen. En genom gång med stegvisa instruktioner för att skapa en Data Factory pipeline med hjälp av Azure PowerShell-och JSON-definitioner finns i [Självstudier: skapa en data fabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline med vänta-aktivitet
I det här exemplet har pipelinen två aktiviteter: **till** och med **vänta**. Vänta-aktiviteten är konfigurerad att vänta en sekund. Pipelinen kör webb aktiviteten i en slinga med en andra vänte tid mellan varje körning. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)
