---
title: Översikt över Azure Service Bus begränsning | Microsoft Docs
description: Översikt över Service Bus begränsning – standard-och Premium-nivåer.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340944"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Begränsnings åtgärder på Azure Service Bus

Inbyggda Cloud-lösningar ger ett begrepp för obegränsade resurser som kan skalas med din arbets belastning. Även om den här begreppet är mer sann i molnet än med lokala system, finns det fortfarande begränsningar som finns i molnet.

Dessa begränsningar kan orsaka begränsning av klient program begär Anden på nivåerna standard och Premium enligt beskrivningen i den här artikeln. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Begränsning i Azure Service Bus standard nivån

Azure Service Bus standard nivån fungerar som en installation med flera innehavare med en pris sättnings modell enligt principen betala per användning. Här är flera namn områden i samma kluster delar de allokerade resurserna. Standard nivån är det rekommenderade valet för utvecklings-, testnings-och frågor och svars miljöer tillsammans med låg data flödes produktions system.

Tidigare hade Azure Service Bus grov begränsnings gränser som är strikt beroende av resursutnyttjande. Det finns dock en möjlighet att förfina begränsnings logiken och tillhandahålla förutsägbart begränsnings beteende för alla namn områden som delar resurserna.

I ett försök att säkerställa en rättvis användning och distribution av resurser i alla Azure Service Bus standard namn områden som använder samma resurser har begränsnings logiken ändrats till kredit.

> [!NOTE]
> Det är viktigt att Observera att begränsningen ***inte är ny*** för Azure Service Bus eller någon moln intern tjänst.
>
> Kredit baserad begränsning är helt enkelt att förfina det sätt som olika namn områden delar resurser i en miljö med flera klienters standard nivåer och därmed aktiverar rättvis användning av alla namn områden som delar resurserna.

### <a name="what-is-credit-based-throttling"></a>Vad är en kredit baserad begränsning?

Kreditbaserade begränsningar begränsar antalet åtgärder som kan utföras i ett angivet namn område under en viss tids period. 

Nedan visas arbets flödet för kredit baserad begränsning – 

  * I början av varje tids period ger vi ett visst antal krediter för varje namn område.
  * Alla åtgärder som utförs av avsändare eller mottagares klient program räknas mot dessa krediter (och subtraheras från de tillgängliga krediterna).
  * Om krediterna är slut kommer efterföljande åtgärder att begränsas tills nästa tids period börjar gälla.
  * Krediter debiteras i början av nästa tids period.

### <a name="what-are-the-credit-limits"></a>Vilka är kredit gränserna?

Kredit gränserna är för närvarande inställt på 1000-krediter varje sekund (per namnrymd).

Alla åtgärder har inte skapats lika. Här är kredit kostnaderna för var och en av driften – 

| Åtgärd | Kredit kostnad|
|-----------|-----------|
| Data åtgärder (skicka, SendAsync, ta emot, ReceiveAsync, spetsig) |1 kredit per meddelande |
| Hanterings åtgärder (skapa, läsa, uppdatera, ta bort i köer, ämnen, prenumerationer, filter) | 10 krediter |

> [!NOTE]
> Observera att varje meddelande som skickas till ett ämne utvärderas mot filter innan de görs tillgängliga i prenumerationen.
> Varje filter utvärdering räknas också mot kredit gränsen (t. ex. 1 kredit per filter utvärdering).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Hur vet jag att jag är begränsad?

När klient program begär Anden begränsas tas nedanstående Server svar emot av ditt program och loggas.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hur kan jag undvika att begränsas?

Med delade resurser är det viktigt att se till att du har en viss typ av god användning i olika Service Bus namn områden som delar dessa resurser. Begränsningen säkerställer att all insamling i en enskild arbets belastning inte medför att andra arbets belastningar på samma resurser begränsas.

Som vi nämnt senare i artikeln finns det ingen risk för att begränsas eftersom klient-SDK: erna (och andra Azure PaaS-erbjudanden) har en inbyggd standard princip för återförsök. Eventuella begränsade begär Anden görs om med exponentiell backoff och kommer att gå igenom när krediten har återgått.

Vissa program kan förstås vara känsliga för att begränsas. I så fall rekommenderar vi att du [migrerar din nuvarande Service Bus standard namn område till Premium](service-bus-migrate-standard-premium.md). 

