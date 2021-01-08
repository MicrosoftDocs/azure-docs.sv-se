---
title: Vad är Personanpassning?
description: Personanpassar är en molnbaserad tjänst som gör att du kan välja den bästa upplevelsen som ska visas för användarna, lära sig från deras real tids beteende.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personanpassare, Azure-personanpassare, maskin inlärning
ms.openlocfilehash: 33c1770c5c8722a55d8f1df4aff9b1637d903977
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028785"
---
# <a name="what-is-personalizer"></a>Vad är Personanpassning?

Azure-personanpassa är en molnbaserad tjänst som hjälper dina program att välja det bästa innehålls objektet för att visa dina användare. Du kan använda tjänsten personanpassa för att avgöra vilken produkt som ska föreslås till shoppare eller för att ta reda på den optimala positionen för en annons. När innehållet har visats för användaren övervakar programmet användarens reaktion och rapporterar en belönings Poäng tillbaka till tjänsten för anpassning. Detta säkerställer kontinuerlig förbättring av Machine Learning-modellen och personanpassa möjlighet att välja det bästa innehålls objektet baserat på den sammanhangsbaserade information som den tar emot.

> [!TIP]
> Innehåll är en informations enhet, till exempel text, bilder, URL, e-postmeddelanden eller något annat som du vill välja från och Visa för dina användare.

