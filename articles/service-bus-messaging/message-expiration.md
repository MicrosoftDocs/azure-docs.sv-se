---
title: Azure Service Bus-förfallo datum för meddelande
description: I den här artikeln beskrivs förfallo datum och tid för Azure Service Bus meddelanden. Efter en sådan deadline levereras meddelandet inte längre.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 74df8909633c2fa048c23c559ffdd315a8616e11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042835"
---
# <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (Time to Live)
Nytto lasten i ett meddelande, eller ett kommando eller en fråga om att ett meddelande skickas till en mottagare, är nästan alltid beroende av en viss typ av förfallo datum för program nivå. Efter en sådan tids gräns levereras inte längre innehållet eller så körs inte den begärda åtgärden längre.

För utvecklings-och test miljöer där köer och ämnen ofta används i samband med partiella körningar av program eller program delar, är det också önskvärt att insamlade test meddelanden automatiskt samlas in automatiskt så att nästa test körning kan starta ren.

Du kan kontrol lera förfallo datum för enskilda meddelanden genom att ange egenskapen **Time-to-Live** system, som anger en relativ varaktighet. Förfallo datumet blir en absolut omedelbarhet när meddelandet är i kö i entiteten. Vid det här tillfället tar egenskapen **Expires-at-UTC** till värdet **queueed** Time-  +  **to-Live**. TTL-inställningen (Time-to-Live) på ett Service Broker-meddelande används inte om inga klienter aktivt lyssnar.

Efter **att ha gått ut från och med UTC** blir meddelanden inte berättigade till hämtning. Förfallet påverkar inte meddelanden som för närvarande är låsta för leverans. Dessa meddelanden hanteras fortfarande som vanligt. Om låset går ut eller meddelandet överges, börjar giltighets tiden att gälla omedelbart.

Även om meddelandet är under låst kan programmet ha ett meddelande som har upphört att gälla. Huruvida programmet är villigt att fortsätta med bearbetning eller väljer att överge meddelandet är upp till Implementeraren.

Extremt låg TTL i ordningen på millisekunder eller sekunder kan orsaka att meddelanden upphör att gälla innan mottagar programmen tar emot dem. Överväg den högsta TTL som fungerar för ditt program.

## <a name="entity-level-expiration"></a>Förfallo datum för enhets nivå
Alla meddelanden som skickas till en kö eller ett ämne är underkastade ett standard förfallo datum som anges på enhets nivå. Det kan också ställas in i portalen under skapandet och justeras senare. Standard förfallo datum används för alla meddelanden som skickas till entiteten där Time-to-Live inte anges explicit. Standard förfallo tiden fungerar också som ett tak för Time to Live-värdet. Meddelanden som har en längre tid till Live utgångs datum än standardvärdet justeras tyst till standardvärdet för Time-to-Live innan de placeras i kö.

> [!NOTE]
> Standard TTL-värdet (Time-to-Live) för ett brokerat meddelande är det största möjliga värdet för ett inbyggt 64-bitars heltal om inget annat anges.
>
> För meddelande enheter (köer och ämnen) är standard förfallo tiden också största möjliga värde för ett enskilt 64-bitars heltal för Service Bus standard-och Premium-nivåer. För **Basic** -nivån är standard förfallo tiden **14 dagar**.

Förfallna meddelanden kan också flyttas till en [kö för obeställbara](service-bus-dead-letter-queues.md)meddelanden. Du kan konfigurera den här inställningen program mässigt eller med hjälp av Azure Portal. Om alternativet lämnas inaktiverat, släpps utgångna meddelanden. Förfallna meddelanden som flyttats till kön för obeställbara meddelanden kan särskiljas från andra meddelanden om obeställbara meddelanden genom att utvärdera [orsaks](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) egenskapen för obeställbara meddelanden som Broker lagrar i avsnittet användar egenskaper. 

I det fall då meddelandet skyddas från förfallo datum under lås och om flaggan har angetts för entiteten, flyttas meddelandet till kön för obeställbara meddelanden när låset överges eller upphör att gälla. Men det flyttas inte om meddelandet har kvittats, vilket förutsätter att programmet har lyckats hantera det, trots det nominella förfallo datumet.

Kombinationen av tids-till-Live och automatisk (och transaktionell) obeställbara meddelanden vid förfallo datum är ett värdefullt verktyg för att fastställa om ett jobb till en hanterare eller en grupp med hanterare under en tids gräns hämtas för bearbetning när tids gränsen uppnås.

Anta till exempel att du har en webbplats som behöver köra jobb på ett tillförlitligt sätt på en begränsad Server del och som ibland upplever trafik toppar eller vill vara isolerad mot tillgänglighets avsnitt i den server delen. I det vanliga fallet skickar Server sidans hanterare för de inskickade användar data informationen till en kö och därefter får du ett svar som bekräftar lyckad hantering av transaktionen i en svarskö. Om det finns en trafik ökning och Server dels hanteraren inte kan bearbeta sina efter släpning-objekt i tid, returneras de utgångna jobben i kön för obeställbara meddelanden. Den interaktiva användaren kan meddelas att den begärda åtgärden tar lite längre tid än vanligt, och begäran kan sedan placeras i en annan kö för en bearbetnings väg där det slutliga bearbetnings resultatet skickas till användaren via e-post. 


## <a name="temporary-entities"></a>Temporära entiteter

Service Bus köer, ämnen och prenumerationer kan skapas som temporära entiteter, vilka tas bort automatiskt när de inte har använts under en angiven tids period.
 
Automatisk rensning är användbart i utvecklings-och test scenarier där entiteter skapas dynamiskt och inte rensas efter användningen, på grund av avbrott i testet eller fel söknings körningen. Det är också användbart när ett program skapar dynamiska entiteter, t. ex. en svarskö, för att få svar tillbaka till en webb Server process, eller till ett annat relativt kort period objekt där det är svårt att rensa dessa enheter när objekt instansen försvinner.

Funktionen aktive ras med egenskapen **ta bort automatiskt vid inaktivitet** i namn området. Den här egenskapen anges till den varaktighet för vilken en entitet måste vara inaktiv (oanvänd) innan den tas bort automatiskt. Det minsta värdet för den här egenskapen är 5.
 
## <a name="idleness"></a>Inaktivitetskrav

Det här är vad som anses vara inaktivitet i entiteter (köer, ämnen och prenumerationer):

- Köer
    - Inga sändningar  
    - Inga mottagningar  
    - Inga uppdateringar i kön  
    - Inga schemalagda meddelanden  
    - Ingen Browse/spetsig 
- Ämnen  
    - Inga sändningar  
    - Inga uppdateringar av ämnet  
    - Inga schemalagda meddelanden 
- Prenumerationer
    - Inga mottagningar  
    - Inga uppdateringar av prenumerationen  
    - Inga nya regler har lagts till i prenumerationen  
    - Ingen Browse/spetsig  
 

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
