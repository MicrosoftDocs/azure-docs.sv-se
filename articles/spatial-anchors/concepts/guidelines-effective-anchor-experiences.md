---
title: Rikt linjer för effektiva Anchor-upplevelser
description: Rikt linjer och överväganden för att skapa och hitta ankare effektivt med hjälp av Azure spatiala ankare.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95487511"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Skapa en effektiv fäst punkts upplevelse med hjälp av Azure spatiala ankare

Den här artikeln innehåller rikt linjer och överväganden för att hjälpa dig att skapa och hitta ankare på ett effektivt sätt med hjälp av Azure spatiala ankare.

## <a name="anchor-improvement-over-time"></a>Anchor-förbättringar över tid

Med Azures spatiala ankare försöker vi förbättra kvaliteten på kommande Sök åtgärder varje gång du hittar ankare. Vi gör detta med hjälp av de miljö data som samlas in för att utöka den visuella informationen på de ankare vi söker. Den här processen körs under huven och är en offline-optimering som körs av tjänsten Azure spatiala ankare för att optimera för din miljö. De ytterligare data som samlas in under varje åtgärd skapar en bättre förståelse för miljön. Detta förbättrar kvaliteten och gör att du bättre kan hitta ankare genom miljö förändringar, tids överföring och för användare som tittar på ankare från olika vinklar och perspektiv.

## <a name="good-anchors"></a>Lämpliga ankare

Medan Azures avstånds ankare försöker förbättra kvaliteten på ankare över tid, är det också viktigt att investera tid i att antingen utbilda eller guida användare i ditt användar gränssnitt (UX) för att skapa bra ankare. Genom att investera i skapa välgrundade fäst punkter kan du hjälpa slutanvändare att hitta ankare på ett tillförlitligt sätt:

- Mellan olika enheter.
- Vid olika tidpunkter.
- Under olika ljus förhållanden.
- Från önskade perspektiv inom utrymmet.

## <a name="static-and-dynamic-locations"></a>Statiska och dynamiska platser

En del av att designa ankaret är att välja platser. Kommer platserna att vara statiska och definierade av en administratör av utrymmet? Eller kommer de att vara dynamiska och definierade av användaren?

En Retail Store-hanterare kanske vill ha en statisk in-Store-upplevelse för att locka användare att besöka. Men utvecklaren av ett bräde-spel i mixat verklighet vill förmodligen låta användarna välja var de ska spelas upp.

För statiska platser kan du lära administratörerna att ägna tid åt att granska utrymmet med lämpliga ankare.

För dynamiska platser bör du tänka på hur du lär dig mer om hur du undervisar eller vägleder användare i ditt UX för att skapa lämpliga ankare.

## <a name="stable-visual-features"></a>Stabila visuella funktioner

Visuella spårnings system som används i Mixad verklighet och förhöjda Reality-enheter är beroende av visuella funktioner i miljön. För att få den mest pålitliga upplevelsen:

- Skapa ankare på platser som har stabila visuella funktioner (det *vill* säga funktioner som inte ändras ofta).

- Skapa *inte* ankare på stora tomma ytor som inte har några särskiljande egenskaper.

- Skapa *inte* ankare på mycket reflekterande material.

- Skapa *inte* fäst punkter på ytor där mönstret upprepas, till exempel mattor eller tapet.

