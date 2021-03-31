---
title: 'Vanliga frågor och svar: SRE och DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Vanliga frågor och svar: förstå relationen mellan SRE och DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674271"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Vanliga frågor och svar: Vad är relationen mellan SRE och DevOps?

Det finns en uppsättning vanliga frågor som kommer runt förhållandet mellan platsens Tillförlitlighets teknik och DevOps, inklusive "hur är de samma? Hur skiljer de sig åt? Kan vi ha både i vår organisation? ". Den här artikeln försöker dela några av svaren som har erbjudits av SRE-och DevOps-communities som ger oss närmare förståelse för den här relationen.

## <a name="how-are-they-the-same"></a>Hur är de samma?

SRE och DevOps är både moderna drift metoder som har skapats och utvecklats som svar på utmaningar som ingår:

- en växande komplexitet i våra produktions miljöer och utvecklings processer
- ökande affärsberoende på kontinuerlig drift i sådana miljöer
- oförmågan att skala arbetsstyrkan linjärt med storleken på dessa miljöer
- behovet av att gå snabbare samtidigt som drifts stabiliteten hålls kvar

Båda drifts metodernas värde riktar sig mot ämnen som är viktiga för att hantera dessa utmaningar som övervakning/undersöknings-, automatiserings-, dokumentations-och samarbets verktyg för program utveckling.

Det finns betydande överlappningar i verktyg och arbets områden mellan SRE och DevOps. När _arbets boken för platsens tillförlitlighet_ försätts, ser SRE ut i samma saker som DevOps men av någon annan anledning. "

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Tre olika sätt att jämföra de två drifts metoderna

Likheter mellan SRE och DevOps är tydliga. Var det verkligen är intressant att de två skiljer sig åt eller avviker från varandra. Här erbjuder vi tre sätt att tänka på när det gäller relationen som ett sätt att sätta vissa Nuance i den här frågan. Du kanske inte accepterar svaren, men de ger en bra start plats för diskussion.

### <a name="class-sre-implements-interface-devops"></a>"klass SRE implementerar gränssnittet DevOps"

_Arbets boken för plats tillförlitlighet_ (som nämns i vår [lista över resurs böcker](../resources/books.md)) diskuterar SRE och DevOps i sitt första kapitel. I kapitlet används frasen "Class SRE implementerar gränssnitts DevOps" som dess under rubrik. Detta är avsett att föreslås (med hjälp av en fras som är avsedd för utvecklare) att SRE kan betraktas som en speciell implementering av DevOps-filosofin. Som kapitlet pekar ut är "DevOps relativt tyst med att köra åtgärder på en detaljerad nivå" medan SRE är betydligt mer proskriptiga i dess praxis. Ett möjligt svar på frågan om hur de två relaterarna är SRE kan betraktas som en av många möjliga implementeringar av DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE är tillförlitligt eftersom DevOps är till leverans

Den här jämförelsen är en liten muddied eftersom det finns flera definitioner för både SRE och DevOps, men det kan ändå vara användbart. Den börjar med frågan "om du behövde destillera varje åtgärds metod i ett eller två ord som återspeglar dess kärnor, vad skulle det vara?"

Om vi använder den här definitionen av SRE från [platsens Tillförlitlighets tekniks hubb](../index.yml):

> Site Reliability Engineering är ett teknikområde som är avsett att hjälpa en organisation att uppnå lämplig nivå av tillförlitlighet i deras system, produkter och tjänster.

sedan är det enkelt att säga ordet för SRE är "tillförlitlighet". Att ha det i mitten av namnet ger också några utmärkta bevis för detta anspråk.

Om vi använder den här definitionen av DevOps från [Azure DevOps Resource Center](/azure/devops/learn/):

> DevOps är en union av människor, processer och produkter för kontinuerlig värdeleverans till våra slutanvändare.

sedan kan en liknande destillation för DevOps bli "Delivery".

Därför är SRE tillförlitligt eftersom DevOps är att leverera.

### <a name="direction-of-attention"></a>Uppmärksamhet riktning

Det här svaret är citerat eller något paraphrased från ett bidrag av Thomas Limoncelli till _SRE_ -adressboken som nämns i [listan över resurs böcker](../resources/books.md). Han noterar att DevOps-teknikerna i stor utsträckning fokuserar på program varu utvecklings livs cykelns pipeline med tillfälliga produktions drifts ansvar samtidigt som SREs fokuserar på produktions åtgärder med tillfälliga SDLC pipeline-ansvar.

Men det är viktigt att han också ritar ett diagram som börjar med program utvecklings processen på en sida och produktions åtgärderna fungerar på den andra. De två är anslutna till den vanliga pipelinen som är byggd för att ta koden från en utvecklare, Shepherd den genom det önskade antalet tester och steg och sedan flytta koden till produktion.

Limoncelli noterar att DevOps-tekniker börjar i utvecklings miljön och automatiserar stegen mot produktion. När du är klar går de tillbaka för att optimera Flask halsar.

SREs, å andra sidan, fokuserar på produktions åtgärder och når djup i pipelinen som ett sätt att förbättra slut resultatet (i princip fungerar i motsatt riktning).

Det är skillnad i SRE-och DevOps-fokus som kan hjälpa dig att skilja dem åt.

## <a name="coexistence-in-the-same-organization"></a>Samexistens i samma organisation

Den slutliga fråga vi vill adressera är "kan du ha både SRE och DevOps i samma organisation?"

Svaret på den här frågan är en Emphatic "Ja!".

Vi hoppas att de tidigare svaren ger någon uppfattning om hur de två drifts metoderna överlappar och, om de inte överlappar, hur de kan komplettera i fokus. Organisationer med en etablerad DevOps-praxis kan experimentera med SRE-praxis på en liten skala (till exempel testa SLIs och SLO: erna) utan att du behöver göra något för att skapa SRE-positioner eller team. Det här är ett ganska vanligt SREt införande mönster.

## <a name="next-steps"></a>Nästa steg

Vill du veta mer om hur du lär dig mer om platsens tillförlitlighet eller DevOps? Kolla på vår [webbplats för Tillförlitlighets teknik](../index.yml) och [Azure DevOps Resource Center](/azure/devops/learn/).