---
title: Mall-funktioner – alla
description: Beskriver de funktioner som är tillgängliga i bicep för att konvertera typer.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746915"
---
# <a name="any-function-for-bicep"></a>Alla funktioner för bicep

Bicep stöder en funktion `any()` som kallas för att lösa typ fel i bicep-typ systemet. Du använder den här funktionen när formatet på värdet som du anger inte stämmer överens med vad typ systemet förväntar sig. Om egenskapen till exempel kräver ett nummer, men du måste ange den som en sträng, t. ex `'0.5'` .. Använd `any()` funktionen för att ignorera det fel som rapporteras av typen system.

Den här funktionen finns inte i Azure Resource Manager Template Runtime. Den används endast av bicep och genereras inte i JSON för den inbyggda mallen.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>valfri

`any(value)`

Returnerar ett värde som är kompatibelt med vilken datatyp som helst.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| värde | Ja | alla typer | Värdet som ska konverteras till en kompatibel typ. |

### <a name="return-value"></a>Returvärde

Värdet i ett formulär som är kompatibelt med vilken datatyp som helst.

### <a name="examples"></a>Exempel

I följande exempel mall visas hur du använder `any()` funktionen för att ange numeriska värden som strängar.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

Funktionen fungerar med ett tilldelat värde i bicep. Följande exempel använder `any()` med ett ternärt uttryck som argument.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Nästa steg

Mer komplexa användnings områden för `any()` funktionen finns i följande exempel:

* [Underordnade resurser som kräver ett angivet namn](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [En resurs egenskap som inte har definierats i resurs typen, även om den finns](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