Vid migrering kan du allokera dedikerade resurser till ditt Service Bus-namnområde och skala upp resurserna på ett lämpligt sätt om du har en topp i arbets belastningen och minska sannolikheten för att bli begränsad. När din arbets belastning minskar till normala nivåer kan du dessutom skala ned resurserna som är allokerade till ditt namn område.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Begränsning i Azure Service Bus Premium-nivån

[Azure Service Bus Premium](service-bus-premium-messaging.md) -nivån allokerar dedikerade resurser, vad gäller meddelande enheter, till varje namn områdes konfiguration av kunden. Dessa dedikerade resurser tillhandahåller förutsägbart data flöde och svars tider och rekommenderas för stora data flöden eller känsliga system för produktions betyg.

Dessutom gör Premium nivån det också möjligt för kunderna att skala upp sin data flödes kapacitet när de upplever toppar i arbets belastningen.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hur fungerar begränsningen i Service Bus Premium?

Med exklusiv resursallokering för Service Bus Premium styrs begränsningen helt av begränsningarna för de resurser som har allokerats till namn området.

Om antalet begär Anden är fler än vad de aktuella resurserna kan betjäna, kommer begäran att begränsas.

### <a name="how-will-i-know-that-im-being-throttled"></a>Hur vet jag att jag är begränsad?

Det finns olika sätt att identifiera begränsning i Azure Service Bus Premium- 
  * **Begränsade begär Anden** visas i [Azure Monitor begär ande mått](service-bus-metrics-azure-monitor.md#request-metrics) för att identifiera hur många begär Anden som begränsades.
  * Hög **CPU-användning** anger att den aktuella resursallokeringen är hög och att förfrågningar kan bli begränsade om den aktuella arbets belastningen inte minskar.
  * Hög **minnes användning** anger att den aktuella resursallokeringen är hög och att förfrågningar kan bli begränsade om den aktuella arbets belastningen inte minskar.

### <a name="how-can-i-avoid-being-throttled"></a>Hur kan jag undvika att begränsas?

Eftersom namn området för Service Bus Premium redan har dedikerade resurser, kan du minska risken för att få en begränsning genom att skala upp antalet meddelande enheter som allokerats till ditt namn område i händelsen (eller förväntade) av en insamling i arbets belastningen.

Skala upp/ned kan uppnås genom att skapa [Runbooks](../automation/automation-create-alert-triggered-runbook.md) som kan utlösas av ändringar i ovanstående mått.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="how-does-throttling-affect-my-application"></a>Hur påverkar begränsningen mitt program?

När en begäran har begränsats innebär det att tjänsten är upptagen eftersom den är riktad mot fler begär Anden än vad resurserna tillåter. Om samma åtgärd görs igen efter en liten stund, när tjänsten har fungerat genom sin nuvarande arbets belastning, kan begäran hanteras.

Eftersom begränsning är det förväntade beteendet för en moln intern tjänst har vi skapat logiken för nya försök i själva Azure Service Bus SDK: n. Standardvärdet är inställt på automatiskt försök med en exponentiell säkerhets kopiering för att se till att vi inte har samma begäran som begränsas varje tid.

Standard logiken för omförsök kommer att gälla för varje åtgärd.

### <a name="does-throttling-result-in-data-loss"></a>Leder begränsningen till data förlust?

Azure Service Bus är optimerat för persistence ser vi till att alla data som skickas till Service Bus sparas i lagrings utrymmet innan tjänsten bekräftar att begäran lyckades.

När begäran har blivit ACK (bekräftad) av Service Bus, innebär det att Service Bus har bearbetat begäran. Om Service Bus returnerar ett ' NACK ' (fel) innebär det att Service Bus inte har kunnat bearbeta begäran och klient programmet måste försöka utföra begäran igen.

Men när en begäran är begränsad, förutsätter tjänsten att den inte kan godkänna och bearbeta begäran just nu på grund av resurs begränsningar. Detta innebär **ingen** sortering av data förlust, eftersom Service Bus helt enkelt inte har tittat på begäran. I det här fallet är det att förlita dig på standard principen för återförsök i Service Bus SDK säkerställer att begäran behandlas.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus meddelande hantering](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)

