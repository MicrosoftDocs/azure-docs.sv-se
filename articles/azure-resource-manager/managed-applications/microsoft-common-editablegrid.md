---
title: EditableGrid-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. EditableGrid UI för Azure Portal. Gör det möjligt för användare att samla in tabell ingångar.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88893816"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Elementet Microsoft. Common. EditableGrid UI

En kontroll för insamling av tabell indata. Alla fält i rutnätet kan redige ras och antalet rader kan variera.

## <a name="ui-sample"></a>UI-exempel

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>Schema

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Exempelutdata

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Kommentarer

- De enda giltiga kontrollerna inom matrisen kolumns är [text rutan](microsoft-common-textbox.md), [OptionsGroup](microsoft-common-optionsgroup.md)och [list rutan](microsoft-common-dropdown.md).
- `$rowIndex`Variabeln är endast giltig i uttryck som finns i underordnade till rutnätets kolumner. Det är ett heltal som representerar elementets relativa rad index och antalet börjar med ett och flera steg. Som du ser i schemat `"columns":` , `$rowIndex` används för verifiering.
- När valideringar utförs med `$rowIndex` variabeln, är det möjligt att hämta den aktuella radens värde genom att kombinera `last()` `take()` kommandona och.

  Exempel:

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`Egenskapen visas inte som en del av kontrollen men visas på den sista fliken i sammanfattningen.
- `ariaLabel`Egenskapen är hjälpmedels etiketten för rutnätet. Ange nyttig text för användare som använder skärm läsare.
- `constraints.width`Egenskapen används för att ange rutnätets övergripande bredd. Alternativen är _full_, _medium_, _liten_. Standardvärdet är _full_.
- `width`Egenskapen för kolumnernas underordnade bestämmer kolumn bredden. Bredder anges med bråktal, till exempel _3FR_, med totalt utrymme som tilldelas till kolumner som är proportionella för deras enheter. Om ingen kolumn bredd har angetts är standardvärdet _1fr_.

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
