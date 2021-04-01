---
title: Avsnitts GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Common. section för Azure Portal. Använd för att gruppera element i portalen för att distribuera hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063971"
---
# <a name="microsoftcommonsection-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. section

En kontroll som grupperar ett eller flera element under en rubrik.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>Schema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer

- `elements` måste ha minst ett element och kan ha alla element typer förutom `Microsoft.Common.Section` .
- Det här elementet har inte stöd för `toolTip` egenskapen.

## <a name="sample-output"></a>Exempelutdata
Om du vill komma åt indatavärdena för element i `elements` använder du funktionerna [Basic ()](create-ui-definition-referencing-functions.md#basics) eller Step [()](create-ui-definition-referencing-functions.md#steps) och punkt notation:

```json
steps('configuration').section1.text1
```

Element av typen `Microsoft.Common.Section` har inga värden för utdata.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
