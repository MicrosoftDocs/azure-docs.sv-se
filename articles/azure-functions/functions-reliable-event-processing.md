---
title: Azure Functions tillförlitlig händelse bearbetning
description: Undvik Event Hub-meddelanden som saknas i Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93379595"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions tillförlitlig händelse bearbetning

Händelse bearbetning är ett av de vanligaste scenarierna som är kopplade till arkitektur utan server. I den här artikeln beskrivs hur du skapar en tillförlitlig meddelande processor med Azure Functions för att undvika att förlora meddelanden.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Utmaningar med händelse strömmar i distribuerade system

Överväg ett system som skickar händelser till en konstant hastighet på 100 händelser per sekund. Inom några minuter kan instanser med flera parallell funktioner använda inkommande 100-händelser varje sekund.

Något av följande mindre optimala villkor är dock möjligt:

- Vad händer om händelse utgivaren skickar en skadad händelse?
- Vad händer om din functions-instans påträffar ohanterade undantag?
- Vad händer om ett underordnat system kopplas från?

Hur kan du hantera dessa situationer samtidigt som du bevarar program varans data flöde?

Med köer kommer Reliable Messaging att bli naturligt. När den kombineras med en Functions-utlösare skapar funktionen ett lås i Queue-meddelandet. Om bearbetningen Miss lyckas frigörs låset för att tillåta en annan instans att försöka bearbeta igen. Bearbetningen fortsätter tills antingen meddelandet utvärderas korrekt eller läggs till i en Poison-kö.

Även om ett enskilt Queue-meddelande kan finnas kvar i en återförsöks cykel fortsätter andra parallella körningar att fortsätta att köa återstående meddelanden. Resultatet är att det totala data flödet fortfarande fortfarande påverkas av ett dåligt meddelande. Lagrings köer garanterar dock inte beställningar och är inte optimerade för de höga data flödes krav som krävs av Event Hubs.

Som kontrast inkluderar inte Azure Event Hubs ett låsnings begrepp. För att tillåta funktioner som hög genom strömning, flera konsument grupper och omuppspelnings möjligheter, fungerar Event Hubs händelser som en Videos pelare. Händelser läses från en enda punkt i strömmen per partition. Från pekaren kan du läsa framåt eller bakåt från den platsen, men du måste välja att flytta pekaren för händelser att bearbeta.

Om det uppstår fel i en data ström, om du bestämmer dig för att behålla pekaren på samma plats, blockeras händelse bearbetningen tills pekaren är avancerad. Med andra ord, om pekaren har stoppats för att hantera problem med att bearbeta en enskild händelse, börjar de obearbetade händelserna Piling.

Azure Functions undviker död lägen genom att spela upp strömmens pekare, oavsett om det lyckas eller Miss lyckas. Eftersom pekaren fortsätter att fungera måste dina funktioner hantera problem på lämpligt sätt.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Hur Azure Functions använder Event Hubs händelser

Azure Functions använder Event Hub-händelser samtidigt som du går igenom följande steg:

1. En pekare skapas och sparas i Azure Storage för varje partition i händelsehubben.
2. När nya meddelanden tas emot (i en batch som standard) försöker värden att utlösa funktionen med meddelande gruppen.
3. Om funktionen Slutför körningen (med eller utan undantag) och en kontroll punkt sparas på lagrings kontot.
4. Om villkoren förhindrar att funktions körningen slutförs, går det inte att fortsätta med pekaren. Om pekaren inte är avancerad kontrollerar senare att samma meddelanden har bearbetats.
5. Upprepa steg 2 – 4

Det här beteendet visar några viktiga punkter:

