---
title: Konfigurera Azures front dörrs väg
description: Den här artikeln visar hur du konfigurerar en väg mellan dina domäner och ursprungs grupper.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099540"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Konfigurera en Azure frontend-dörr standard/Premium-väg

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

I den här artikeln beskrivs var och en av de inställningar som används för att skapa en AFD-väg (Azure frontend dörr) för en befintlig slut punkt. När du har lagt till en anpassad domän och ursprung i din befintliga Azure-frontend-slutpunkt måste du konfigurera vägen för att definiera associationen mellan domäner och ursprung för att dirigera trafiken mellan dem.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera en Azure-frontend-väg måste du skapa minst en ursprungs grupp och en anpassad domän inom den aktuella slut punkten. 

Information om hur du konfigurerar en ursprungs grupp finns i [skapa en ny ursprungs grupp för Azure frontend-dörr standard/Premium](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Skapa en ny Azure-front dörr standard/Premium-väg

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azures standard-/Premium-profil.

1. Välj **slut punkts hanteraren** under **Inställningar**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Skärm bild av inställningarna för slut punkts hanteraren för front dörren." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Välj sedan **Redigera slut punkt** för den slut punkt som du vill konfigurera vägen.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Skärm bild av val av redigera slut punkt.":::

1. Sidan **Redigera slut punkt** visas. Välj **+ Lägg till** för vägar.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Skärm bild av sidan Lägg till en väg på Redigera slut punkt.":::    
    
1. På sidan **Lägg till väg** anger eller väljer du följande information.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Skärm bild av sidan Lägg till en väg." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Inställning | Värde |
    | --- | --- |
    | Namn | Ange ett unikt namn för den nya vägen. |   
    | Domain| Välj en eller flera domäner som har verifierats och som inte är kopplade till en annan väg |
    | Mönster som ska matchas  | Konfigurera alla URL-sökvägar som den här vägen accepterar. Du kan till exempel ange att det ska `/images/*` acceptera alla begär Anden på URL: en `www.contoso.com/images/*` . AFD kommer att försöka fastställa trafiken baserat på exakt matchning först, om inga exakta matchnings Sök vägar används, och sedan söka efter en sökväg för jokertecken som matchar. Om inga regler för routning hittas med en matchande sökväg avvisar du begäran och returnerar en 400: felaktig begäran HTTP-svar. |
    | Godkända protokoll | Ange de protokoll som du vill att Azure-front dörren ska acceptera när klienten gör begäran. |
    | Omdirigera | Ange om HTTPS ska tillämpas för inkommande begäran med HTTP-begäran |
    | Ursprungs grupp | Välj vilken ursprungs grupp som ska vidarebefordras till när begäran om tillbaka till ursprung inträffar. |
    | Sökväg till ursprung | Ange sökvägen till de resurser som du vill cachelagra. Lämna inställningen tom om du vill tillåta cachelagring av alla resurser på domänen. |
    | Protokoll för vidarebefordran | Välj det protokoll som används för vidarebefordran av begäran. |
    | Cachelagring | Välj det här alternativet om du vill aktivera cachelagring av statiskt innehåll med Azures frontend-dörr. |
    | Regel | Välj regel uppsättningar som ska tillämpas på den här vägen. Mer information om hur du konfigurerar regler finns i [Konfigurera en regel uppsättning för Azures frontend-dörr](how-to-configure-rule-set.md) | 

1. Välj **Lägg till** för att skapa den nya vägen. Vägen visas i listan över vägar för slut punkten.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Skärm bild av listan över vägar.":::  
    
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort en väg när du inte längre behöver den väljer du vägen och väljer sedan **ta bort**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Skärm bild av hur du tar bort en väg.":::  

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om anpassade domäner fortsätter du till självstudien för att lägga till en anpassad domän i din Azures frontend-slutpunkt.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän]()
