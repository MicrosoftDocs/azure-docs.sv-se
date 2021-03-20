---
title: GRÄNSSNITTs element för lösen ord
description: Beskriver användar gränssnitts elementet Microsoft. Common. password för Azure Portal. Gör att användarna kan ange ett hemligt värde när de distribuerar hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7902ea2e30dec20e57d88344dc9507aec3993600
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87064105"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. password

En kontroll som kan användas för att ange och bekräfta ett lösen ord.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft-common-passwordbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Kommentarer

- Det här elementet har inte stöd för `defaultValue` egenskapen.
- Information om implementeringen av `constraints` finns i [Microsoft. Common. text ruta](microsoft-common-textbox.md).
- Om `options.hideConfirmation` är inställt på **Sant** döljs den andra text rutan för att bekräfta användarens lösen ord. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
