---
title: Flytta EA-och CSP-prenumerationer för Azure VMware
description: Lär dig hur du flyttar det privata molnet från en prenumeration till en annan. Rörelsen kan göras av olika orsaker, till exempel fakturering.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555748"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Flytta EA-och CSP-prenumerationer för Azure VMware

I den här artikeln får du lära dig hur du flyttar det privata molnet från en prenumeration till en annan. Rörelsen kan göras av olika orsaker, till exempel fakturering. 

>[!IMPORTANT]
>Du bör ha minst deltagar rättigheter för både käll-och mål prenumerationer. Det går inte att flytta VNet och VNet Gateway från en prenumeration till en annan. Att flytta dina prenumerationer påverkar dessutom inte hanteringen och arbets belastningarna, som vCenter, NSX och virtuella arbets belastnings datorer.

1. Logga in på Azure Portal och välj det privata moln som du vill flytta.

1. Välj **prenumerations länken (ändra)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Skärm bild som visar information om det privata molnet.":::

1. Ange prenumerations informationen för **målet** och välj **Nästa**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Skärm bild av mål resursen." lightbox="media/move-resources-subscription-target.png":::

1. Bekräfta verifieringen av de resurser som du har valt att flytta och välj **Nästa**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Skärm bild som visar den resurs som flyttas." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Markera kryss rutan som anger att du förstår att de verktyg och skript som är kopplade inte fungerar förrän du uppdaterar dem för att använda de nya resurs-ID: na. Välj sedan **Flytta**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Skärm bild som visar en sammanfattning av den valda resursen som flyttas. " lightbox="media/review-move-resources-subscription-target.png":::

   Ett meddelande visas när resurs flyttningen är klar. Den nya prenumerationen visas i Översikt över privata moln.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Skärm bild som visar en ny prenumeration." lightbox="media/moved-subscription-target.png":::

