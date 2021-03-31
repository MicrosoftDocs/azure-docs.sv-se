---
title: Arkitektur för Azures front dörr-routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå den globala vyns aspekt av den främre dörrens arkitektur.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449187"
---
# <a name="routing-architecture-overview"></a>Översikt över arkitektur för routning

När Azures front dörr tar emot dina klient förfrågningar, kommer den att göra något av två saker. Antingen besvarar du dem om du aktiverar cachelagring eller vidarebefordrar dem till lämplig program Server del som en omvänd proxy.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Välja miljön för den främre dörren för trafik dirigering (anycast)

Trafik som dirigeras till Azures frontend-miljöer använder [anycast](https://en.wikipedia.org/wiki/Anycast) för både DNS-(Domain Name System) och HTTP-trafik (Hypertext Transfer Protocol), som gör det möjligt för användar begär Anden att komma åt den närmaste miljön i minsta antal nätverks hopp. Den här arkitekturen erbjuder bättre svars tider för slutanvändare genom att maximera fördelarna med delade TCP. Front dörren ordnar sina miljöer i primär och återgång till "ringar". Den yttre ringen har miljöer som är närmare användare, vilket ger lägre fördröjning.  Den inre ringen har miljöer som kan hantera redundansväxlingen för den yttre ring miljön i händelse av att några problem inträffar. Den yttre ringen är det prioriterade målet för all trafik och den inre ringen är att hantera trafik spill från den yttre ringen. Varje klient dels värd eller domän som hanteras av en front dörr tilldelas en primär VIP (Virtual Internet Protocol-adresser), som lanseras av miljöer i både den inre och den yttre ringen. En reserv-VIP annonseras endast av miljöer i den inre ringen. 

Den här arkitekturen säkerställer att begär Anden från dina slutanvändare alltid når den närmaste front dörrens miljö. Även om den önskade front dörr miljön inte är felfri flyttas all trafik automatiskt till nästa närmaste miljö.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ansluta till en front dörrs miljö (delad TCP)

[Delad TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) är en teknik för att minska fördröjnings-och TCP-problem genom att bryta en anslutning som skulle innebära en hög fördröjnings tid i mindre delar. Med front dörr miljöer närmare slutanvändare, avslutas TCP-anslutningar inuti den främre dörren. En TCP-anslutning som har en stor tur och retur-tid till program Server delen delas upp i två separata anslutningar. Den "korta anslutningen" mellan slutanvändaren och den främre dörren innebär att anslutningen upprättas över tre korta turer i stället för tre långa turer, vilket leder till att en svars tid sparas. "Lång anslutning" mellan front dörrens miljö och Server del kan företableras och sedan återanvändas över andra slutanvändarnas begär Anden om att ansluta till anslutningen. Resultatet av delad TCP multipliceras när du etablerar en SSL/TLS-anslutning (Transport Layer Security) eftersom det finns fler tur och ingångar för att skydda en anslutning.

## <a name="processing-request-to-match-a-routing-rule"></a>Bearbetar begäran för att matcha en regel för routning
När du har upprättat en anslutning och slutfört en TLS-handskakning, är det första steget efter en begäran till en front dörr miljö som matchar den för att vidarebefordra regeln. Matchningen bestäms av konfigurationer på den främre dörren till vilken en viss regel för routning som matchar begäran till. Läs om hur front dörr [matchar väg matchning](front-door-route-matching.md) för att lära dig mer.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifiera tillgängliga Server delar i backend-poolen för Routningsprincipen
När front dörren har matchat en adresslisteregel för en inkommande begäran är nästa steg att hämta status för hälso avsökningen för den backend-pool som är kopplad till Routningsprincipen om det inte finns någon cachelagring. Läs om hur front dörren övervakar Server delens hälso tillstånd med hjälp av [hälso avsökningar](front-door-health-probes.md) för mer information.

## <a name="forwarding-the-request-to-your-application-backend"></a>Vidarebefordra begäran till din program Server del
Slutligen, förutsatt att cachelagring inte har kon figurer ATS, vidarebefordras användar förfrågan till Server delen "bästa" baserat på konfigurationen av din [routningsmetod](front-door-routing-methods.md) .

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
