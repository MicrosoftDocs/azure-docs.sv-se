---
title: Real tids statistik i Azure CDN | Microsoft Docs
description: Real tids statistik ger real tids data om prestanda för Azure CDN när du levererar innehåll till dina klienter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887233"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Real tids statistik i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
I det här dokumentet beskrivs real tids statistik i Microsoft Azure CDN.  Den här funktionen ger real tids data, till exempel bandbredd, cache-status och samtidiga anslutningar till din CDN-profil vid leverans av innehåll till dina klienter. Detta möjliggör kontinuerlig övervakning av tjänstens hälso tillstånd när som helst, inklusive Go-Live-händelser.

Följande diagram är tillgängliga:

* [Bandbredd](#bandwidth)
* [Status koder](#status-codes)
* [Cache status](#cache-statuses)
* [Anslutningar](#connections)

## <a name="accessing-real-time-stats"></a>Åtkomst till real tids statistik
1. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil.
   
    ![Bladet CDN-profil](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera för CDN-profil bladet](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
3. Hovra över fliken **analys** och hovra över den utfällbara **statistiken i real tid** .  Klicka på ett **stort http-objekt**.
   
    ![Hanterings Portal för CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Real tids statistik diagrammet visas.

I varje diagram visas real tids statistik för det valda tidsintervallet, med början när sidan läses in.  Diagrammen uppdateras automatiskt med några sekunder.  Om du klickar på knappen **uppdatera diagram** rensas grafen, där den endast visar markerade data.

## <a name="bandwidth"></a>Bandbredd
![Diagram över bandbredd](./media/cdn-real-time-stats/cdn-bandwidth.png)

I diagrammet **bandbredd** visas mängden bandbredd som används för den aktuella plattformen under den valda tids perioden. Den skuggade delen av diagrammet indikerar bandbredds användning. Den exakta mängden bandbredd som används för närvarande visas direkt under linje diagrammet.

## <a name="status-codes"></a>Statuskoder
![Status kod diagram](./media/cdn-real-time-stats/cdn-status-codes.png)

Diagrammet **status kod** visar hur ofta vissa HTTP-svarskod inträffar under det valda tidsintervallet.

> [!TIP]
> En beskrivning av alternativen för HTTP-statuskod finns i [Azure CDN HTTP-statuskod](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

En lista med HTTP-statuskod visas direkt ovanför grafen. I den här listan visas varje status kod som kan inkluderas i linje diagrammet och det aktuella antalet förekomster per sekund för den status koden. Som standard visas en rad för var och en av dessa status koder i diagrammet. Du kan dock välja att bara övervaka status koder som har särskild betydelse för CDN-konfigurationen. Det gör du genom att markera önskade status koder och avmarkera alla andra alternativ och sedan klicka på **uppdatera diagram**. 

Du kan tillfälligt dölja loggade data för en viss status kod.  Från förklaringen direkt under grafen klickar du på den status kod som du vill dölja. Status koden kommer att döljas direkt från grafen. Om du klickar på den här status koden igen visas alternativet igen.

## <a name="cache-statuses"></a>Cache status
![Diagram över cache status](./media/cdn-real-time-stats/cdn-cache-status.png)

I diagrammet **cache status** visas hur ofta vissa typer av cache-status inträffar under det valda tidsintervallet. 

> [!TIP]
> En beskrivning av varje status kod alternativ för cache finns i [Azure CDN cache status koder](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

En lista med status koder för cache visas direkt ovanför grafen. I den här listan visas varje status kod som kan inkluderas i linje diagrammet och det aktuella antalet förekomster per sekund för den status koden. Som standard visas en rad för var och en av dessa status koder i diagrammet. Du kan dock välja att bara övervaka status koder som har särskild betydelse för CDN-konfigurationen. Det gör du genom att markera önskade status koder och avmarkera alla andra alternativ och sedan klicka på **uppdatera diagram**. 

Du kan tillfälligt dölja loggade data för en viss status kod.  Från förklaringen direkt under grafen klickar du på den status kod som du vill dölja. Status koden kommer att döljas direkt från grafen. Om du klickar på den här status koden igen visas alternativet igen.

## <a name="connections"></a>Anslutningar
![Diagram över anslutningar](./media/cdn-real-time-stats/cdn-connections.png)

Det här diagrammet visar hur många anslutningar som har upprättats till dina Edge-servrar. Varje begäran om en till gång som passerar genom vårt CDN resulterar i en anslutning.

## <a name="next-steps"></a>Nästa steg
* Få meddelanden [i real tid i Azure CDN](cdn-real-time-alerts.md)
* Gräv djupare med [avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
* Analysera [användnings mönster](cdn-analyze-usage-patterns.md)

