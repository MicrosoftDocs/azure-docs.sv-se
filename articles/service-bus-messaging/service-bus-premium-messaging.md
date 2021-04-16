---
title: Azure Service Bus premium- och standardnivåer
description: I den här artikeln beskrivs standard- och premiumnivåer för Azure Service Bus. Jämför dessa nivåer och ger tekniska skillnader.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389493"
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
| Meddelandestorlek upp till 1 MB. Den här gränsen kan höjas i framtiden. De senaste viktiga uppdateringarna för tjänsten finns i Meddelanden [på Azure-bloggen](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Meddelandestorlek upp till 256 kB |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resurscontainern kallas *för en meddelandefunktionsenhet.* Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2, 4, 8 eller 16 meddelandeenheter för varje Service Bus Premium-namnområde. En enskild arbetsbelastning eller entitet kan sträcka sig över flera meddelandefunktionsenheter och antalet meddelandefunktionsenheter kan ändras när du vill. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Med Premium-meddelanden är topprestandan mycket snabbare än på standardnivån.

> [!NOTE]
> Batchstorleksgränsen för Premium-meddelanden är 1 MB.

## <a name="premium-messaging-technical-differences"></a>Premium-meddelanden – tekniska skillnader

I följande avsnitt diskuteras några skillnader mellan Premium- och Standard-meddelandenivåerna.

### <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Partitionerade köer och ämnen stöds inte i Premium-meddelanden. Mer information om partitionering finns i [Partitionerade köer och ämnen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressenheter

Eftersom Premium-meddelanden körs i en isolerad körningsmiljö stöds inte expressenheter i Premium-namnområden. En expressentitet innehåller ett meddelande i minnet tillfälligt innan den skrivs till beständig lagring. Om du har kod som körs under Standard-meddelanden och vill porta den till Premium-nivån ser du till att expressentitetsfunktionen är inaktiverad.

## <a name="premium-messaging-resource-usage"></a>Resursanvändning för Premium-meddelanden
I allmänhet kan en åtgärd på en entitet orsaka processor- och minnesanvändning. Här är några av dessa åtgärder: 

- Hanteringsåtgärder som CRUD-åtgärder (Skapa, Hämta, Uppdatera och Ta bort) för köer, ämnen och prenumerationer.
- Körningsåtgärder (skicka och ta emot meddelanden)
- Övervaka åtgärder och aviseringar

Den ytterligare processor- och minnesanvändningen prissätts dock inte ytterligare. För Premium Messaging-nivån finns det ett enda pris för meddelandeenheten.

Processor- och minnesanvändningen spåras och visas för dig av följande skäl: 

- Ge transparens i systemets interna system
- Förstå kapaciteten för köpta resurser.
- Kapacitetsplanering som hjälper dig att skala upp/ned.

## <a name="messaging-unit---how-many-are-needed"></a>Meddelandeenhet – Hur många behövs?

När du etablerar Azure Service Bus Premium-namnrymd måste antalet allokerade meddelandeenheter anges. Dessa meddelandeenheter är dedikerade resurser som allokeras till namnområdet.

Antalet meddelandeenheter som allokerats till Service Bus Premium-namnområdet kan justeras dynamiskt för att ta med ändringen (öka eller minska) i arbetsbelastningar. 

Det finns ett antal faktorer att ta hänsyn till när du bestämmer antalet meddelandeenheter för din arkitektur:

- Börja med ***1 eller 2 meddelandeenheter som allokerats*** till ditt namnområde.
- Studera cpu-användningsstatistiken i [resursanvändningsstatistiken](service-bus-metrics-azure-monitor.md#resource-usage-metrics) för ditt namnområde.
    - Om CPU-användningen är ***under 20 %** _, kan du *_kanske_* skala ned * antalet meddelandeenheter som allokerats till ditt namnområde.
    - Om CPU-användningen är ***över 70 %** _, kan programmet dra nytta av _ *_skala_* upp * antalet meddelandeenheter som allokerats till ditt namnområde.

Information om hur du konfigurerar en Service Bus för automatisk skalning (öka eller minska meddelandeenheter) finns [i Uppdatera meddelandeenheter automatiskt.](automate-update-messaging-units.md)

> [!NOTE]
> **Skalning** av de resurser som allokeras till namnområdet kan antingen vara förebyggande eller reaktiv.
>
>  * **Förebyggande:** Om ytterligare arbetsbelastning förväntas (på grund av säsongsberoende eller trender) kan du fortsätta med att allokera fler meddelandeenheter till namnområdet innan arbetsbelastningarna används.
>
>  * **Reaktiv:** Om ytterligare arbetsbelastningar identifieras genom att studera resursanvändningsstatistiken kan ytterligare resurser allokeras till namnområdet för att införliva ökande efterfrågan.
>
> Faktureringsmätare för Service Bus varje timme. När det gäller uppskalning betalar du bara för de ytterligare resurserna för de timmar som dessa användes.
>

## <a name="get-started-with-premium-messaging"></a>Kom igång med Premium-meddelandetjänster

Det är enkelt att komma igång med Premium-meddelandetjänster och processen liknar den för Standard-meddelandetjänster. Börja med att [skapa ett namnområde](service-bus-create-namespace-portal.md) i [Azure Portal](https://portal.azure.com). Se till att du väljer **Premium** under **Prisnivå**. Klicka på **Visa fullständiga prisuppgifter** om du vill se mer information om varje nivå.

![create-premium-namespace][create-premium-namespace]

Du kan också skapa [Premium-namnområden med hjälp av Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Nästa steg

I följande länkar kan du lära dig mer om Service Bus-meddelanden:

- [Uppdatera meddelandeenheter automatiskt.](automate-update-messaging-units.md)
- [Introduktion Azure Service Bus Premium Messaging (blogginlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
