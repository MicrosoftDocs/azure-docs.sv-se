---
title: Rensa en Azure CDN-slutpunkt | Microsoft Docs
description: Lär dig hur du rensar allt cachelagrat innehåll från en Azure Content Delivery Network-slutpunkt. Edge-noder cachelagrar till gångar tills deras tid till Live upphör att gälla.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: 4fe4b99f9635ff254f1a75e03f13d7e6ffcb3c49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366525"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Rensa en Azure CDN-slutpunkt
## <a name="overview"></a>Översikt
Azure CDN Edge-noder cachelagrar till gångar tills till gångens TTL (Time-to-Live) går ut.  När TTL-värdet för till gången har upphört att gälla när en klient begär till gången från Edge-noden, hämtar noden Edge en ny uppdaterad kopia av till gången för att betjäna klientbegäran och lagra uppdateringen av cachen.

Det bästa sättet att se till att användarna alltid får den senaste kopian av dina till gångar är att version till dina till gångar för varje uppdatering och publicera dem som nya URL: er.  CDN hämtar omedelbart de nya till gångarna för nästa klient begär Anden.  Ibland kanske du vill rensa cachelagrat innehåll från alla Edge-noder och tvinga dem att hämta nya, uppdaterade till gångar.  Detta kan bero på uppdateringar i ditt webb program eller för att snabbt uppdatera till gångar som innehåller felaktig information.

> [!TIP]
> Observera att rensning bara rensar det cachelagrade innehållet på CDN Edge-servrarna.  Alla underordnade cacheminnen, till exempel proxyservrar och lokala webbläsares cacheminnen, kan fortfarande innehålla en cachelagrad kopia av filen.  Det är viktigt att du kommer ihåg detta när du ställer in en filtime-to-Live-fil.  Du kan tvinga en efterföljande klient att begära den senaste versionen av filen genom att ge den ett unikt namn varje gång du uppdaterar den eller genom att dra nytta av [cachelagring av frågesträngar](cdn-query-string.md).  
> 
> 

Den här självstudien vägleder dig genom att rensa till gångar från alla Edge-noder i en slut punkt.

## <a name="walkthrough"></a>Genomgång
1. I [Azure Portal](https://portal.azure.com)bläddrar du till den CDN-profil som innehåller den slut punkt som du vill rensa.
2. Klicka på knappen Rensa på bladet CDN-profil.
   
    ![Bladet CDN-profil](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Bladet rensa öppnas.
   
    ![Bladet rensa CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. På bladet rensa väljer du den tjänst adress som du vill rensa i list rutan URL.
   
    ![Rensa formulär](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Du kan också öppna bladet rensa genom att klicka på knappen **ta bort** på BLADet CDN-slutpunkt.  I så fall fylls **URL** -fältet i automatiskt med tjänst adressen för den angivna slut punkten.
   > 
   > 
4. Välj vilka till gångar du vill rensa från Edge-noderna.  Om du vill rensa alla till gångar klickar du på kryss rutan **Rensa alla** .  Annars skriver du sökvägen till varje till gång som du vill tömma i text rutan **sökväg** . Under formaten stöds i sökvägen.
    1. **Enkel URL-rensning**: rensa enskilda till gångar genom att ange den fullständiga URL: en, med eller utan fil namns tillägget, t `/pictures/strasbourg.png` . ex. `/pictures/strasbourg`
    2. **Jokertecken**: asterisk ( \* ) kan användas som jokertecken. Rensa alla mappar, undermappar och filer under en slut punkt med `/*` sökvägen eller ta bort alla undermappar och filer under en särskild mapp genom att ange mappen följt av `/*` , t. ex., `/pictures/*` .  Observera att det inte finns stöd för att rensa jokertecken i Azure CDN från Akamai. 
    3. **Rot domän rensning**: Rensa roten för slut punkten med "/" i sökvägen.
   
   > [!TIP]
   > Sökvägar måste anges för rensning och måste vara en relativ URL som passar följande [reguljära uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Rensa alla** och **Rensa jokertecken** stöds inte av **Azure CDN från Akamai** för närvarande.
   > > Ta bort enskild URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Frågesträng `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Jokertecken har rensats `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Text rutor med fler **sökvägar** visas när du har angett text så att du kan skapa en lista över flera till gångar.  Du kan ta bort till gångar från listan genom att klicka på knappen med tre punkter (...).
   > 
5. Klicka på knappen **Rensa** .
   
    ![Knappen Rensa](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Rensnings förfrågningar tar cirka 10 minuter att bearbeta med **Azure CDN från Microsoft**, ungefär 2 minuter med **Azure CDN från Verizon** (standard och Premium) och cirka 10 sekunder med **Azure CDN från Akamai**.  Azure CDN har en gräns på 100 samtidiga rensnings begär Anden vid en specifik tidpunkt på profil nivån. 
> 
> 

## <a name="see-also"></a>Se även
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](cdn-preload-endpoint.md)
* [Azure CDN REST API referens-rensa eller för inläsning av en slut punkt](/rest/api/cdn/cdn/endpoints)

