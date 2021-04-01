---
title: List Rute GRÄNSSNITTs element
description: Beskriver UI-elementet Microsoft. Common. list Rute gränssnitt för Azure Portal. Använd om du vill välja mellan tillgängliga alternativ när du distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168349"
---
# <a name="microsoftcommondropdown-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. list rutan

En markerings kontroll med en nedrullningsbar lista. Du kan bara välja ett enstaka objekt eller flera objekt. Du kan också välja att inkludera en beskrivning med objekten.

## <a name="ui-sample"></a>UI-exempel

List elementet har olika alternativ som avgör hur det ser ut i portalen.

När endast ett enda objekt tillåts för markering visas kontrollen som:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. Common. list Rute enkel markering":::

När beskrivningar ingår visas kontrollen som:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. Common. list Rute enkel markering med beskrivningar":::

När du har aktiverat flera val lägger kontrollen till alternativet **Välj alla** och kryss rutor för att markera fler än ett objekt:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. Common. listen Multi-SELECT":::

Beskrivningar kan inkluderas med flera SELECT-aktiverade.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Skärm bild som visar hur beskrivningar kan inkluderas med Multi-SELECT aktiverat":::

När filtrering är aktiverat innehåller kontrollen en text ruta för att lägga till filtrering svärdet.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. listruta Multi-SELECT med beskrivningar":::

## <a name="schema"></a>Schema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- Används `multiselect` för att ange om användarna ska kunna välja fler än ett objekt.
- Som standard `selectAll` är `true` när flera val är aktiverade.
- `filter`Egenskapen gör det möjligt för användare att söka inom en lång lista med alternativ.
- Etiketten för `constraints.allowedValues` är visnings texten för ett objekt och dess värde är utdata-värdet för elementet när det är markerat.
- Om det här alternativet anges måste standardvärdet vara en etikett i `constraints.allowedValues` . Om inget anges väljs det första objektet i `constraints.allowedValues` . Standardvärdet är **Null**.
- `constraints.allowedValues` måste ha minst ett objekt.
- För att emulera ett värde som inte krävs lägger du till ett objekt med etiketten och värdet `""` (tom sträng) till `constraints.allowedValues` .
- `defaultDescription`Egenskapen används för objekt som inte har någon beskrivning.
- `placeholder`Egenskapen är hjälp text som försvinner när användaren börjar redigera. Om `placeholder` och `defaultValue` båda definieras prioriteras `defaultValue` och visas.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
