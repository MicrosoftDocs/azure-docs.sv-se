---
title: Vanliga frågor och svar om API för avläsning av tjänst – Microsofts kommersiella marknads platser
description: 'Vanliga frågor om API: er för mätnings tjänsten för SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320008"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace för avgiftsbelagda fakturerings-API: er – vanliga frågor

När en kund prenumererar på en SaaS-tjänst eller Azure Application med en plan för hanterade appar, kan du spåra förbrukningen för varje fakturerings dimension som används.  Om förbrukningen överskrider den inkluderade mängden som har angetts för den period som kunden har valt, kommer tjänsten att generera användnings händelser till Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>För både SaaS-erbjudanden och hanterade appar

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hur ofta förväntas att kunna generera användning?

Vi rekommenderar att du genererar användning varje timme den senaste timmen, bara om det finns användning i föregående timme.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Finns det en maximal period mellan ett utsläpp och nästa?

Det finns ingen sådan begränsning. Generera bara användning som det sker. Om du till exempel bara behöver skicka en enhets användning per prenumerations livs längd kan du göra det.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Vad är den maximala fördröjningen mellan tiden en händelse inträffar och hur länge en användnings händelse skickas till Microsoft?

Vi rekommenderar att användnings händelsen genereras varje timme för händelser som inträffat under den senaste timmen. Fördröjningar förväntas dock. Högsta tillåtna fördröjning är 24 timmar, efter vilken användnings händelser inte kommer att godkännas. Det bästa sättet är att samla in användning per timme och att generera är som en händelse i slutet av timmen.

Om en användnings händelse inträffar till en dag på en dag, har du till och med 1 PM på nästa dag för att generera en användnings händelse som är associerad med den här händelsen.  Om den system som avger användningen inte är igång kan den återställas och sedan skicka användnings händelsen för det tidsintervall då användningen skedde, utan att det går att förlora åter givning.

Om 24 timmar har passerat efter den faktiska användningen kan du fortfarande generera de förbrukade enheterna med senare användnings händelser.  Den här metoden kan dock försämra till slut kunden om fakturerings händelse rapporter.  Vi rekommenderar att du inte skickar ett mätnings utsläpp en gång om dagen/veckan/månaden.  Det kommer att vara svårare att förstå den faktiska användningen av en kund och att lösa problem eller frågor som kan uppstå om användnings händelser.

En annan orsak till att skicka användning varje timme är att undvika situationer då användaren avbryter prenumerationen innan utgivaren skickar händelsen dagliga/veckovis/månads utsläpp.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Vad händer när du skickar fler än en användnings händelse inom samma timme?

Endast en användnings händelse godkänns för intervallet med en timme. Tim intervallet börjar vid minut 0 och slutar vid minut 59.  Om fler än en användnings händelse genereras för samma timme, släpps efterföljande användnings händelser som dubbletter.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Vad händer när kunden avbryter köpet inom den tid som tillåts av policyn för uppsägning?

Det fasta priset debiteras inte, men överförbrukningen blir.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Kan anpassade mätar planer användas för engångs betalningar?

Ja, du kan definiera en anpassad dimension som en enhet för engångs betalning och generera den bara en gång för varje kund.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Kan anpassade mätar planer användas för att få en nivå av pris modellen?

Ja, det kan implementeras med varje anpassad dimension som representerar en enda pris nivå.

Contoso vill t. ex. debitera $0,5 per e-post för de första 1000 e-postmeddelandena, $0,4 per e-post mellan 1000 och 5000 e-postmeddelanden och $0,2 per e-post för meddelanden över 5000 som gäller ovan. De kan definiera tre anpassade dimensioner som motsvarar de tre pris nivåerna för e-post. Genererar enheter av den första dimensionen så länge antalet e-postmeddelanden ligger under 1000, sedan enheter i den andra dimensionen när antalet e-postmeddelanden är mellan 1000 och 5000, och slutligen enheter i den tredje dimensionen för över 5000 e-postmeddelanden.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Vad händer om marknads mätnings tjänsten har ett avbrott?

Om ISV: en skickar en anpassad mätare och får ett fel meddelande, kan det ha orsakats av ett problem på Microsoft (vanligt vis i händelse av att liknande händelser togs emot före ett fel). sedan bör ISV vänta och försöka igen.

Om felet kvarstår skickar du om den anpassade mätaren nästa timma (ackumulera antalet). Fortsätt med den här processen tills ett icke-felsvar tas emot.

## <a name="for-saas-offers-only"></a>Endast för SaaS-erbjudanden

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Vad händer när du genererar användning för en SaaS-prenumeration som redan har avbrutit prenumerationen?

All användnings händelse som släpps till Marketplace-plattformen godkänns inte efter att en SaaS-prenumeration har tagits bort.

Användningen kan endast genereras för prenumerationer i prenumerations statusen (och inte för prenumerationer i `PendingFulfillmentStart` , `Suspended` eller `Unsubscribed` status).

Det enda undantaget är rapporterings användningen för den tid som ingick innan SaaS-prenumerationen har annullerats.

Till exempel har kunden avbrutit SaaS prenumerationen idag med 3 PM. Nu är den som är 5 PM kan utgivaren fortfarande generera användning för perioden mellan 6 och den 3: a EM i dag för den här SaaS-prenumerationen.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kan du hämta en lista över alla SaaS-prenumerationer, inklusive aktiva och avbrutna prenumerationer?

Ja, när du anropar [API för Hämta prenumerationer](pc-saas-fulfillment-api-v2.md#subscription-apis) som innehåller en lista över alla SaaS-prenumerationer. Fältet Status i svaret för varje SaaS-prenumeration fångar om prenumerationen är aktiv eller inte prenumererad.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>Är start-och slutdatumen för SaaS prenumerations villkor och överförbruknings utsläpp anslutna?

Överanvändning av händelser kan genereras när som helst för befintliga SaaS-prenumerationer i *prenumerations* status. Det är utgivarens ansvar att generera användnings händelser baserat på principen som definierats i fakturerings planen. Överanvändning måste beräknas baserat på de datum som definieras i SaaS-prenumerationens villkor. 

Om utgivaren till exempel definierar en SaaS-plan som innehåller 1000 e-postmeddelanden för $100 i månatlig fast pris, faktureras varje e-post över 1000 till $1 via anpassad dimension.

När kunden köper och aktiverar prenumerationen den 6 januari kommer 1000-e-postmeddelandet som ingår i den fasta taxan att räknas från den här dagen. Om till och med 5 februari (slutet av den första månaden i prenumerationen) bara skickas 900 e-post, kommer kunden endast att betala fast pris nivån för den första månaden av den här prenumerationen och inga överförbruknings händelser kommer att genereras av utgivaren mellan 6 januari och 5 februari. Den 6 februari förnyas prenumerationen automatiskt och antalet startar igen. Om den 15 februari kunden nådde 1000 e-postmeddelanden som skickats, kommer resten av e-postmeddelandena som skickas fram till 5 att debiteras som överförbrukning ($1 per e-post) baserat på de användnings händelser som utsätts av utgivaren.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [API: er för Marketplace för avläsning av tjänst](./marketplace-metering-service-apis.md).
