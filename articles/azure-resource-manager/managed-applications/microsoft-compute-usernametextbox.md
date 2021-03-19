---
title: UserNameTextBox-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Compute. UserNameTextBox för Azure Portal. Gör det möjligt för användare att ange Windows-eller Linux-användarnamn.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063590"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. UserNameTextBox

En text Rute kontroll med inbyggd verifiering för Windows-och Linux-användarnamn.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"Example name"
```

## <a name="remarks"></a>Kommentarer

- Om `constraints.required` är inställt på **Sant** måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är **True**.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**.
- `constraints.regex` är ett mönster för reguljära JavaScript-uttryck. Om det här alternativet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är **Null**.
- `constraints.validationMessage` är en sträng som ska visas när text rutans värde inte klarar den verifiering som anges av `constraints.regex` . Om inget värde anges används text rutans inbyggda verifierings meddelanden. Standardvärdet är **Null**.
- Det här elementet har en inbyggd verifiering som baseras på det värde som har angetts för `osPlatform` . Den inbyggda verifieringen kan användas tillsammans med ett anpassat reguljärt uttryck. Om ett värde för `constraints.regex` anges utlöses båda de inbyggda och anpassade valideringarna.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
