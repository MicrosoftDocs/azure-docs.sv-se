---
title: Azure Internet Analyzer | Microsoft Docs
description: Lär dig mer om Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360896"
---
# <a name="what-is-internet-analyzer-preview"></a>Vad är Internet Analyzer? (Förhandsversion)

Internet Analyzer är en mätnings plattform på klient sidan för att testa hur nätverks infrastruktur förändringar påverkar kundernas prestanda. Oavsett om du migrerar från lokala resurser till Azure eller om du utvärderar en ny Azure-tjänst kan du med Internet Analyzer få information från dina användares data och Microsofts omfattande analyser för att bättre förstå och optimera din nätverksarkitektur med Azure – innan du migrerar.

Internet Analyzer använder en liten JavaScript-klient som är inbäddad i ditt webb program för att mäta svars tiden från dina slutanvändare till din valda uppsättning nätverks mål, och vi kallar _slut punkter_. Med Internet Analyzer kan du konfigurera flera sida-vid-sida-tester, så att du kan utvärdera flera olika scenarier när infrastrukturen och kund behoven utvecklas. Internet Analyzer tillhandahåller anpassade och förkonfigurerade slut punkter, vilket ger dig både bekvämligheten och flexibiliteten att fatta betrodda prestanda beslut för dina slutanvändare. 


> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Snabba & anpassningsbara tester

