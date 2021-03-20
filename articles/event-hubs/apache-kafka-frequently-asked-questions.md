---
title: Vanliga frågor och svar – Azure Event Hubs för Apache Kafka
description: I den här artikeln besvaras vanliga frågor om Azure Event Hubs support för Apache Kafka-klienter som inte omfattas av någon annan stans.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dc6a12b2098a1fdf33adda92b4347f91ab4e5489
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91828109"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Vanliga frågor och svar – Event Hubs för Apache Kafka 
Den här artikeln innehåller svar på några vanliga frågor om att migrera till Event Hubs för Apache Kafka.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Körs Azure Event Hubs på Apache Kafka?

Nej. Azure Event Hubs är en molnbaserad Service Broker för flera nivåer med stöd för flera protokoll som utvecklas och underhålls av Microsoft och som inte använder någon Apache Kafka kod. Ett av de protokoll som stöds är Kafka RPC-protokollet för Kafka-klientens konsument-och producent-API: er. Event Hubs fungerar med många av dina befintliga Kafka-program. Mer information finns i [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). Eftersom begreppen Apache Kafka och Azure Event Hubs är mycket lika (men inte identiska) kan vi erbjuda den oöverträffade tillförlitligheten hos Azure-Event Hubs till kunder med befintliga Apache Kafka investeringar. 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs konsument grupp eller konsument grupp för Kafka
Vad är skillnaden mellan en konsument grupp för Event Hub och en Kafka-konsument grupp på Event Hubs? Kafka konsument grupper på Event Hubs är helt skilda från standard Event Hubs konsument grupper.

**Event Hubs konsument grupper**

- De hanteras med åtgärder för att skapa, Hämta, uppdatera och ta bort (CRUD) via portal, SDK eller Azure Resource Manager mallar. Event Hubs konsument grupper kan inte skapas autoskapade.
- De är underordnade entiteter för en Event Hub. Det innebär att samma konsument grupp namn kan återanvändas mellan Event Hub i samma namnrymd eftersom de är separata entiteter.
- De används inte för att lagra förskjutningar. Den dirigerade AMQP-förbrukningen görs med hjälp av extern offset Storage, till exempel Azure Storage.

**Kafka konsument grupper**

- De skapas Autoskapas.  Kafka-grupper kan hanteras via Kafka-konsument grupps-API: er.
- De kan lagra förskjutningar i Event Hubss tjänsten.
- De används som nycklar i vad som effektivt är ett förskjutnings nyckel värdes lager. För ett unikt par av `group.id` och `topic-partition` lagrar vi en förskjutning i Azure Storage (3x-replikering). Event Hubs användare debiteras inte extra lagrings kostnader för lagring av Kafka-förskjutningar. Förskjutningar är manipulable via Kafka konsument grupps-API: er, men de förskjutnings lagrings *kontona* är inte direkt synliga eller Manipulable för Event Hub-användare.  
- De sträcker sig över ett namn område. Om du använder samma Kafka grupp namn för flera program i flera ämnen innebär det att alla program och deras Kafka-klienter kommer att ombalanseras när endast ett enda program behöver ombalansera.  Välj grupp namn med en bra idé.
- De är helt åtskilda från Event Hubs konsument grupper. Du behöver **inte** använda "$default" eller behöver inte oroa dig om Kafka-klienter som stör AMQP-arbetsbelastningar.
- De visas inte i Azure Portal. Konsument grupp information kan nås via Kafka-API: er.

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs och Event Hubs för Kafka finns i följande artiklar:  

- [Apache Kafka Developer Guide för Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migrations guide för Event Hubs](apache-kafka-migration-guide.md)
- [Apache Kafka fel söknings guide för Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Rekommenderade konfigurationer](apache-kafka-configurations.md)

