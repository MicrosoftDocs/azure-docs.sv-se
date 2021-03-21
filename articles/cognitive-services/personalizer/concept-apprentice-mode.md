---
title: Lärlings läge – Personanpassare
description: Lär dig hur du använder lärlings läget för att få förtroende för en modell utan att ändra någon kod.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385038"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Använd lärlings läget för att träna Personanpassare utan att påverka ditt befintliga program

På grund av det **verkliga** förstärknings inlärnings typen kan en personanpassa modell bara tränas i en produktions miljö. När du distribuerar ett nytt användnings fall fungerar inte anpassnings modellen effektivt eftersom det tar tid för modellen att vara tillräckligt tränad.  **Lärlings läget** är ett inlärnings beteende som underlättar den här situationen och gör att du kan få förtroende i modellen – utan att utvecklaren ändrar någon kod.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Vad är lärlings läge?

På liknande sätt som en lärlings expert lär sig från en huvud server och erfarenheten kan bli bättre. Lärlings läget är ett _beteende_ som gör att du kan lära dig mer genom att iaktta de resultat som erhålls från befintlig program logik.

Personanpassa tågen genom att mimicking samma utdata som programmet. Som ett mer händelse flöde kan en Personanpassare _fånga upp_ till det befintliga programmet utan att påverka den befintliga logiken och resultatet. Mått, som är tillgängliga från Azure Portal och API, hjälper dig att förstå prestandan som modellen lär sig.

När en utvecklare har lärt sig och uppnått en viss nivå av förståelse kan utvecklaren ändra beteendet från lärlings läge till onlineläge. Vid detta tillfälle börjar Personanpassaren att påverka åtgärderna i rang-API: et.

## <a name="purpose-of-apprentice-mode"></a>Syftet med lärlings läget

Lärlings läget ger dig förtroende för tjänsten personanpassa och dess funktioner för maskin inlärning och ger en garanti för att tjänsten skickar information som kan hämtas från – utan att riskera online-trafik.

De två huvudsakliga orsakerna till att använda lärlings läget är:

* Att förhindra **kall startar**: lärlings läget hjälper till att hantera och utvärdera kostnaden för den nya modellens inlärnings tid – när den inte returnerar den bästa åtgärden och inte uppnår en lämplig effektivitets nivå på cirka 60-80%.
* **Verifierar åtgärds-och kontext funktioner**: funktioner som skickas i åtgärder och kontext kan vara otillräckliga eller felaktiga, för mycket, felaktiga eller för att träna personanpassare att uppnå den perfekta effektivitets takten. Använd [funktions utvärderingar](concept-feature-evaluation.md) för att hitta och åtgärda problem med funktioner.

## <a name="when-should-you-use-apprentice-mode"></a>När bör du använda lärlings läge?

Använd lärlings läget för att träna Personanpassare att förbättra sin effektivitet genom följande scenarier samtidigt som användarna inte påverkas av Personanpassan:

* Du implementerar Personanpassaren i ett nytt användnings fall.
* Du har markant ändrat de funktioner som du skickar i kontext eller åtgärder.
* Du har betydligt ändrat när och hur du beräknar belöningar.

