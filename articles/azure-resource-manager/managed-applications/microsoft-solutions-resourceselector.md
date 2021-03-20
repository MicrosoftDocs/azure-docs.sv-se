---
title: ResourceSelector-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Solutions. ResourceSelector för Azure Portal. Används för att hämta en lista över befintliga resurser.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87100220"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Solutions. ResourceSelector

Med ResourceSelector kan användarna välja en befintlig resurs från en prenumeration.

## <a name="ui-sample"></a>UI-exempel

![Microsoft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Schema

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Kommentarer

I `resourceType` egenskapen anger du resurs leverantörens namn område och resurs typ namnet för den resurs som du vill visa i listan.

`filter`Egenskapen begränsar de tillgängliga alternativen för resurserna. Du kan begränsa resultaten efter plats eller prenumeration. Om du bara vill visa resurser som matchar valet i grunderna använder du `onBasics` . Använd om du vill visa alla resurser `all` . Standardvärdet är `all`.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
