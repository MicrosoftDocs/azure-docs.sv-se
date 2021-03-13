---
title: Använd en regel motor för att använda HTTPS i standard Azure CDN | Microsoft Docs
description: Använd regel motorn för Microsoft standard Azure-Content Delivery Network (Azure CDN) för att anpassa hur Azure CDN hanterar HTTP-begäranden, inklusive att blockera leverans av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden. I den här artikeln lär du dig hur du skapar en regel för att omdirigera användare till HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b94798580b2d4ba746c152486c0de753cf64d2c9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564908"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Konfigurera standard regel motorn för Azure CDN

Den här artikeln beskriver hur du konfigurerar och använder standard regel motorn för Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Standard regel motor

Du kan använda standard regel motorn för Azure CDN för att anpassa hur HTTP-begäranden hanteras. Du kan till exempel använda regel motorn för att genomdriva innehålls leverans för att använda vissa protokoll, för att definiera en princip för cachelagring eller ändra ett HTTP-huvud. Den här artikeln visar hur du skapar en regel som omdirigerar användare till HTTPS automatiskt. 

> [!NOTE]
> Regel motorn som beskrivs i den här artikeln är endast tillgänglig för standard Azure CDN från Microsoft. 

## <a name="redirect-users-to-https"></a>Omdirigera användare till HTTPS

1. I dina Microsoft-profiler går du till Azure Content Delivery Network.

1. På sidan **CDN-profil** väljer du den slut punkt som du vill skapa regler för.
  
1. Välj fliken **regel motor** .
   
    Fönstret **regel motor** öppnas och visar en lista över tillgängliga globala regler. 
   
    [![Sidan Azure CDN nya regler](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Ordningen i vilken flera regler visas påverkar hur regler hanteras. De åtgärder som anges i en regel kan skrivas över av en efterföljande regel.
   >

1. Välj **Lägg till regel** och ange ett regel namn. Regel namn måste börja med en bokstav och får bara innehålla siffror och bokstäver.

1. Om du vill identifiera vilken typ av förfrågningar som regeln gäller för skapar du ett matchnings villkor:
    1. Välj **Lägg till villkor** och välj sedan villkoret för matchning av **begäran protokoll** .
    1. Som **Operator** väljer du **Lika med**.
    1. För **värde** väljer du **http**.
   
   [![Villkor för Azure CDN regel matchning](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Du kan välja mellan flera matchnings villkor i list rutan **Lägg till villkor** . En detaljerad lista över matchnings villkor finns [i matchnings villkor i standard regel motorn](cdn-standard-rules-engine-match-conditions.md).
   
1. Välj den åtgärd som ska tillämpas på de begär Anden som uppfyller matchnings villkoret:
   1. Välj **Lägg till åtgärd** och välj sedan **URL-omdirigering**.
   1. I **typ** väljer du **found (302)**.
   1. För **Protokoll** väljer du **HTTPS**.
   1. Lämna alla andra fält tomma om du vill använda inkommande värden.
   
   [![Åtgärd för Azure CDN regel](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Du kan välja mellan flera åtgärder i list rutan **Lägg till åtgärd** . En detaljerad lista över åtgärder finns i [åtgärder i standard regel motorn](cdn-standard-rules-engine-actions.md).

6. Välj **Spara** för att spara den nya regeln. Regeln är nu tillgänglig för användning.
   
   > [!IMPORTANT]
   > Regel ändringar kan ta upp till 15 minuter innan de sprids via Azure CDN.
   >
   

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Matchnings villkor i standard regel motorn](cdn-standard-rules-engine-match-conditions.md)
- [Åtgärder i standard regel motorn](cdn-standard-rules-engine-actions.md)
