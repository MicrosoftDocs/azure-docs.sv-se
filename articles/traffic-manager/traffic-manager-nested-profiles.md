---
title: Kapslade Traffic Manager profiler i Azure
titleSuffix: Azure Traffic Manager
description: I den här artikeln förklaras funktionen "nästlade profiler" i Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185588"
---
# <a name="nested-traffic-manager-profiles"></a>Kapslade Traffic Manager-profiler

Traffic Manager innehåller en mängd metoder för trafik dragning som gör att du kan styra hur Traffic Manager väljer vilken slut punkt som ska ta emot trafik från varje slutanvändare. Mer information finns i [Traffic Manager metoder för trafik dirigering](traffic-manager-routing-methods.md).

Varje Traffic Manager profil anger en enda metod för trafik dirigering. Det finns dock scenarier som kräver mer avancerad trafikroutning än routningen som tillhandahålls av en enda Traffic Manager profil. Du kan kapsla Traffic Manager profiler för att kombinera fördelarna med mer än en metod för trafikroutning. Med kapslade profiler kan du åsidosätta standard Traffic Manager beteende som stöder större och mer komplexa program distributioner.

I följande exempel visas hur du använder kapslade Traffic Manager profiler i olika scenarier.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exempel 1: kombinera "prestanda" och "viktad trafik routning"

Anta att du har distribuerat ett program i följande Azure-regioner: västra USA, Västeuropa och Asien, östra. Du använder Traffic Manager ' prestanda Traffic-routningsmetod ' för att distribuera trafik till den region som är närmast användaren.

![Single Traffic Manager-profil][4]

Anta nu att du vill testa en uppdatering av tjänsten innan du lyfter ut den mycket. Du vill använda metoden "viktad Traffic-routing" för att dirigera en liten procent andel av trafiken till test distributionen. Du ställer in test distributionen tillsammans med den befintliga produktions distributionen i Västeuropa.

Du kan inte kombinera både "viktad" och "prestanda trafik-routning i en enskild profil. För att stödja det här scenariot skapar du en Traffic Manager profil med hjälp av två Västeuropas slut punkter och "viktad" Traffic-routing-metod. Sedan lägger du till den här underordnade profilen som en slut punkt till den överordnade profilen. Den överordnade profilen använder fortfarande metoden för prestanda trafik-Routning och innehåller andra globala distributioner som slut punkter.

Följande diagram illustrerar det här exemplet:

![Kapslade Traffic Manager-profiler][2]

I den här konfigurationen distribuerar trafik som riktas via den överordnade profilen trafik mellan regioner normalt. I Europa Västeuropa distribuerar den kapslade profilen trafiken till produktions-och test slut punkterna enligt tilldelade vikter.

