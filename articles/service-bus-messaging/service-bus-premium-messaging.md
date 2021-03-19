---
title: Azure Service Bus Premium-och standard-nivåer
description: I den här artikeln beskrivs Azure Service Buss standard-och Premium-nivåer. Jämför dessa nivåer och ger tekniska skillnader.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: aa08a99009ef3d20e831e214ae5811059817d13c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607559"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- och Standard-meddelandenivåer

Service Bus-meddelanden, inklusive entiteter som köer och ämnen, kombinerar meddelandefunktioner för företag med utförlig publicerings-/prenumerationssemantik i molnskala. Service Bus-meddelanden används som kommunikationsryggrad i flera avancerade molnlösningar.

*Premium*-nivån av Service Bus-meddelanden är ett svar på vanliga kundönskemål gällande skala, prestanda och tillgänglighet för verksamhetskritiska program. Premium-nivån rekommenderas för användning i produktionsmiljö. Även om funktionsuppsättningarna är snudd på identiska är dessa två nivåer av Service Bus-meddelanden utformade för att passa olika användningsfall.

En del övergripande skillnader visas i tabellen nedan.

| Premium | Standard |
| --- | --- |
| Högt genomflöde |Variabelt genomflöde |
| Förutsägbara prestanda |Variabel svarstid |
| Fast prissättning |Variabla priser – betala per användning |
| Möjlighet att skala arbetsbelastningen uppåt och nedåt |Ej tillämpligt |
| Meddelande storlek upp till 1 MB. Den här gränsen kan höjas i framtiden. De senaste viktiga uppdateringarna för tjänsten finns i [meddelanden i Azure-bloggen](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Meddelandestorlek upp till 256 kB |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resurs behållaren kallas för en *meddelande enhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2, 4, 8 eller 16 meddelande enheter för varje Service Bus Premium-namnrymd. En enskild arbets belastning eller entitet kan sträcka sig över flera meddelande enheter och antalet meddelande enheter kan ändras. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Med Premium-meddelanden är topprestandan mycket snabbare än på standardnivån.

## <a name="premium-messaging-technical-differences"></a>Premium-meddelanden – tekniska skillnader

I följande avsnitt diskuteras några skillnader mellan Premium- och Standard-meddelandenivåerna.

### <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Partitionerade köer och ämnen stöds inte i Premium-meddelanden. Mer information om partitionering finns i [Partitionerade köer och ämnen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressenheter

Eftersom Premium-meddelanden körs i en isolerad körnings miljö stöds inte Express-enheter i Premium-namnområden. En Express-entitet innehåller ett meddelande i minnet tillfälligt innan det skrivs till beständigt lagrings utrymme. Om du har kod som körs under standard meddelanden och vill Porta den till Premium nivån, kontrollerar du att funktionen Express entitet är inaktive rad.

## <a name="premium-messaging-resource-usage"></a>Användning av Premium-meddelande resurser
I allmänhet kan alla åtgärder på en entitet orsaka CPU-och minnes användning. Här följer några av de här åtgärderna: 

- Hanterings åtgärder som CRUD (skapa, Hämta, uppdatera och ta bort) åtgärder för köer, ämnen och prenumerationer.
- Körnings åtgärder (skicka och ta emot meddelanden)
- Övervaka åtgärder och aviseringar

Den ytterligare CPU-och minnes användningen priss ätts inte heller. För Premium-meddelande nivån finns det ett enda pris för meddelande enheten.

PROCESSOR-och minnes användningen spåras och visas av följande orsaker: 

- Lämna insyn i systemets interna
- Förstå kapaciteten hos de köpta resurserna.
- Kapacitets planering som hjälper dig att välja att skala upp/ned.

## <a name="messaging-unit---how-many-are-needed"></a>Meddelande enhet – hur många behövs?

När du konfigurerar ett Azure Service Bus Premium-namnområde måste antalet allokerade meddelande enheter anges. Dessa meddelande enheter är dedikerade resurser som är allokerade till namn området.

Antalet meddelande enheter som tilldelas Service Bus Premium-namnrymden kan **justeras dynamiskt** till faktor i ändringen (ökning eller minskning) i arbets belastningar.

Det finns ett antal faktorer att tänka på när du bestämmer antalet meddelande enheter för din arkitektur:

- Börja med ***1 eller 2 meddelande enheter*** som har tilldelats ditt namn område.
- Studera processor användnings måtten i [användnings statistik för resursanvändningen](service-bus-metrics-azure-monitor.md#resource-usage-metrics) för ditt namn område.
    - Om CPU-användningen är ***under 20%** _ kan du kanske _ *_skala ned_** antalet meddelande enheter som har allokerats till ditt namn område.
    - Om CPU-användningen är ***över 70%** _, kommer ditt program att få från _ *_skala upp_** antalet meddelande enheter som har allokerats till ditt namn område.

Information om hur du konfigurerar ett Service Bus namn område för automatisk skalning (öka eller minska meddelande enheter) finns i [Uppdatera meddelande enheter automatiskt](automate-update-messaging-units.md).

> [!NOTE]
> **Skalning** av resurser som är allokerade till namn området kan vara antingen ogiltiga eller reaktivt.
>
>  * **Ogiltiga**: om ytterligare arbets belastning förväntas (på grund av säsongs beroende eller trender) kan du fortsätta med att allokera fler meddelande enheter till namn området innan arbets belastningarna nåtts.
>
>  * **Reaktiv**: om ytterligare arbets belastningar identifieras genom att studera resursanvändnings måtten kan ytterligare resurser allokeras till namn området för att införliva ökande efter frågan.
>
> Fakturerings mätare för Service Bus per timme. Om du skalar upp, betalar du bara för de ytterligare resurserna för de timmar som de användes.
>

## <a name="get-started-with-premium-messaging"></a>Kom igång med Premium-meddelandetjänster

Det är enkelt att komma igång med Premium-meddelandetjänster och processen liknar den för Standard-meddelandetjänster. Börja med att [skapa ett namnområde](service-bus-create-namespace-portal.md) i [Azure Portal](https://portal.azure.com). Se till att du väljer **Premium** under **Prisnivå**. Klicka på **Visa fullständiga prisuppgifter** om du vill se mer information om varje nivå.

![create-premium-namespace][create-premium-namespace]

Du kan också skapa [Premium-namnområden med hjälp av Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Nästa steg

I följande länkar kan du lära dig mer om Service Bus-meddelanden:

- [Uppdatera meddelande enheter automatiskt](automate-update-messaging-units.md).
- [Introduktion till Azure Service Bus Premium-meddelanden (blogg inlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