Internet Analyzer riktar in sig på prestanda relaterade frågor för migrering av molnet, distribution till en ny eller ytterligare Azure-regioner eller för att testa nya program och innehålls leverans plattformar i Azure, till exempel [Azures front dörr](https://azure.microsoft.com/services/frontdoor/) och [Microsoft Azure CDN](https://azure.microsoft.com/services/cdn/). 

Varje test som du skapar i Internet Analyzer består av två slut punkter – slut punkt A och slut punkt B. slut punkt B: s prestanda analyseras i förhållande till slut punkt A. 

Du kan antingen konfigurera en egen anpassad slut punkt eller välja från en rad olika förkonfigurerade Azure-slutpunkter. Anpassade slut punkter ska användas för att utvärdera lokala arbets belastningar, dina instanser i andra moln leverantörer eller dina anpassade Azure-konfigurationer. Testerna kan bestå av två anpassade slut punkter; minst en anpassad slut punkt måste dock vara värd i Azure. Förkonfigurerade Azure-slutpunkter är ett snabbt och enkelt sätt att utvärdera prestanda hos populära Azure-nätverksklienter, till exempel Azures frontend-plattform, Azure Traffic Manager och Azure CDN. 

Under för hands versionen är följande förkonfigurerade slut punkter tillgängliga: 

* **Azure-regioner**
    * Brasilien, södra
    * Indien, centrala
    * Central US
    * Asien, östra
    * East US
    * Japan, västra
    * Europa, norra
    * Sydafrika, norra
    * Sydostasien 
    * Förenade Arabemiraten, norra
    * Storbritannien, västra  
    * Europa, västra
    * USA, västra 
    * USA, västra 2
* **Flera kombinationer av Azure-regioner** 
    * Östra USA, södra Brasilien 
    * USA, östra Asien, östra 
    * Västeuropa, södra Brasilien
    * Europa, västra Sydostasien
    * Västeuropa, Västeuropa, Nord Förenade Arabemiraten
    * Västra USA, östra USA 
    * Västra USA, Västeuropa
    * Västra USA, Förenade Arabemiraten Nord
    * Västeuropa, Västeuropa, Förenade Arabemiraten, norra, Sydostasien
    * Västra USA, Västeuropa, Asien, östra
    * Västra USA, norra Europa, Sydostasien, Nord, sydöstra Afrika, Nord Förenade Arabemiraten 
* **Azure + Azure-front dörr** – distribuerad på en eller flera kombinationer av Azure-regioner som anges ovan
* **Azure + Azure CDN från Microsoft** – distribuerat på en enskild kombination av Azure-regioner som anges ovan
* **Azure** -och azure-Traffic Manager – distribuerat på en kombination av flera Azure-regioner som anges ovan

## <a name="suggested-test-scenarios"></a>Föreslagna test scenarier 

För att hjälpa dig att fatta bästa möjliga prestanda för dina kunder kan du med Internet Analyzer utvärdera två slut punkter för din särskilda befolkning för slutanvändare. 

Även om Internet Analyzer kan svara på flera olika frågor är några av de vanligaste: 
* Vilken prestanda påverkas när du migrerar till molnet? 
    * *Föreslaget test: anpassad (din aktuella lokala infrastruktur) vs. Azure (en förkonfigurerad slut punkt)*
* Vad är värdet för att placera mina data i kanten jämfört med i ett Data Center? 
    *  *Föreslaget test: Azure-eller Azure-frontend, Azure vs. Azure CDN från Microsoft*
* Vad är prestanda fördelarna med Azures front dörr?
    *  *Föreslaget test: anpassad/Azure/CDN jämfört med Azures frontend-dörr*
* Vad är prestanda fördelarna med Azure CDN från Microsoft? 
    *  *Föreslaget test: Custom/Azure/AFD vs. Azure CDN från Microsoft*
* Hur Azure CDN från Microsoft stack? 
    *  *Föreslaget test: anpassad (annan CDN-slutpunkt) jämfört med Azure CDN från Microsoft*
* Vilket är det bästa molnet för din slut användar population i varje region? 
    *  *Föreslaget test: anpassad (annan moln tjänst) jämfört med Azure (en förkonfigurerad slut punkt)*

## <a name="how-it-works"></a>Så här fungerar det

Om du vill använda Internet Analyzer konfigurerar du en Internet Analyzer-resurs i Microsoft Azure-portalen och installerar den lilla JavaScript-klienten i ditt program. Klienten mäter svars tiden från dina slutanvändare till dina valda slut punkter genom att hämta en bild med en bild punkt över HTTPS. Efter att svars tiden har samlats in skickar klienten Mät data till Internet Analyzer.

När en användare besöker webb programmet väljer JavaScript-klienten två slut punkter för att mäta över alla konfigurerade tester. För varje slut punkt utför klienten ett _kallt_ och _varmt_ mått. _Kall_ mätningen medför ytterligare svars tid bredvid den rena nätverks fördröjningen mellan användaren och slut punkten, till exempel DNS-matchning, TCP-anslutningens hand SKAKNING och SSL/TLS-förhandling. Den _varma_ mätningen följer precis efter att _kall_ mätningen är klar och drar nytta av moderna webbläsares beständiga hantering av TCP-anslutningar för att få ett korrekt mått på svars tiden från slut punkt till slut punkt. När det stöds av användarens webbläsare används API: t för W3C-resursens tids inställning för korrekt mätnings tid. För närvarande används endast mått för varm fördröjning för analys.

![I diagrammet visas en slutanvändare som ansluter till en program server med-klienten Embedded och de två slut punkterna på Internet från flera alternativ. Användaren överför mätningar till Internet Analyzer.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Styrkort 

När ett test startar visas telemetridata i din Internet Analyzer-resurs under fliken styrkort. Dessa data sammanställs alltid. Använd följande filter för att ändra vilken vy av data som visas: 

* **Test:** Välj det test som du vill visa resultat för. Test data visas när det finns tillräckligt med data för att slutföra analysen – i de flesta fall inom 24 timmar. 
* **Tids period & slutdatum:** Internet Analyzer genererar tre styrkort varje dag – varje styrkort visar en annan sammanställnings tids period – 24 timmar före (dag), sju dagar före (vecka) och 30 dagar före (månad). Använd filtret "avsluta datum" för att välja den tids period som du vill se. 
* **Land:** Använd det här filtret om du vill visa data som är speciella för slutanvändare som är bosatta i ett land. Det globala filtret visar data i alla geografiska områden.  

Mer information om styrkort finns på sidan [tolka ditt styrkort](internet-analyzer-scorecard.md) . 


## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar din första Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).
* Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md). 
