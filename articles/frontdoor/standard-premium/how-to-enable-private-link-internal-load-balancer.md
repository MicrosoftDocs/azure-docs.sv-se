---
title: Anslut Azure front dörr Premium till en intern belastningsutjämnare med privat länk
titleSuffix: Azure Private Link
description: Lär dig hur du ansluter din Azure frontend-dörr Premium till en intern belastningsutjämnare.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803824"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Anslut Azure front dörr Premium till en intern belastningsutjämnare med privat länk

Den här artikeln beskriver hur du konfigurerar Azure frontend-SKU: n för att ansluta till ditt interna belastnings Utjämnings ursprung med tjänsten Azure Private Link.

## <a name="prerequisites"></a>Förutsättningar

Skapa en [privat länk-tjänst](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Aktivera privat länk till en intern belastningsutjämnare
 
I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt som skapats i Azures front dörrs privata nätverk. 

1. I din Azure front dörr Premium-profil väljer du **ursprungs grupper** under *Inställningar*.

1. Välj den ursprungs grupp som du vill aktivera privat länk för den interna belastningsutjämnaren.

1. Välj **+ Lägg till ett ursprung** för att lägga till en intern belastnings Utjämnings ursprung.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Skärm bild som visar hur du aktiverar privat länk till en intern belastningsutjämnare.":::

1. För **Välj en Azure-resurs** väljer du **i min katalog**. Välj eller ange följande inställningar för att konfigurera den plats som du vill att Azure frontend-dörren ska ansluta till privat.

    | Inställning | Värde |
    | ------- | ----- |
    | Region | Välj den region som är samma eller närmast ditt ursprung. |
    | Resurstyp | Välj **Microsoft. Network/privateLinkServices**. |
    | Resurs | Välj den privata länken som är kopplad till den interna belastningsutjämnaren. |
    | Mål under resurs | Lämna tomt. |
    | Begär ande meddelande | Anpassa meddelandet eller Välj standard. |

1. Välj sedan **Lägg till** och sedan **Uppdatera** för att spara konfigurationen.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Godkänn privat slut punkts anslutning från lagrings kontot

1. Gå till det privata länk centret och välj **privata länk tjänster**. Välj sedan namnet på din privata länk.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Skärm bild av privat länk lista.":::

1. Välj **privata slut punkts anslutningar** under *Inställningar*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Skärm bild av översikts sidan för privat länk.":::

1. Välj den *väntande* privata slut punkts förfrågan från Azure front dörr Premium och välj sedan **Godkänn**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Skärm bild av väntande godkännande för privat länk.":::

1. När den har godkänts bör den se ut som skärm bilden nedan. Det tar några minuter för anslutningen att etableras fullständigt. Nu kan du få åtkomst till den interna belastningsutjämnaren från Azure front dörr Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Skärm bild av begäran om godkänd privat länk.":::

## <a name="next-steps"></a>Nästa steg

Läs mer om [tjänsten för privata länkar](../../private-link/private-link-service-overview.md).