![Exempel på en bra miljö för ankare och en dålig miljö för ankare](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Olika visnings perspektiv

När du skapar ett ankare bör du tänka på vilka personer som senare kommer att försöka hitta ankaret.

Överväg till exempel ett ankare mitt i ett rum som har två dörrar. Du vill förmodligen att användarna ska kunna ange rummet från dörren. När du skapar ankaret måste du skanna dess position från båda doorways. Du kan ändra perspektiv för att avbilda miljö data runt fäst punkten så att användarna kan hitta ankaret från båda dörrarna.

När du skapar ett ankare bör du i allmänhet söka igenom det från perspektiven för de personer som ska försöka hitta det. Så om du placerar virtuellt innehåll på en Sculpture är det klokt att gå runt Sculpture, medan du skannar den, när du skapar ankaret. Om ankaret finns i hörnet av ett rum finns det bara en riktning att gå från. När du skapar den här fäst punkten kan du Skanna den direkt från det här perspektivet.

## <a name="multiple-anchors"></a>Flera ankare

Belysningen kan göra skillnad i de visuella funktioner som en app identifierar. Ankare som skapats med stark naturlig belysning kan vara svåra att hitta i artificiellt ljus och vice versa.

Om du har det här problemet kan det hjälpa dig att skapa två ankare. Skapa en fäst punkt på samma plats och en annan i artificiellt ljus. Din app kan sedan fråga efter båda ankarena. När du har hittat något av fäst punkten har appen en attityd för ankaret.

På samma sätt kan flera ankare i miljöer där visuella funktioner ändras eftersom de flesta objekt flyttas. När ett ankare blir för svårt att hitta på grund av betydande ändringar i miljön kan du ersätta fäst punkten med en ny. Du kan göra detta, till exempel i en butik där layouten uppdateras varje månad.

## <a name="targets-and-rooms"></a>Mål och rum

I många fall är ett ankare en start punkt för appens upplevelse. Du ska gå igenom det här steget snabbt och tillförlitligt så att användarna kan komma igång med din upplevelse. Utgifts tid för hur användarna kommer att hitta dina ankare är ett viktigt design steg. Det är bra att tänka på att hitta ankare i termer av två breda scenarier: *mål* och *rum*.

### <a name="targets"></a>Targets

I mål scenariot är platsen för en fäst punkt välkänd. I en fiktiv målnings app med blandad verklighet placerar en användare till exempel en virtuell arbets yta på väggen. Hon instruerar de andra användarna i rummet att peka sina enheter på samma plats på väggen för att hitta ankaret och börja uppleva.

Ett annat exempel på ett mål scenario är att logga in ett kafé som läser, "söka efter avtal". Kaféet har placerat ett ankare här. När användarna genomsöker inloggningen letar de efter ankaret och anger den förhöjda verkligheten för att hitta erbjudanden på kaffe.

I mål scenariot kan foton hjälpa dig. Om du visar användare ett foto av det avsedda målet på sin enhet kan de snabbt identifiera vad som ska avsökas i verkligheten. Du kan till exempel hjälpa dina användare att komma in i det allmänna området för ett avsett mål med hjälp av GPS. När användaren kommer visar appen ett foto av målet. Användaren ser runt utrymmet, söker efter målet och söker efter ankaret.

![Illustration av ett ankare som visar ett foto av målet på en användares mobila enhet](./media/start-here-edit.png)

### <a name="rooms"></a>Kylrum

I rums scenariot anger användarna ett utrymme som bara känner till att det finns ett ankare här någonstans. Användarna genomsöker utrymmet med sin enhet och snabbt hittar fäst punkten.

Den här upplevelsen kräver vanligt vis att du skapar välhanterade ankare, som beskrivs i olika visnings perspektiv. Om du har skannat rummet från många perspektiv när du skapade ankaret kan användarna Skanna nästan var som helst när de försöker hitta det.

![Illustration av hur en användare kan skanna ett rum för att hitta en fäst punkt](./media/scan-room.png)

I princip ägnar du mer tid åt att skanna utrymmet när du skapar fäst punkten så att senare användare kan söka efter och hitta ankaret snabbt. När du skapar din upplevelse måste du tänka på detta viktiga kompromisser.

Exemplet på appen med blandad verklighet som vi beskrivit tidigare fungerar inte bra som ett rums scenario. Den användare som placerar fäst punkten vill att andra snabbt ska kunna delta i upplevelsen. Användarna vill inte vänta på att starta upplevelsen förrän rummet är väl genomsökt. Eftersom alla användare vet exakt var de ska peka sina enheter för att hitta ankarena fungerar det här exemplet bättre som ett mål scenario.

## <a name="anchor-location"></a>Fäst punkt

Visuella spårnings system förlitar sig på de visuella funktionerna i en miljö. Ju fler visuella funktioner som en sökning omfattar, desto högre är sannolikheten för att hitta en fäst punkt.

Följ de allmänna rikt linjerna i det här avsnittet för att bygga ett UX som uppmuntrar en användbar genomsökning av miljön.

Först, om användaren inte hittar någon fäst punkt inom några sekunder, bör appen uppmuntra användarna att flytta enheten för att fånga fler perspektiv. Appen kan också uppmuntra användarna att flytta sig själva runt miljön för att söka efter fäst punkten från fler perspektiv. Den mer funktionalitet som enheten ser är bättre, eftersom den ökar sannolikheten för att ett ankare finns och den samlar även in mer miljö data som ska användas för att förbättra fäst punktens kvalitet.

För mål scenarier ber du användaren att flytta runt målet för att visa den från olika perspektiv. Med andra ord ber du användaren att avbilda målet från nya perspektiv tills ankaret är placerat.

För rums scenarier ber du användaren att sakta igenom rummet. Be till exempel användaren att slå på att fånga 180 grader eller till och med 360 grader av rummet. Eller be användaren att Visa rummet från ett nytt perspektiv.

Den mest meningsfulla metoden är att skanna över rummet. En sökning i rummet fångar upp fler visuella funktioner i miljön än en genomsökning av en närliggande vägg, till exempel. En skanning av en närliggande vägg kan inte skapa så många användbara visuella funktioner i miljön.

Det är inte bra att upprepade gånger flytta enheten från sida till sida när du söker efter ett ankare. Detta fångar helt enkelt samma punkter från samma perspektiv.

## <a name="experience-tests"></a>Testa

I den här artikeln har vi diskuterat allmänna rikt linjer. Med spatiala ankare skriver du appar som interagerar med den verkliga världen. På grund av detta bör du ägna tid åt att testa dina appars ankar scenarier i verkliga miljöer. Detta gäller särskilt för miljöer som motsvarar var du förväntar dig att användarna ska använda appen.
