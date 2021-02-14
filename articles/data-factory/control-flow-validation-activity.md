---
title: Validerings aktivitet i Azure Data Factory
description: Validerings aktiviteten fortsätter inte att köra pipelinen förrän den verifierar den bifogade data uppsättningen med vissa villkor som användaren anger.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e00090fc31c129f35632d30b9a5ed6c39c13a414
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385259"
---
# <a name="validation-activity-in-azure-data-factory"></a>Validerings aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Du kan använda en verifiering i en pipeline för att se till att pipelinen bara fortsätter att köra när den har verifierat att den bifogade data uppsättnings referensen finns, att den uppfyller de angivna villkoren eller att tids gränsen har nåtts.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
name | Namnet på validerings aktiviteten | Sträng | Ja |
typ | Måste vara inställd på  **verifiering**. | Sträng | Ja |
data uppsättning | Aktiviteten blockerar körningen tills den har verifierat att den här data uppsättnings referensen finns och att den uppfyller de angivna villkoren, eller också har tids gränsen nåtts. Den angivna data uppsättningen ska ha stöd för egenskapen "MinimumSize" eller "ChildItems". | Referens för data mängd | Ja |
timeout | Anger tidsgränsen för aktivitetens körning. Om inget värde anges är standardvärdet 7 dagar ("7.00:00:00"). Formatet är d. hh: mm: SS | Sträng | Inga |
Spar | En fördröjning i sekunder mellan verifierings försök. Om inget värde anges är standardvärdet 10 sekunder. | Integer | Inga |
childItems | Kontrollerar om mappen har underordnade objekt. Kan anges till-True: kontrol lera att mappen finns och att den har objekt. Block tills minst ett objekt finns i mappen eller så har timeout-värdet nåtts.-false: kontrol lera att mappen finns och att den är tom. Blockerar tills mappen är tom eller tills timeout-värdet nås. Om inget värde anges blockerar aktiviteten tills mappen finns eller tills tids gränsen har nåtts. | Boolesk | Inga |
minimumSize | Minsta storlek på en fil i byte. Om inget värde anges är standardvärdet 0 byte | Integer | Nej |


## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)