När den överordnade profilen använder metoden "prestanda" för Traffic-routing måste varje slut punkt tilldelas en plats. Platsen tilldelas när du konfigurerar slut punkten. Välj den Azure-region som är närmast din distribution. Azure-regionerna är de plats värden som stöds av tabellen Internet latens. Mer information finns i [Traffic Manager "prestanda" för Traffic routing-metoden](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exempel 2: slut punkts övervakning i kapslade profiler

Traffic Manager aktivt övervakar hälsan för varje tjänst slut punkt. Om en slut punkt inte är felfri, Traffic Manager dirigerar användare till alternativa slut punkter för att bevara tjänstens tillgänglighet. Den här åtgärden för slut punkts övervakning och redundans gäller för alla metoder för trafik dirigering. Mer information finns i [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md). Slut punkts övervakning fungerar annorlunda för kapslade profiler. Med kapslade profiler utför inte den överordnade profilen hälso kontroller på den underordnade direkt. I stället används hälsan för den underordnade profilens slut punkter för att beräkna den övergripande hälsan för den underordnade profilen. Den här hälso informationen sprider den kapslade-hierarkin. Den överordnade profilen använder denna aggregerade hälsa för att avgöra om trafik ska dirigeras till den underordnade profilen. Se [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-nested-profiles) för all information om hälso övervakning av kapslade profiler.

I föregående exempel, Antag att produktions distributionen i Västeuropa inte kan utföras. Som standard dirigerar profilen "underordnad" all trafik till test distributionen. Om test distributionen också Miss lyckas, avgör den överordnade profilen att den underordnade profilen inte ska ta emot trafik eftersom alla underordnade slut punkter inte är felfria. Sedan distribuerar den överordnade profilen trafik till de andra regionerna.

![Redundansväxling av kapslad profil (standard beteende)][3]

Du kanske är nöjd med den här ordningen. Eller så kanske du är bekymrad om att all trafik för Västeuropa i Europa nu kommer till test distributionen i stället för en begränsad del av trafik. Oavsett hälso tillståndet för test distributionen vill du växla över till de andra regionerna när produktions distributionen i Västeuropa upphör att fungera. Om du vill aktivera redundans kan du ange parametern "MinChildEndpoints" när du konfigurerar den underordnade profilen som en slut punkt i den överordnade profilen. Parametern bestämmer det minsta antalet tillgängliga slut punkter i den underordnade profilen. Standardvärdet är ' 1 '. I det här scenariot anger du MinChildEndpoints-värdet till 2. Under det här tröskelvärdet anser den överordnade profilen att hela den underordnade profilen inte är tillgänglig och dirigerar trafik till andra slut punkter.

Följande bild visar den här konfigurationen:

![Redundansväxling av kapslad profil med ' MinChildEndpoints ' = 2][4]

> [!NOTE]
> Metoden Priority Traffic-routing distribuerar all trafik till en enda slut punkt. Det finns därför ett litet syfte i en annan MinChildEndpoints än 1 för en underordnad profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exempel 3: prioriterade redundans i routning av prestanda trafik

Standard beteendet för Traffic-metoden för trafik överföring är när du har slut punkter på olika geografiska platser som slutanvändarna dirigeras till den "närmast" slut punkten i förhållande till den lägsta nätverks fördröjningen.

Anta dock att du föredrar trafik växlingen i Västeuropa till västra USA, och endast direkt trafik till andra regioner när båda slut punkterna inte är tillgängliga. Du kan skapa den här lösningen med hjälp av en underordnad profil med Traffic-metoden Traffic-routing.

![Trafikroutning av prestanda med genomdriven redundans][6]

Eftersom slut punkten för Västeuropa har högre prioritet än den västra amerikanska slut punkten skickas all trafik till slut punkten Västeuropa när båda slut punkterna är online. Om Europa Västeuropa Miss lyckas dirigeras trafiken till västra USA. Med den kapslade profilen dirigeras trafiken till Asien, östra endast när både Västeuropa och västra USA kraschar.

Du kan upprepa det här mönstret för alla regioner. Ersätt alla tre slut punkterna i den överordnade profilen med tre underordnade profiler, som var och en ger en prioriterad redundansväxling.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exempel 4: kontrol lera "prestanda" i trafik flödet mellan flera slut punkter i samma region

Anta att metoden "prestanda" Traffic-routing används i en profil som har fler än en slut punkt i en viss region. Som standard fördelas trafik som är riktad till denna region jämnt över alla tillgängliga slut punkter i den regionen.

![Traffic routing Traffic routing in-region Traffic distribution (standard beteende)][7]

I stället för att lägga till flera slut punkter i Västeuropa, står dessa slut punkter i en separat underordnad profil. Den underordnade profilen läggs till i det överordnade objektet som den enda slut punkten i Västeuropa. Inställningarna i den underordnade profilen kan styra trafik distributionen med Västeuropa, Västeuropa genom att aktivera prioriterad eller viktad trafikroutning inom den regionen.

![Trafikroutning av prestanda med anpassad trafik distribution][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exempel 5: övervaknings inställningar per slut punkt

Anta att du använder Traffic Manager för att smidigt migrera trafik från en äldre lokal webbplats till en ny molnbaserad version som finns i Azure. För den äldre platsen vill du använda Start sidans URI för att övervaka plats hälsan. Men för den nya molnbaserade versionen implementerar du en anpassad övervaknings sida (sökväg '/Monitor.aspx ') som innehåller ytterligare kontroller.

![Traffic Manager slut punkts övervakning (standard beteende)][9]

Övervaknings inställningarna i en Traffic Manager profil gäller för alla slut punkter i samma profil. Med kapslade profiler använder du en annan underordnad profil per plats för att definiera olika övervaknings inställningar.

![Traffic Manager slut punkts övervakning med inställningar per slut punkt][10]

## <a name="faqs"></a>Vanliga frågor och svar

* [Hur gör jag för att konfigurera kapslade profiler?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Hur många kapslings nivåer stöder Traffic Manager?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Kan jag blanda andra slut punkts typer med kapslade underordnade profiler i samma Traffic Manager profil?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hur gäller fakturerings modellen för kapslade profiler?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Påverkas prestandan för kapslade profiler?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Hur beräknar Traffic Manager hälsan för en kapslad slut punkt i en överordnad profil?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Traffic Manager profiler](traffic-manager-overview.md)

Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png