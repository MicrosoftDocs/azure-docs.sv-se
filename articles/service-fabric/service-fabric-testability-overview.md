---
title: Översikt över fel analys tjänster
description: I den här artikeln beskrivs fel analys tjänsten i Service Fabric för inducing-fel och körning av test scenarier mot dina tjänster.
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 4e879b0b39d58f115b4661d47cf2ae564ba14e5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531385"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introduktion till fel analys tjänsten
Fel analys tjänsten är utformad för att testa tjänster som bygger på Microsoft Azure Service Fabric. Med fel analys tjänsten kan du orsaka meningsfulla fel och köra fullständiga test scenarier mot dina program. Dessa fel och scenarier använder och validerar de många tillstånd och över gångar som en tjänst kommer att uppleva under hela sin livs längd, allt på ett kontrollerat, säkert och konsekvent sätt.

Åtgärder är enskilda fel som riktar sig mot en tjänst för att testa den. En tjänst utvecklare kan använda dessa som bygg stenar för att skriva komplicerade scenarier. Exempel:

* Starta om en nod för att simulera ett antal situationer där en dator eller virtuell dator startas om.
* Flytta en replik av en tillstånds känslig tjänst för att simulera belastnings utjämning, redundans eller program uppgradering.
* Anropa kvorum i en tillstånds känslig tjänst för att skapa en situation där Skriv åtgärder inte kan fortsätta eftersom det inte finns tillräckligt med säkerhets kopior eller sekundära repliker för att acceptera nya data.
* Anropa data förlust i en tillstånds känslig tjänst för att skapa en situation där alla minnes minnes status är helt rensade.

Scenarier är komplexa åtgärder som består av en eller flera åtgärder. Fel analys tjänsten innehåller två inbyggda fullständiga scenarier:

* Kaos-scenario
* Failover-scenario

## <a name="testing-as-a-service"></a>Testa som en tjänst
Tjänsten fel analys är en Service Fabric system tjänst som startas automatiskt med ett Service Fabric-kluster. Den här tjänsten fungerar som värd för fel inmatning, test scenario körning och hälso analys. 

![Fel analys tjänst][0]

När en fel åtgärd eller ett test scenario startas skickas ett kommando till fel analys tjänsten för att köra fel åtgärden eller test scenariot. Tjänsten fel analys är tillstånds känslig så att den kan köra fel och scenarier på ett tillförlitligt sätt och verifiera resultaten. Ett långvarigt test scenario kan till exempel köras tillförlitligt av fel analys tjänsten. Eftersom test körs i klustret kan tjänsten undersöka klustrets tillstånd och dina tjänster för att ge mer djupgående information om fel.

## <a name="testing-distributed-systems"></a>Testa distribuerade system
Service Fabric gör jobbet att skriva och hantera distribuerade skalbara program betydligt enklare. Tjänsten fel analys gör testningen av ett distribuerat program på ett liknande sätt. Det finns tre huvudsakliga problem som måste lösas vid testning:

1. Simulera/generera fel som kan uppstå i verkliga scenarier: en av de viktiga aspekterna av Service Fabric är att den gör det möjligt för distribuerade program att återställa från olika fel. Men för att testa att programmet kan återställas från dessa fel behöver vi en mekanism för att simulera/generera dessa verkliga fel i en kontrollerad test miljö.
1. Möjligheten att generera korrelerade problem: grundläggande problem i systemet, t. ex. nätverks problem och maskin haverier, är enkla att producera individuellt. Att generera ett stort antal scenarier som kan uppstå i verkligheten som ett resultat av att interaktionen mellan dessa enskilda haverier är icke-trivial.
1. Enhetlig upplevelse mellan olika utvecklings-och distributions nivåer: det finns många fel insprutnings system som kan utföra olika typer av fel. Upplevelsen i alla dessa är emellertid dåligt när du flyttar från scenarier med en enda utvecklare, till att köra samma tester i stora test miljöer för att använda dem för test i produktionen.

Även om det finns många mekanismer för att lösa dessa problem, ett system som gör samma sak med nödvändiga garantier – hela vägen från en utvecklings miljö i ett enda fält, för att testa i produktions kluster – saknas. Tjänsten fel analys hjälper programutvecklarna att koncentrera sig på att testa sin affärs logik. Fel analys tjänsten innehåller alla funktioner som behövs för att testa interaktionen för tjänsten med det underliggande distribuerade systemet.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulera/generera scenarier med verkliga problem
För att testa att ett distribuerat system är stabilt mot felen behöver vi en mekanism för att generera problem. I teorin verkar det enkelt att generera ett fel som en nod, och det påträffas av samma uppsättning konsekvens problem som Service Fabric försöker lösa. Om vi t. ex. vill stänga av en nod är det arbets flöde som krävs följande:

