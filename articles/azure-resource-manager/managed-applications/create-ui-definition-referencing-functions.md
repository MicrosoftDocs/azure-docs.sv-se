---
title: Skapa referens funktioner för GRÄNSSNITTs definitioner
description: Beskriver de funktioner som används när du skapar GRÄNSSNITTs definitioner för Azure Portal som refererar till andra objekt.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099919"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition-referens funktioner

De funktioner som används för att referera till utdata från egenskaper eller kontexten för en CreateUiDefinition-fil.

## <a name="basics"></a>om

Returnerar värdena för ett element som definieras i steget [grundläggande](create-uidefinition-overview.md#basics) . Överför namnet på elementet som en parameter till den här funktionen.

Använd funktionen [steg ()](#steps) för att hämta utdata för element i andra steg.

I följande exempel returneras utdata från elementet med namnet `clusterName` i det grundläggande steget:

```json
"[basics('clusterName')]"
```

De returnerade värdena varierar beroende på vilken typ av element som hämtas.

## <a name="location"></a>location

Returnerar den plats som valts i det grundläggande steget eller den aktuella kontexten.

Följande exempel returnerar ett värde som `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Returnerar information om den resourceGroup som valts i det grundläggande steget eller i den aktuella kontexten.

Följande exempel:

```json
"[resourceGroup()]"
```

Returnerar följande egenskaper:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Du kan få ett visst värde med punkt notation.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steg

Returnerar elementen i ett angivet steg. Överför namnet på steget som en parameter till den här funktionen. Du kan hämta specifika egenskaps värden från de returnerade elementen.

Om du vill hämta indatavärdena för element i grundläggande steg, använder du funktionen [Basics ()](#basics) .

I följande exempel returneras steget med namnet `vmParameters` . I det steget är ett-element med namnet `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>prenumeration

Returnerar egenskaperna för den prenumeration som valts i det grundläggande steget eller i den aktuella kontexten.

Följande exempel:

```json
"[subscription()]"
```

Returnerar följande egenskaper:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till att utveckla ett Portal gränssnitt finns i [CreateUiDefinition.jspå för Azure Managed Application Create Experience](create-uidefinition-overview.md).
