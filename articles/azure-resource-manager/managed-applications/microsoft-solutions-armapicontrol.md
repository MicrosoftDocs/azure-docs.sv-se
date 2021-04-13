---
title: ArmApiControl-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Solutions. ArmApiControl för Azure Portal. Används för att anropa API-åtgärder.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313308"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>GRÄNSSNITTs element för Microsoft. Solutions. ArmApiControl

Med ArmApiControl kan du få resultat från en Azure Resource Manager API-åtgärd. Använd resultaten för att fylla i dynamiskt innehåll i andra kontroller.

## <a name="ui-sample"></a>UI-exempel

Det finns inget användar gränssnitt för den här kontrollen.

## <a name="schema"></a>Schema

I följande exempel visas schemat för den här kontrollen:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Exempelutdata

Kontrollens utdata visas inte för användaren. I stället används resultatet av åtgärden i andra kontroller.

## <a name="remarks"></a>Kommentarer

- `request.method`Egenskapen anger HTTP-metoden. Endast `GET` eller `POST` tillåts.
- `request.path`Egenskapen anger en URL som måste vara en relativ sökväg till en arm-slutpunkt. Det kan vara en statisk sökväg eller kan konstrueras dynamiskt genom att referera till utmatnings värden för de andra kontrollerna.

  Till exempel anropet ARM till `Microsoft.Network/expressRouteCircuits` Resource provider:

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- `request.body`Egenskapen är valfri. Använd den för att ange en JSON-text som skickas med begäran. Texten kan vara statiskt innehåll eller konstrueras dynamiskt genom att referera till utmatnings värden från andra kontroller.

## <a name="example"></a>Exempel

I följande exempel `providersApi` anropar-elementet ett API för att hämta en matris med leverantörs objekt.

`allowedValues`Egenskapen för- `providersDropDown` elementet har kon figurer ATS för att hämta namnen på providern. Den visas i list rutan.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Ett exempel på hur du använder ArmApiControl för att kontrol lera tillgängligheten för ett resurs namn finns i [Microsoft. Common. text ruta](microsoft-common-textbox.md).

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
