---
title: SizeSelector-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Compute. SizeSelector för Azure Portal. Används för att välja storlek på en virtuell dator.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063629"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. SizeSelector

En kontroll för att välja en storlek för en eller flera virtuella dator instanser.

## <a name="ui-sample"></a>UI-exempel

Användaren ser en selektor med standardvärden från element definitionen.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

När du har valt kontrollen ser användaren en utökad vy av de tillgängliga storlekarna.

![Microsoft. Compute. SizeSelector utökad](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"Standard_D1"
```

## <a name="remarks"></a>Kommentarer

- `recommendedSizes` måste ha minst en storlek. Den första rekommenderade storleken används som standard. Listan över tillgängliga storlekar sorteras inte efter rekommenderat tillstånd. Användaren kan välja den kolumnen för att sortera efter rekommenderat tillstånd.
- Om en rekommenderad storlek inte är tillgänglig på den valda platsen hoppas storleken automatiskt över. I stället används nästa rekommenderade storlek.
- `constraints.allowedSizes` och `constraints.excludedSizes` är både valfria, men kan inte användas samtidigt. Listan över tillgängliga storlekar kan bestämmas genom att [listan med tillgängliga storlekar för virtuella datorer anropas för en prenumeration](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Alla storlekar som inte anges i `constraints.allowedSizes` är dolda och den storlek som inte anges i `constraints.excludedSizes` visas.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**. Den används för att fastställa maskin varu kostnaderna för de virtuella datorerna.
- `imageReference` utelämnas för avbildningar från första part, men tillhandahålls för avbildningar från tredje part. Den används för att fastställa program varu kostnaderna för de virtuella datorerna.
- `count` används för att ange lämplig multiplikator för elementet. Det stöder ett statiskt värde, till exempel **2**, eller ett dynamiskt värde från ett annat element, t `[steps('step1').vmCount]` . ex.. Standardvärdet är **1**.
- `numAvailabilityZonesRequired`Kan vara 1, 2 eller 3.
- Som standard `hideDiskTypeFilter` är **falskt**. Filtret disk typ gör att användaren kan se alla disk typer eller endast SSD.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
