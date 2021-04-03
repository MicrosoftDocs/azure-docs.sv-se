---
title: GRÄNSSNITTs element för informations instans
description: Beskriver användar gränssnitts elementet Microsoft. Common. info i Azure Portal. Använd för att lägga till text eller varningar när du distribuerar hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033366"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Användar gränssnitts element för Microsoft. Common. info

En kontroll som lägger till en informations ruta. Rutan innehåller viktig text eller varningar som hjälper användarna att förstå de värden som de tillhandahåller. Den kan också länka till en URI för mer information.

## <a name="ui-sample"></a>UI-exempel

![Microsoft. Common. info](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Kommentarer

* För `icon` , Använd **ingen**, **information**, **Varning** eller **fel**.
* `uri`Egenskapen är valfri.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