- *Ohanterade undantag kan innebära att du förlorar meddelanden.* Körningar som resulterar i ett undantag fortsätter att försätta pekaren.  Om du anger en [princip för återförsök](./functions-bindings-error-pages.md#retry-policies-preview) fördröjs pekaren tills hela återförsöks principen har utvärderats.
- *Functions garanterar minst en leverans.* Din kod och beroende system kan behöva [konto för att samma meddelande ska kunna tas emot två gånger](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Hantering av undantag

Som en allmän regel ska varje funktion innehålla ett [try/catch-block](./functions-bindings-error-pages.md) på den högsta kod nivån. Mer specifikt bör alla funktioner som använder Event Hubs händelser ha ett `catch` block. När ett undantag aktive RAS hanterar catch-blocket felet innan pekaren fortskrider.

### <a name="retry-mechanisms-and-policies"></a>Försök igen mekanismer och principer

Vissa undantag är tillfälliga i natur och visas inte igen när en åtgärd försöker igen senare. Det är därför som det första steget alltid ska försöka utföra åtgärden igen.  Du kan använda funktionen för att [köra nya återförsöks principer](./functions-bindings-error-pages.md#retry-policies-preview) för appar eller redigera logik för omförsök i funktionen.

Genom att införa fel hanterings beteenden för dina funktioner kan du definiera både grundläggande och avancerade principer för återförsök. Du kan till exempel implementera en princip som följer ett arbets flöde som illustreras i följande regler:

- Försök att infoga ett meddelande tre gånger (eventuellt en fördröjning mellan återförsök).
- Om det slutliga resultatet av alla nya försök är ett fel, lägger du till ett meddelande i en kö så att bearbetningen kan fortsätta på data strömmen.
- Skadade eller obearbetade meddelanden hanteras sedan senare.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) är ett exempel på ett flexibelt och tillfälligt fel hanterings bibliotek för C#-program.

## <a name="non-exception-errors"></a>Fel som inte är undantag

Vissa problem uppstår även när det inte finns något fel. Överväg till exempel ett fel som inträffar i mitten av en körning. I det här fallet, om en funktion inte slutför körningen, kommer förskjutnings pekaren aldrig att gå vidare. Om pekaren inte går vidare fortsätter alla instanser som körs efter en misslyckad körning att läsa samma meddelanden. Den här situationen ger en "minst en gång"-garanti.

Försäkran att varje meddelande bearbetas minst en gång innebär att vissa meddelanden kan bearbetas mer än en gång. Dina funktions program måste vara medvetna om den här möjligheten och måste byggas runt [principerna för idempotens](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Stoppa och starta om körning

Några fel kan vara acceptabla, vad händer om din app upplever betydande fel? Du kanske vill stoppa inaktive ring av händelser tills systemet når ett felfritt tillstånd. Att ha möjlighet att pausa bearbetningen uppnås ofta med ett krets brytares mönster. Mönstret krets brytare gör att din app kan "bryta kretsen" av händelse processen och återuppta vid ett senare tillfälle.

Det finns två bitar som krävs för att implementera en krets brytare i en händelse process:

- Delat tillstånd över alla instanser för att spåra och övervaka kretsens hälso tillstånd
- Huvud process som kan hantera kretsens tillstånd (öppen eller stängd)

Implementerings informationen kan variera, men för att dela tillstånd mellan instanser behöver du en lagrings funktion. Du kan välja att lagra tillstånd i Azure Storage, en Redis-cache eller något annat konto som är tillgängligt för en samling funktioner.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) eller [varaktiga funktioner](./durable/durable-functions-overview.md) är en naturlig anpassning för att hantera arbets flödes-och krets tillstånd. Andra tjänster fungerar även lika bra, men Logic Apps används i det här exemplet. Med hjälp av Logic Apps kan du pausa och starta om en funktions körning som ger dig den kontroll som krävs för att implementera krets brytar mönstret.

### <a name="define-a-failure-threshold-across-instances"></a>Definiera ett tröskelvärde för en tröskel över instanser

För att kunna hantera flera instanser bearbetar händelser samtidigt, så behövs ett beständigt delat externt tillstånd för att övervaka kretsens hälso tillstånd.

En regel som du kan välja att implementera kan genomdriva följande:

- Om det finns fler än 100 eventuella fel inom 30 sekunder i alla instanser, Bryt du kretsen och slutar utlösa nya meddelanden.

Implementerings informationen varierar beroende på dina behov, men i allmänhet kan du skapa ett system som:

1. Logg felen till ett lagrings konto (Azure Storage, Redis osv.)
1. När ett nytt haveri loggas bör du kontrol lera antalet rullandeser för att se om tröskelvärdet är uppfyllt (till exempel mer än 100 under de senaste 30 sekunderna).
1. Om tröskelvärdet är uppfyllt genererar du en händelse för att Azure Event Grid att systemet ska bryta kretsen.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Hantera krets tillstånd med Azure Logic Apps

Följande beskrivning visar ett sätt som du kan skapa en Azure Logic-app för att stoppa en Functions-app från bearbetning.

Azure Logic Apps innehåller inbyggda kopplingar till olika tjänster, har tillstånds känsliga dirigeringar och är ett naturligt alternativ för att hantera krets tillstånd. När du har identifierat kretsen måste brytas, kan du bygga en Logic app för att implementera följande arbets flöde:

1. Utlös ett Event Grid-arbetsflöde och stoppa Azure-funktionen (med Azure Resource Connector)
1. Skicka ett e-postmeddelande som innehåller ett alternativ för att starta om arbets flödet

E-postmottagaren kan undersöka hälso tillståndet för kretsen och vid behov starta om kretsen via en länk i e-postmeddelandet. När arbets flödet startar om funktionen bearbetas meddelanden från den sista kontroll punkten för Event Hub.

Med den här metoden går inga meddelanden förlorade, alla meddelanden bearbetas i ordning och du kan dela upp kretsen så länge som det behövs.

## <a name="resources"></a>Resurser

- [Exempel på Reliable Event Processing](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Krets brytare för Azure tåliga enheter](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Azure Functions fel hantering](./functions-bindings-error-pages.md)
- [Automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Skapa en funktion som kan integreras med Azure Logic Apps](./functions-twitter-email.md)
