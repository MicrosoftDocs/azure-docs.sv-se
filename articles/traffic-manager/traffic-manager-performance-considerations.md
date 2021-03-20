---
title: Prestanda överväganden för Azure Traffic Manager | Microsoft Docs
description: Förstå prestanda på Traffic Manager och testa prestanda för din webbplats när du använder Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186183"
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

På den här sidan förklaras prestanda överväganden med Traffic Manager. Föreställ dig följande scenario:

Du har instanser av webbplatsen i regionerna väst och Asienöstra. En av instanserna kan inte utföra hälso kontrollen för Traffic Manager-avsökningen. Program trafiken dirigeras till den felfria regionen. Den här redundansväxlingen förväntas, men prestanda kan vara ett problem baserat på trafik fördröjningen som nu reser till en avlägsen region.

## <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Den enda prestanda påverkan som Traffic Manager kan ha på din webbplats är den första DNS-sökningen. En DNS-begäran för namnet på din Traffic Manager profil hanteras av den Microsoft DNS-rotserver som är värd för trafficmanager.net-zonen. Traffic Manager fyller i och regelbundet uppdaterar Microsofts DNS-rotkataloger baserat på Traffic Manager princip och avsöknings resultat. Så även under den inledande DNS-sökningen skickas inga DNS-frågor till Traffic Manager.

Traffic Manager består av flera komponenter: DNS-namnservrar, en API-tjänst, lagrings skiktet och en tjänst för slut punkts övervakning. Om en Traffic Manager tjänst komponent Miss lyckas, sker ingen påverkan på det DNS-namn som är associerat med din Traffic Managers profil. Posterna i Microsoft DNS-servrarna är oförändrade. Slut punkts övervakning och DNS-uppdatering sker dock inte. Därför kan Traffic Manager inte uppdatera DNS för att peka på platsen för redundans när den primära platsen slutar fungera.

DNS-namnmatchning är snabbt och resultaten cachelagras. Hastigheten för den inledande DNS-sökningen beror på vilka DNS-servrar klienten använder för namn matchning. En klient kan normalt slutföra en DNS-sökning inom ~ 50 ms. Resultaten av sökningen cachelagras under varaktigheten för DNS-TTL (Time-to-Live). Standard-TTL för Traffic Manager är 300 sekunder.

Trafiken flödar inte genom Traffic Manager. När DNS-sökningen är klar har klienten en IP-adress för en instans av webbplatsen. Klienten ansluter direkt till adressen och passerar inte genom Traffic Manager. Den Traffic Manager principen du väljer påverkar inte DNS-prestandan. En prestanda cirkulations metod kan dock påverka program upplevelsen negativt. Om principen exempelvis omdirigerar trafik från Nordamerika till en instans som finns i Asien, kan nätverks fördröjningen för dessa sessioner vara ett prestanda problem.

## <a name="measuring-traffic-manager-performance"></a>Mäta Traffic Manager prestanda

Det finns flera webbplatser som du kan använda för att förstå prestanda och beteende för en Traffic Managers profil. Många av dessa webbplatser är kostnads fria men kan ha begränsningar. Vissa webbplatser erbjuder förbättrad övervakning och rapportering för en avgift.

Verktygen på dessa platser mäter DNS-fördröjning och visar de matchade IP-adresserna för klient platser runtom i världen. De flesta av dessa verktyg cachelagrar inte DNS-resultaten. Därför visar verktygen hela DNS-sökningen varje gången ett test körs. När du testar från din egen klient får du bara fullständig DNS-sökprestanda en gång under TTL-tiden.

## <a name="sample-tools-to-measure-dns-performance"></a>Exempelverktyg för att mäta DNS-prestanda

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS erbjuder många prestanda verktyg. Med verktyget för DNS-jämförelse kan du se hur lång tid det tar att matcha ditt DNS-namn och hur det jämförs med andra DNS-tjänstleverantörer.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Ett av de enklaste verktygen är WebSitePulse. Ange URL: en för att se DNS-matchningstid, första byte, senaste byte och annan prestanda statistik. Du kan välja mellan tre olika test platser. I det här exemplet ser du att den första körningen visar att DNS-sökning tar 0,204 SEK.

    ![Skärm bild som visar "WebSitePulse"-verktyget med Sök resultatet "DNS" markerat.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Eftersom resultaten cachelagras tar det andra testet för samma Traffic Manager-slutpunkt som DNS-sökningen tar 0,002 SEK.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Syntetisk Övervakare för CA-appen](https://asm.ca.com/en/checkit.php)

    Som tidigare kallades webbplats verktyget för titta-musen visar den här platsen DNS-matchnings tiden från flera geografiska regioner samtidigt. Ange URL: en för att se DNS-matchnings tid, anslutnings tid och hastighet från flera geografiska platser. Använd det här testet för att se vilken värdbaserad tjänst som returneras för olika platser runtom i världen.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Det här verktyget ger prestanda statistik för varje element på en webb sida. På fliken sid analys visas den procent andel av tiden som krävs för DNS-sökning.

* [Vad är min DNS?](https://www.whatsmydns.net/)

    Den här platsen gör en DNS-sökning från 20 olika platser och visar resultaten på en karta.

* [Gräva webb gränssnitt](https://www.digwebinterface.com)

    Den här platsen visar mer detaljerad DNS-information, inklusive CNAME-och A-poster. Se till att kontrol lera "färga utdata" och "stats" under alternativ och välj "alla" under namnservrar.

## <a name="next-steps"></a>Nästa steg

[Om Traffic Manager Traffic routing-metoder](traffic-manager-routing-methods.md)

[Testa dina Traffic Manager-inställningar](traffic-manager-testing-settings.md)

[Åtgärder i Traffic Manager (REST-API-referens)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager-cmdletar](/powershell/module/az.trafficmanager)