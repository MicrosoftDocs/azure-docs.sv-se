---
title: OptionsGroup-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. OptionsGroup UI för Azure Portal. Gör att användarna kan välja bland tillgängliga alternativ när de distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87004206"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elementet Microsoft. Common. OptionsGroup UI

Med OptionsGroup-kontrollen kan användarna välja ett alternativ från två eller fler alternativ. En användare kan bara välja ett alternativ.

> [!NOTE]
> Tidigare utsmälte den här kontrollen alternativen vågrätt. Nu visar kontrollen alternativen lodrätt som alternativ knappar.

## <a name="ui-sample"></a>UI-exempel

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"two"
```

## <a name="remarks"></a>Kommentarer

- Etiketten för `constraints.allowedValues` är visnings texten för ett objekt och dess värde är utdata-värdet för elementet när det är markerat.
- Om det här alternativet anges måste standardvärdet vara en etikett i `constraints.allowedValues` . Om inget värde anges väljs det första objektet i `constraints.allowedValues` som standard. Standardvärdet är **Null**.
- `constraints.allowedValues` måste ha minst ett objekt.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