Lärlings läget är inte ett effektivt sätt att mäta inverkarens Personanpassare har på belönings poäng. Om du vill mäta hur effektiva anpassningar är vid valet av bästa möjliga åtgärd för varje rang anrop använder du [offline-utvärdering](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Vem bör använda lärlings läget?

Lärlings läget är användbart för utvecklare, data forskare och affärs besluts fattare:

* **Utvecklare** kan använda lärlings läget för att se till att API: er för rang och belöning används korrekt i programmet och att funktioner som skickas till en personanpassare från programmet inte innehåller några buggar, eller icke-relevanta funktioner som ett timestamp-eller UserID-element.

* **Data** experter kan använda lärlings läget för att kontrol lera att funktionerna är effektiva att träna de personliga modellerna, att svars tiden för belöningen inte är för lång eller kort.

* **Företags besluts fattare** kan använda lärlings läget för att bedöma möjligheten för personligt anpassade företag att förbättra resultaten (d.v.s. förmåner) jämfört med befintlig affärs logik. Detta gör det möjligt för dem att fatta ett informerat beslut som påverkar användar upplevelsen, där verkliga intäkter och användarens belåtenhet är i spel.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Jämföra beteenden – lärlings läge och online-läge

Lär dig om i lärlings läge skiljer sig från online-läget på följande sätt.

|Område|Lärlingsläge|Online-läge|
|--|--|--|
|Påverkan på användar upplevelsen|Du kan använda ett befintligt användar beteende för att träna Personanpassare genom att låta den se till att den fungerar (inte påverkar) vad din **standard åtgärd** skulle ha och den erhållna belöningen. Det innebär att användarnas upplevelse och att affärs resultaten från dem inte påverkas.|Visa topp åtgärd som returnerades från rang anrop för att påverka användar beteendet.|
|Inlärnings hastighet|Personanpassare kommer att lära sig långsammare i lärlings läge än vid inlärning i online-läge. Lärlings läget kan bara lära sig genom att känna till de förmåner som erhålls av din **standard åtgärd**, vilket begränsar inlärnings hastigheten, eftersom ingen utforskning kan utföras.|Lär dig snabbare eftersom det kan utnyttja den aktuella modellen och utforska nya trender.|
|Tak för inlärnings effektivitet|En personanpassare kan approximera, mycket sällan matcha, och aldrig överskrida prestanda för din bas affärs logik (den totala belöningen som uppnåtts av **standard åtgärden** för varje rang anrop). Detta ungefärligt tak minskas av utforskning. Med utforskningen vid 20% är det mycket osannolikt att prestandan för lärlings läge överstiger 80% och 60% är ett rimligt mål för att reglera onlineläge.|En personanpassare bör överskrida program bas linjen, och med tiden där den stannar, bör du utföra en offline-utvärdering och funktions utvärdering för att fortsätta att få förbättringar i modellen. |
|Ranka API-värde för rewardActionId|Användarnas upplevelse påverkas inte, eftersom _rewardActionId_ alltid är den första åtgärd som du skickar i rang förfrågan. Med andra ord syns inte rang-API: et för ditt program i lärlings läge. Belönings-API: er i programmet bör inte ändra hur det använder sig av belönings-API: et mellan ett läge och ett annat.|Användarens upplevelse ändras av _rewardActionId_ som används av personanpassare för ditt program. |
|Utvärderingar|Personanpassaren är en jämförelse av belönings summorna som din standard affärs logik hämtar, och den totala belönings funktionen för belöningar skulle bli om i onlineläge vid det här läget. Det finns en jämförelse i Azure Portal för resursen|Utvärdera tillverkarens effektivitet genom att köra [offline-utvärderingar](concepts-offline-evaluation.md)som gör att du kan jämföra den totala personliga belönings tjänsten med de potentiella fördelarna med programmets bas linje.|

En kommentar om lärlings läges effektivitet:

* Personanpassare effektivitet i lärlings läge uppnås sällan i nära 100% av programmets bas linje. och överskrider aldrig.
* Bästa praxis skulle vara att inte försöka att komma till 100% för uppnående; men ett intervall på 60% – 80% ska vara mål beroende på användnings fallet.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Använda lärlings läget för att träna med historiska data

Om du har en stor mängd historiska data vill du använda för att träna tränare, du kan använda lärlings läget för att spela upp data via Personanpassaren.

Konfigurera Personanpassaren i lärlings läge och skapa ett skript som anropar rang med åtgärder och kontext funktioner från historiska data. Anropa belönings-API: t baserat på dina beräkningar av posterna i dessa data. Du behöver cirka 50 000 historiska händelser för att se resultatet, men 500 000 rekommenderas för högre tillförlitlighet i resultaten.

När du tränar från historiska data rekommenderar vi att data som skickas i (funktioner för kontext och åtgärder, deras layout i JSON som används för ranknings förfrågningar och beräkningen av belöning i den här tränings data uppsättningen), matchar data (funktioner och beräkning av belöning) som är tillgängliga från det befintliga programmet.

Offline-och post-fakta-data tenderar att vara mer ofullständiga och noisier och skiljer sig åt i format. Även om det är möjligt att utbildning från historiska data är möjlig, kan det vara tveksamt att göra det och inte en bra förväntare för att lära sig att lära sig, särskilt om funktionerna varierar mellan tidigare data och det befintliga programmet.

I allmänhet är det en mer effektiv väg att ha en effektiv modell, med mindre arbete, data teknik och rensnings arbete, jämfört med utbildning med historiska data, vilket är en mer effektiv väg för att få en effektiv modell.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Använda lärlings läge jämfört med A/B-tester

Det är bara användbart att göra ett/B-test av anpassnings behandlingar när den har verifierats och lära sig i onlineläge. I lärlings läge används endast **standard åtgärden** , vilket innebär att alla användare skulle kunna se kontroll upplevelsen effektivt.

Även om Personanpassaren bara är den här _hanteringen_, finns samma utmaning när du verifierar att data är bra för träna personanpassare. Lärlings läget kan användas i stället, med 100% trafik, och med alla användare som får kontrollen (inte påverkad) upplevelse.

När du har ett användnings fall med hjälp av personanpassa och utbildning online kan du med ett/B-experiment göra kontrollerade kohorter och vetenskaplig jämförelse av resultat som kan vara mer komplexa än de signaler som används för förmåner. Ett exempel på frågan A/B-test kan svara: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aktiva och inaktiva händelser](concept-active-inactive-events.md)
