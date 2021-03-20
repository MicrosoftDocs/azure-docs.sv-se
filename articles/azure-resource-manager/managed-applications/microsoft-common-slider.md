---
title: GRÄNSSNITTs element för Slider
description: Beskriver användar gränssnitts elementet Microsoft. Common. Slider för Azure Portal. Gör det möjligt för användare att ange ett värde från ett intervall med alternativ.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87100047"
---
# <a name="microsoftcommonslider-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. Slider

Med skjutreglaget-kontrollen kan användarna välja från ett intervall med tillåtna värden.

## <a name="ui-sample"></a>UI-exempel

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Schema

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
26
```

## <a name="remarks"></a>Kommentarer

- `min`Värdena och `max` måste anges. De anger start-och slut punkterna för skjutreglaget.
- `showStepMarkers`Standardvärdet för egenskapen är sant. Steg markörerna visas bara när intervallet från min till max är 100 eller mindre.


## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
