---
title: Anslut Azure front dörr Premium till ett webb programs ursprung med privat länk
titleSuffix: Azure Private Link
description: Lär dig hur du ansluter Azure front dörr Premium till en webapp privat.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030587"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Anslut Azure front dörr Premium till ett webb programs ursprung med privat länk

Den här artikeln beskriver hur du konfigurerar Azure frontend-SKU: n för att ansluta till webbappen privat med tjänsten Azure Private Link.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Skapa en [privat länk](../../private-link/create-private-link-service-portal.md) tjänst för dina ursprungliga webb servrar.

> [!Note]
> Privat slut punkt är tillgänglig i offentliga regioner för PremiumV2-nivå, PremiumV3 Windows-webbappar, Linux-webbappar och Azure Functions Premium-plan (kallas ibland elastisk Premium-plan).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Aktivera privat länk till en webbapp i Azure front dörr Premium
 
I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt som skapats i Azures front dörrs privata nätverk. 

1. I din Azure front dörr Premium-profil väljer du **ursprungs grupper** under *Inställningar*.

1. Välj den ursprungs grupp som innehåller det ursprungs-webbapp som du vill aktivera privat länk för.

1. Välj **+ Lägg till ett ursprung** för att lägga till ett nytt webb programs ursprung eller Välj ett tidigare skapat webb program från listan.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Skärm bild som visar hur du aktiverar privat länk till en webbapp.":::

1. För **Välj en Azure-resurs** väljer du **i min katalog**. Välj eller ange följande inställningar för att konfigurera den plats som du vill att Azure frontend-dörren ska ansluta till privat.

    | Inställning | Värde |
    | ------- | ----- |
    | Region | Välj den region som är samma eller närmast ditt ursprung. |
    | Resurstyp | Välj **Microsoft. Web/Sites**. |
    | Resurs | Välj **myPrivateLinkService**. |
    | Mål under resurs | webbplatser |
    | Begär ande meddelande | Anpassa meddelandet eller Välj standard. |

1. Välj sedan **Lägg till** för att spara konfigurationen.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Godkänn privat slut punkts anslutning för Azure frontend-dörr från webbappen

1. Gå till den webbapp som du konfigurerar privat länk för i det sista avsnittet. Välj **nätverk** under **Inställningar**.

1. I **nätverk** väljer **du konfigurera dina privata slut punkts anslutningar**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Skärm bild av nätverks inställningar i en webbapp.":::

1. Välj den *väntande* privata slut punkts förfrågan från Azure front dörr Premium och välj sedan **Godkänn**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Skärm bild av väntande begäran om privat slut punkt.":::

1. När den har godkänts bör den se ut som skärm bilden nedan. Det tar några minuter för anslutningen att etableras fullständigt. Nu kan du komma åt din webbapp från Azures front dörr Premium. Direkt åtkomst till webbappen från det offentliga Internet inaktive ras när privat slut punkt aktive ras.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Skärm bild av godkänd slut punkts förfrågan.":::

## <a name="next-steps"></a>Nästa steg

Lär dig om [tjänsten för privat länk med Azure Web App](../../app-service/networking/private-endpoint.md).
