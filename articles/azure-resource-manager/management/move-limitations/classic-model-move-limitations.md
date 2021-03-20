---
title: Flytta klassiska Azure-distributions resurser
description: Använd Azure Resource Manager för att flytta klassiska distributions resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75485291"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Flytta vägledning för klassiska distributions modell resurser

Stegen för att flytta resurser som distribueras via den klassiska modellen beror på om du flyttar resurserna i en prenumeration eller till en ny prenumeration.

## <a name="move-in-the-same-subscription"></a>Flytta i samma prenumeration

När du flyttar resurser från en resurs grupp till en annan resurs grupp inom samma prenumeration gäller följande begränsningar:

* Det går inte att flytta virtuella nätverk (klassisk).
* Virtuella datorer (klassiska) måste flyttas med moln tjänsten.
* Det går bara att flytta moln tjänsten när den innehåller alla virtuella datorer.
* Det går bara att flytta en moln tjänst åt gången.
* Det går bara att flytta ett lagrings konto i taget.
* Lagrings kontot (klassisk) kan inte flyttas i samma åtgärd med en virtuell dator eller en moln tjänst.

Om du vill flytta klassiska resurser till en ny resurs grupp i samma prenumeration använder du [standard flyttnings åtgärder](../move-resource-group-and-subscription.md) via portalen, Azure PowerShell, Azure CLI eller REST API. Du använder samma åtgärder som du använder för att flytta Resource Manager-resurser.

## <a name="move-across-subscriptions"></a>Flytta över prenumerationer

När du flyttar resurser till en ny prenumeration gäller följande begränsningar:

* Alla klassiska resurser i prenumerationen måste flyttas i samma åtgärd.
* Mål prenumerationen får inte ha några andra klassiska resurser.
* Flytten kan bara begäras via en separat REST API för klassiska flyttningar. Standard kommandona för att flytta med Resource Manager fungerar inte när du flyttar klassiska resurser till en ny prenumeration.

Om du vill flytta klassiska resurser till en ny prenumeration använder du de REST-åtgärder som är speciella för klassiska resurser. Gör så här om du vill använda REST:

1. Kontrol lera om käll prenumerationen kan delta i en flyttning mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I begär ande texten inkluderar:

   ```json
   {
    "role": "source"
   }
   ```

     Svaret för verifierings åtgärden är i följande format:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Kontrol lera om mål prenumerationen kan delta i en flyttning mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I begär ande texten inkluderar:

   ```json
   {
    "role": "target"
   }
   ```

     Svaret har samma format som valideringen av käll prenumerationen.
1. Om båda prenumerationerna klarar valideringen flyttar du alla klassiska resurser från en prenumeration till en annan prenumeration med följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    I begär ande texten inkluderar:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Åtgärden kan köras i flera minuter.

## <a name="next-steps"></a>Nästa steg

Kontakta [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)om du har problem med att flytta klassiska resurser.

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