Innan du börjar kan du prova att lära dig [med den här interaktiva demonstrationen](https://personalizationdemo.azurewebsites.net/).

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hur väljer Personanpassare det bästa innehålls objektet?

Personanpassare använder **förstärknings inlärning** för att välja det bästa objektet (_åtgärd_) baserat på kollektivt beteende och belönings resultat för alla användare. Åtgärder är innehålls objekt, till exempel nyhets artiklar, vissa filmer eller produkter.

**Ranknings** anropet tar objektet Action, tillsammans med funktionerna i åtgärden och kontext funktionerna för att välja det översta objektet:

* **Åtgärder med funktioner** – innehålls objekt med funktioner som är speciella för varje objekt
* **Kontext funktioner** – funktioner för dina användare, deras kontext eller deras miljö när du använder din app

Rang anropet returnerar det ID för vilket innehålls objekt, __åtgärd__, som ska visas för användaren i fältet **belönings åtgärds-ID** .

Den __åtgärd__ som visas för användaren väljs med Machine Learning-modeller, som försöker maximera det totala antalet förmåner över tid.

### <a name="sample-scenarios"></a>Exempelscenarier

Låt oss ta en titt på några scenarier där Personanpassaren kan användas för att välja det bästa innehållet som ska renderas för en användare.

|Innehållstyp|Åtgärder (med funktioner)|Kontext funktioner|Returnerat åtgärds-ID för belöning<br>(Visa det här innehållet)|
|--|--|--|--|
|Nyhets lista|a. `The president...` (National, politiken, [text])<br>b. `Premier League ...` (global, sport, [text, bild, video])<br> c. `Hurricane in the ...` (regional, väder, [text, bild]|Enhetens Nyheter läses från<br>Månad eller säsong<br>|en `The president...`|
|Film lista|1. `Star Wars` (1977, [Action, Adventure, fantasi], George Lucas)<br>2. `Hoop Dreams` (1994, [dokument, idrotts], Steve Jonas<br>3. `Casablanca` (1942, [romantik, drama, War], Michael Curtiz)|Enhets filmen bevakas från<br>skärm storlek<br>Typ av användare<br>|3. `Casablanca`|
|Produkt lista|i. `Product A` (3 kg, $ $ $ $, leverera på 24 timmar)<br>ii. `Product B` (20 kg, $ $, 2 veckor som levereras med tull)<br>iii. `Product C` (3 kg, $ $ $, leverans på 48 timmar)|Enhets köp läses från<br>Utgifts nivå för användare<br>Månad eller säsong|ii. `Product B`|

Personanpassare använder förstärknings inlärning för att välja den enda bästa åtgärden som kallas för _belönings åtgärds-ID_. Machine Learning-modellen använder: 

* En utbildad modell – information som tidigare togs emot från tjänsten personanpassa som används för att förbättra Machine Learning-modellen
* Aktuella data-/regionsspecifika åtgärder med funktioner och kontext funktioner

## <a name="when-to-use-personalizer"></a>När du ska använda en Personanpassare

En persons **rang** - [API](https://go.microsoft.com/fwlink/?linkid=2092082) anropas varje gången ditt program presenterar innehåll. Detta kallas en **händelse** som anges med ett _händelse-ID_.

Personens **belönings** - [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) kan anropas i real tid eller fördröjs för att bättre passa din infrastruktur. Du fastställer belönings poängen utifrån dina affärs behov. Belönings poängen är mellan 0 och 1. Det kan vara ett enda värde, till exempel 1 för bra, och 0 för dåligt, eller ett tal som skapats av en algoritm som du skapar med tanke på dina affärs mål och mät värden.

## <a name="content-requirements"></a>Innehålls krav

Använd Personanpassare när ditt innehåll:

* Har en begränsad uppsättning åtgärder eller objekt (max ~ 50) för att välja från i varje anpassnings händelse. Om du har en större lista kan du [använda en rekommendations motor](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) för att minska listan till 50 objekt varje gången du anropar rang för tjänsten för anpassning.
* Innehåller information som beskriver det innehåll som du vill rangordna: _åtgärder med funktioner_ och _kontext funktioner_.
* Har minst ~ 1 – a innehålls relaterade händelser som är relaterade till att personanpassa ska vara effektiva. Om Personanpassaren inte får den lägsta trafik som krävs tar det längre tid för tjänsten att fastställa det enkla innehålls objektet.

Eftersom Personanpassaren använder samlad information i nära real tid för att returnera det enda bästa innehålls objektet, så är tjänsten inte:
* Behåll och hantera information om användar profiler
* Logga enskilda användares inställningar eller historik
* Kräv rensad och märkt innehåll

## <a name="how-to-design-for-and-implement-personalizer"></a>Så här utformar och implementerar du Personanpassare

1. [Utforma](concepts-features.md) och planera för innehåll, **_åtgärder_** och **_kontext_**. Fastställ belönings algoritmen för **_belönings_** poängen.
1. Varje [personanpassa resurs](how-to-settings.md) som du skapar betraktas som en inlärnings slinga. Slingan tar emot både rang-och belönings anrop för det innehållet eller användar upplevelsen.

    |Resurstyp| Syfte|
    |--|--|
    |[Lärlingsläge](concept-apprentice-mode.md) `E0`|Träna en personanpassa modell utan att påverka ditt befintliga program och distribuera sedan till onlineutbildning i en produktions miljö|
    |Standard `S0`|Utbildnings beteende online i en produktions miljö|
    |Kostnads fria `F0`| Testa inlärnings beteendet i en miljö som inte är en produktions miljö|

1. Lägg till en Personanpassare till ditt program, din webbplats eller ditt system:
    1. Lägg till ett **rang** anrop till personanpassaren i ditt program, din webbplats eller ditt system för att fastställa det bästa, enda _innehålls_ posten innan innehållet visas för användaren.
    1. Visa det bästa, enda _innehålls_ objekt, vilket är det returnerade _Åtgärds-ID: t för belöning_, till användare.
    1. Använd _affärs logik_ för att samla in information om hur användaren beter sig för att fastställa **belönings** poängen, till exempel:

    |Beteende|Beräknad belönings Poäng|
    |--|--|
    |Användaren har valt bästa, enskilt _innehålls_ objekt (ID för belönings åtgärd)|**1**|
    |Användaren har valt annat innehåll|**0**|
    |Användaren pausade, rullade runt på ett avgörande sätt innan du väljer bästa, enskilt _innehålls_ objekt (belönings ÅTGÄRDS-ID)|**0,5**|

    1. Lägg till ett **belönings** samtal som skickar en belönings Poäng mellan 0 och 1
        * Direkt efter att ha visat ditt innehåll
        * Eller någon gång senare i ett offline-system
    1. [Utvärdera din loop](concepts-offline-evaluation.md) med en offline-utvärdering efter en användnings period. Med en offline-utvärdering kan du testa och utvärdera effektiviteten för tjänsten personanpassa utan att ändra din kod eller påverka användar upplevelsen.

## <a name="complete-a-quickstart"></a>Slutför en snabb start

Vi erbjuder snabb starter i C#, Java Script och python. Varje snabb start är utformad för att lära dig grundläggande design mönster, och du kan köra kod på mindre än 10 minuter. 

* [Snabb start: så här använder du personanpassa klient biblioteket](./quickstart-personalizer-sdk.md)

När du har fått en chans att komma igång med tjänsten för anpassning kan du prova våra självstudier och lära dig hur du använder personanpassa i webb program, chatta robotar eller en Azure-anteckningsbok.

* [Självstudie: använda personanpassar i en .NET-webbapp](tutorial-use-personalizer-web-app.md)
* [Självstudie: använda en Personanpassare i en .NET Chat-robot](tutorial-use-personalizer-chat-bot.md)
* [Självstudie: använda en Personanpassare i en Azure-anteckningsbok](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>Referens 

* [/.NET-distribution. SDK för personanpassa C#](/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Personanpassa go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [SDK för anpassnings-Java Script](/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Personanpassa python SDK](/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [REST API:er](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här fungerar](how-personalizer-works.md) 
>  personanpassaren [Vad är förstärknings inlärning?](concepts-reinforcement-learning.md)
