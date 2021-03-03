---
title: Översikt över dedikerade Event Hub – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över dedikerade Azure-Event Hubs, som erbjuder distributioner av enskilda innehavare av Event Hub.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715623"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

*Event Hubs kluster* erbjuder distributioner för enskilda klienter för kunder med de mest krävande strömnings behoven. Det här erbjudandet för en enda klient organisation har ett garanterat 99,99% SLA och är bara tillgängligt på vår dedikerade pris nivå. Ett Event Hubs kluster kan intränga miljon tals händelser per sekund med garanterad kapacitet och under sekund svars tid. Namn områden och händelse nav som skapats i det dedikerade klustret inkluderar alla funktioner i standard erbjudandet och mer, men utan några ingångs gränser. Den innehåller också den populära [Event Hubs Capture](event-hubs-capture-overview.md) -funktionen utan extra kostnad. Med den här funktionen kan du automatiskt köra och logga data strömmar för att Azure Storage eller Azure Data Lake. 

Kluster etablerade och debiteras efter **kapacitets enheter (CUS)**, en förallokerad mängd processor-och minnes resurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 CUs för varje kluster. Hur mycket du kan mata in och strömma per CU beror på flera olika faktorer, till exempel följande: 

- Antal producenter och konsumenter
- Nytto Last form
- Utgående hastighet

> [!NOTE]
> Alla Event Hubs-kluster är Kafka-aktiverade som standard och stöder Kafka-slutpunkter som kan användas av dina befintliga Kafka-baserade program. Att ha Kafka aktiverat i klustret påverkar inte dina användnings fall som inte är Kafka; Det finns inget alternativ eller behöver inaktivera Kafka på ett kluster.

## <a name="why-dedicated"></a>Varför dedikerat?

Dedikerade Event Hubs erbjuder tre övertygande förmåner för kunder som behöver kapacitet på företags nivå:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>En enda innehavare garanterar kapaciteten för bättre prestanda

Ett dedikerat kluster garanterar kapacitet vid full skala. Det kan intränga upp till gigabyte av strömmande data med helt varaktig lagring och under en andra latens för att hantera burst i trafiken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusive och exklusiv åtkomst till funktioner 
Det dedikerade erbjudandet omfattar funktioner som att fånga utan extra kostnad och exklusiv åtkomst till kommande funktioner som Bring Your Own Key (BYOK). Tjänsten hanterar även belastnings utjämning, OS-uppdateringar, säkerhets korrigeringar och partitionering. Det innebär att du kan lägga mindre tid på infrastruktur underhåll och mer tid på att skapa funktioner på klient sidan.  

#### <a name="cost-savings"></a>Kostnads besparingar
Vid höga ingångs volymer (>100-dataflödes enheter) är ett kluster kostar betydligt mindre per timme än att köpa en jämförbar mängd data flödes enheter i standard erbjudandet.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated kvoter och begränsningar

Event Hubs Dedicated erbjudandet debiteras till ett fast månads pris, med minst fyra timmars användning. Den dedikerade nivån innehåller alla funktioner i standard planen, men med kapacitet och gränser i företags skala för kunder med krävande arbets belastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---|:---|
| Bandbredd | 20 antal (upp till 40 antal) | 20 CUs |
| Namnrymder |  1 | 50 per CU |
| Event Hubs |  10 per namnrymd | 1000 per namnrymd |
| Ingress-händelser | Betala per miljon händelser | Ingår |
| Meddelande storlek | 1 000 000 byte | 1 000 000 byte |
| Partitioner | 32 per Event-hubb | 1024 per Event-hubb<br/>2000 per CU |
| Konsumentgrupper | 20 per Event-hubb | Ingen gräns per CU, 1000 per Event-hubb |
| Brokered Connections | 1 000 ingår, 5 000 max | 100 KB ingår och Max |
| [Kvarhållning av händelser](event-hubs-features.md#event-retention) | 7 dagar, 84 GB ingår per data flödes enheter | 90 dagar, 10 TB inkluderat per CU |
| Capture | Betala per timme | Ingår |

Mer kvoter och begränsningar finns i [Event Hubs kvoter och begränsningar](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Publicera

Den självbetjänings upplevelsen för att [skapa ett Event Hubs kluster](event-hubs-dedicated-cluster-create-portal.md) via [Azure Portal](https://aka.ms/eventhubsclusterquickstart) är nu i för hands version. Om du har några frågor eller behöver hjälp med att registrera dig för att Event Hubs Dedicated kan du kontakta [Event Hubss teamet](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Vanliga frågor och svar

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Nästa steg

Kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated. Du kan också skapa ett kluster eller lära dig mer om Event Hubs pris nivåer genom att gå till följande länkar:

- [Skapa ett Event Hubs kluster via Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated prissättning](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan också kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated kapacitet.
- [Event HUBS vanliga](event-hubs-faq.md) frågor och svar innehåller pris information och svar på några vanliga frågor om Event Hubs.
