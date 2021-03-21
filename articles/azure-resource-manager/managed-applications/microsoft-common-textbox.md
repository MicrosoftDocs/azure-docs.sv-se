---
title: UI-element för text rutor
description: Beskriver elementet Microsoft. Common. text Rute gränssnitt för Azure Portal. Använd för att lägga till oformaterad text.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124337"
---
# <a name="microsoftcommontextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. text Rute

Ett användar gränssnitts element (UI) som kan användas för att lägga till oformaterad text. `Microsoft.Common.TextBox`Elementet är ett fält med en enradig indata, men stöder flera rader med `multiLine` egenskapen.

## <a name="ui-sample"></a>UI-exempel

- `TextBox` Elementet använder en text ruta med en rad eller flera rader.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Text ruta med en enkel rad i Microsoft. Common. text Rute element.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Text ruta med flera rader i Microsoft. Common. text ruta med text ruta.":::

## <a name="schema"></a>Schema

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"contoso123"
```

## <a name="remarks"></a>Kommentarer

- Använd `toolTip` egenskapen för att visa text om elementet när mus markören hålls över informations symbolen.
- `placeholder`Egenskapen är hjälp text som försvinner när användaren börjar redigera. Om `placeholder` och `defaultValue` båda definieras prioriteras `defaultValue` och visas.
- `multiLine`Egenskapen är boolesk `true` eller `false` . Ange egenskapen till om du vill använda en flerradig text ruta `true` . Om en flerradig text ruta inte behövs anger du egenskapen till `false` eller undantar egenskapen. För nya rader visar JSON-utdata `\n` för linje matningen. Text rutan med flera rader accepterar `\r` en vagn retur (CR) och `\n` för en rad matning (LF). Ett standardvärde kan till exempel inkludera `\r\n` för att ange CRLF.
- Om `constraints.required` är inställt på `true` , måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är `false`.
- `validations`Egenskapen är en matris där du lägger till villkor för att kontrol lera värdet som anges i text rutan.
- `regex`Egenskapen är ett mönster för reguljära uttryck i Java Script. Om värdet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är `null`. Mer information om regex-syntax finns i [snabb referens för reguljära uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- `isValid`Egenskapen innehåller ett uttryck som utvärderas till `true` eller `false` . I uttrycket definierar du villkoret som avgör om text rutan är giltig.
- `message`Egenskapen är en sträng som visas när text rutans värde inte kan verifieras.
- Det är möjligt att ange ett värde för `regex` när `required` är inställt på `false` . I det här scenariot krävs inget värde för att text rutan ska verifieras korrekt. Om en anges måste den matcha mönstret för reguljära uttryck.

## <a name="examples"></a>Exempel

I exemplen visas hur du använder en text ruta med en rad och en flerradig text ruta.

### <a name="single-line"></a>Enkel rad

I följande exempel används en text ruta med [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) -kontrollen för att kontrol lera tillgängligheten för ett resurs namn.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Flera rader

I det här exemplet används `multiLine` egenskapen för att skapa en flerradig text ruta med platshållartext.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [CreateUiDefinition.jspå för Azure Managed Application Create Experience](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