1. Från klienten utfärdar du en begäran om att stänga noden.
1. Skicka begäran till den högra noden.
   
    a. Om noden inte hittas bör den inte fungera.
   
    b. Om noden hittas ska den bara returnera om noden stängs av.

För att kontrol lera felet från ett test perspektiv måste testet veta att när det här felet induceras uppstår felet. Den garanti som Service Fabric tillhandahåller är att antingen noden stängs av eller var avstängd när kommandot nådde noden. I båda fallen bör testet kunna bero på ett korrekt tillstånd och lyckas eller Miss lyckas korrekt i verifieringen. Ett system som implementeras utanför Service Fabric för att utföra samma uppsättning fel kan innebära många nätverks-, maskin-och program varu problem, vilket skulle hindra det från att tillhandahålla föregående garantier. I närvaro av de problem som angavs tidigare, kommer Service Fabric att konfigurera om kluster tillstånd för att lösa problemen, och därför kommer fel analys tjänsten fortfarande att kunna ge rätt uppsättning garantier.

### <a name="generating-required-events-and-scenarios"></a>Genererar nödvändiga händelser och scenarier
Det är svårt att börja med att skapa korrelerade problem på ett konsekvent sätt, men det är ännu svårare att skapa korrelerade haverier. En data förlust sker till exempel i en tillstånds känslig tjänst när följande saker händer:

1. Endast ett Skriv kvorum av replikerna skapas på replikering. Alla sekundära repliker fördröjningar bakom den primära.
1. Skriv kvorumet slutar fungera på grund av att replikerna går ned (på grund av ett kod paket eller en nod som går ned).
1. Det går inte att säkerhetskopiera eftersom data för replikerna förloras (på grund av skada eller dator avbildning).

Dessa korrelerade haverier sker i verkligheten, men inte så ofta som enskilda haverier. Möjligheten att testa för de här scenarierna innan de inträffar i produktionen är kritisk. Ännu viktigare är möjligheten att simulera dessa scenarier med produktions arbets belastningar i kontrollerade förhållanden (mitt på dagen med alla tekniker på däck). Det är mycket bättre än att det sker för första gången i produktion kl. 2:00

### <a name="unified-experience-across-different-environments"></a>Enhetlig upplevelse i olika miljöer
Praxis har traditionellt varit att skapa tre olika uppsättningar upplevelser, en för utvecklings miljön, en för test och en för produktion. Modellen var:

1. I utvecklings miljön skapar du tillstånds över gångar som tillåter enhets test av enskilda metoder.
1. I test miljön genererar du felen för att tillåta slut punkt till slut punkts test som använder olika typer av problem.
1. Behåll produktions miljöns pristine för att förhindra eventuella icke-naturliga felaktigheter och se till att det är mycket enkelt att reagera på mänskligt.

I Service Fabric, via fel analys tjänsten, föreslår vi att sätta detta runt och använda samma metod från utvecklings miljön till produktion. Det finns två sätt att åstadkomma detta:

1. Om du vill inducera kontrollerade fel kan du använda API: erna för fel analys tjänsten från en enda miljö, hela vägen till produktions kluster.
1. Om du vill ge klustret en feber som orsakar automatisk induktion av fel, använder du fel analys tjänsten för att generera automatiska fel. Genom att kontrol lera frekvensen av misslyckade konfigurationer kan samma tjänst testas på olika sätt i olika miljöer.

Med Service Fabric, även om skalningen av felen skulle vara olika i olika miljöer, skulle de faktiska mekanismerna vara identiska. Detta gör det möjligt att använda en mycket snabbare kod till distribution-pipeline och möjligheten att testa tjänsterna under verkliga belastningar.

## <a name="using-the-fault-analysis-service"></a>Använda fel analys tjänsten
**C#**

Tjänst funktioner för fel analys finns i namn området system. Fabric i Microsoft. ServiceFabric NuGet-paketet. Om du vill använda funktionerna för fel analys tjänsten inkluderar du NuGet-paketet som en referens i projektet.

**PowerShell**

Om du vill använda PowerShell måste du installera Service Fabric SDK. När SDK har installerats läses ServiceFabric PowerShell-modulen in automatiskt så att du kan använda den.

## <a name="next-steps"></a>Nästa steg
För att skapa färdiga molnbaserade tjänster, är det viktigt att garantera att tjänsterna är både före och efter distributionen och att de kan motstå problem i verkligheten. I dagens tjänster är det mycket viktigt att kunna förnya snabbt och flytta kod till produktion. Tjänsten fel analys hjälper tjänst utvecklare att göra exakt det.

Börja testa dina program och tjänster med hjälp av de inbyggda [test scenarierna](service-fabric-testability-scenarios.md), eller redigera dina egna test scenarier med hjälp av de [fel åtgärder](service-fabric-testability-actions.md) som tillhandahålls av tjänsten fel analys.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
