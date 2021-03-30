---
title: StorageAccountSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Storage. StorageAccountSelector UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033313"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Storage. StorageAccountSelector

En kontroll för att välja ett nytt eller befintligt lagrings konto.

## <a name="ui-sample"></a>UI-exempel

Kontrollen visar standardvärdet.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

Kontrollen gör det möjligt för användaren att skapa ett nytt lagrings konto eller välja ett befintligt lagrings konto.

![Microsoft. Storage. StorageAccountSelector New](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Kommentarer

- Om det här värdet anges `defaultValue.name` verifieras automatiskt för unikhet. Om lagrings konto namnet inte är unikt måste användaren ange ett annat namn eller välja ett befintligt lagrings konto.
- Standardvärdet för `defaultValue.type` är **Premium_LRS**.
- Alla typer som inte anges i `constraints.allowedTypes` är dolda och alla typer som inte anges i `constraints.excludedTypes` visas. `constraints.allowedTypes` och `constraints.excludedTypes` är både valfria, men kan inte användas samtidigt.
- Om `options.hideExisting` är **Sant** kan användaren inte välja ett befintligt lagrings konto. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
