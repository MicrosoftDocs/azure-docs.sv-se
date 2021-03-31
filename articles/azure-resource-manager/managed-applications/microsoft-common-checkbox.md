---
title: GRÄNSSNITTs element för kryss ruta
description: Beskriver UI-elementet Microsoft. Common. CheckBox för Azure Portal. Gör att användare kan markera eller avmarkera ett alternativ.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87100051"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. CheckBox

Med kryss Rute kontrollen kan användare Markera eller avmarkera ett alternativ. Kontrollen returnerar **True** när kontrollen är markerad eller **falskt** när den inte är markerad.

## <a name="ui-sample"></a>UI-exempel

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Kryss ruta för Microsoft. Common.":::

## <a name="schema"></a>Schema

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Exempelutdata

```json
true
```

## <a name="remarks"></a>Kommentarer

När du anger **obligatorisk** till **Sant** måste användaren Markera kryss rutan. Om användaren inte markerar kryss rutan visas verifierings meddelandet.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
