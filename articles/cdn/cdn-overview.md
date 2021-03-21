---
title: Vad är CDN (Content Delivery Network)? - Azure | Microsoft Docs
description: Lär dig vad Azure CDN är och hur du använder det för att leverera innehåll med hög bandbredd.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: 54ee903b490e10f7ea4ca3bca91f742b5811404d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563310"
---
# <a name="what-is-a-content-delivery-network-on-azure"></a>Vad är CDN på Azure?
Ett nätverk för innehållsleverans (CDN, Content Delivery Network) är ett distribuerat nätverk av servrar som effektivt kan leverera webbinnehåll till användare. CDN ' lagrar cachelagrat innehåll på Edge-servrar i Point-of-Presence-platser (POP) som är nära slutanvändare, för att minimera svars tiden. 

Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen. Azure CDN möjliggör också snabbare dynamiskt innehåll, som inte kan cachelagras, genom att utnyttja olika nätverksoptimeringar med CDN POP-platser. Till exempel routningsoptimering för att kringgå BGP (Border Gateway Protocol).

Här är några exempel på fördelarna med att tillhandahålla webbplatstillgångar med Azure CDN:

* Bättre prestanda och förbättrad upplevelse för slutanvändarna, särskilt när de använder program där flera turer krävs för att läsa in innehållet.
* Skalning för effektiv hantering av plötsliga belastningstoppar, t.ex. i början av en produktlansering.
* Genom att distribuera användarförfrågningar och hämta innehåll direkt från edge-servrar skickas mindre trafik till ursprungsservern.

En lista över aktuella CDN-nodplatser finns i [POP-platser för Azure CDN](cdn-pop-locations.md).

## <a name="how-it-works"></a>Så här fungerar det
![Översikt över CDN](./media/cdn-overview/cdn-overview.png)

1. En användare (Alice) begär en fil (även kallad till gång) genom att använda en URL med ett särskilt domän namn, till exempel _&lt; slut &gt; punkts namn_. azureedge.net. Det här namnet kan vara slutpunktens värdnamn eller en anpassad domän. DNS omdirigerar begäran till den bästa tillgängliga POP-platsen, som vanligtvis är den POP-plats som är geografiskt närmast användaren.
    
2. Om filen inte finns i cachelagringen i någon edge-server på POP-platsen begär POP filen från ursprungsservern. Ursprungsservern kan vara en Azure-webbapp, en Azure-molntjänst, ett Azure Storage-konto eller en offentligt tillgänglig webbserver.
   
3. Ursprungsservern returnerar filen till en edge-server på POP.
    
4. Edge-servern cachelagrar filen på POP och returnerar den till användaren som ursprungligen begärde den (Alice). Filen förblir cachelagrad på edge-servern på POP tills TTL-värdet som angetts av dess HTTP-huvuden går ut. Om ursprungsservern inte angav någon TTL är standardvärdet sju dagar.
    
5. Ytterligare användare kan sedan begära samma fil med hjälp av samma URL so Alice använde, och kan också dirigeras till samma POP.
    
6. Om filens TTL inte har gått ut returnerar POP edge-servern filen direkt från cachen. Den här processen resulterar i en snabbare och mer responsiv användarupplevelse.

## <a name="requirements"></a>Krav
Om du vill använda Azure CDN måste du äga åtminstone en Azure-prenumeration. Du måste också skapa minst en CDN-profil (d.v.s. en samling CDN-slutpunkter). Varje CDN-slutpunkt representerar en specifik konfiguration av leveransbeteende och åtkomst för innehåll. Du kan organisera dina CDN-slutpunkter efter internetdomän, webbapp eller något annat kriterium genom att använda flera profiler. Eftersom [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) tillämpas på CDN-profilnivå måste du skapa flera CDN-profiler om du vill använda en blandning av prisnivåer. Information om Azure CDN-faktureringsstruktur finns i [Förstå Azure CDN-fakturering](cdn-billing.md).

### <a name="limitations"></a>Begränsningar
Varje Azure-prenumeration har standardgränser för följande resurser:
 - Antalet CDN-profiler som kan skapas.
 - Antalet slutpunkter som kan skapas i en CDN-profil. 
 - Antal anpassade domäner som kan mappas till en slutpunkt.

Mer information om CDN-prenumerationsbegränsningar finns i [CDN limits](../azure-resource-manager/management/azure-subscription-service-limits.md) (CDN-begränsningar).
    
## <a name="azure-cdn-features"></a>Funktioner i Azure CDN
Azure CDN erbjuder följande huvudfunktioner:

- [Acceleration av dynamisk webbplats](cdn-dynamic-site-acceleration.md)
- [CDN-cachelagringsregler](cdn-caching-rules.md)
- [HTTPS-stöd för egna domäner](cdn-custom-ssl.md)
- [Azure Diagnostics-loggar](cdn-azure-diagnostic-logs.md)
- [Filkomprimering](cdn-improve-performance.md)
- [Geo-filtrering](cdn-restrict-access-by-country.md)

En fullständig lista över vilka funktioner som varje Azure CDN-produkt stöder finns i avsnittet [Jämföra Azure CDN-produktfunktioner](cdn-features.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill komma igång med CDN kan du läsa avsnittet om hur du [skapar en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).
- Hantera dina CDN-slutpunkter via [Microsoft Azure Portal](https://portal.azure.com) eller med [PowerShell](cdn-manage-powershell.md).
- Läs mer om hur man automatiserar Azure CDN med [.NET](cdn-app-dev-net.md) eller [Node.js](cdn-app-dev-node.md).
