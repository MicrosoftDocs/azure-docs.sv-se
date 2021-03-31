---
title: Felsök problem med Azure Event Hubs för Apache Kafka
description: Den här artikeln visar hur du felsöker problem med Azure Event Hubs för Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90061435"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Apache Kafka fel söknings guide för Event Hubs
Den här artikeln innehåller fel söknings tips för problem som du kan köra i när du använder Event Hubs för Apache Kafka. 

## <a name="server-busy-exception"></a>Undantag för Server upptagen
Du kan få ett upptaget Server-undantag på grund av Kafka-begränsning. Med AMQP-klienter returnerar Event Hubs omedelbart en **Server upptagen** undantag vid tjänst begränsning. Det motsvarar meddelandet "försök igen senare". I Kafka försenas meddelanden innan de slutförs. Fördröjnings längden returneras i millisekunder som `throttle_time_ms` i svaret på framställning/hämtning. I de flesta fall loggas inte dessa fördröjda förfrågningar som server upptagna undantag på Event Hubs instrument paneler. I stället bör svarets `throttle_time_ms` värde användas som en indikator att data flödet har överskridit den etablerade kvoten.

Om trafiken är för hög, har tjänsten följande beteende:

- Om begärans fördröjning överskrids, kan Event Hubs returnera felkoden för **princip** fel.
- Om fördröjningen för hämtnings begär Anden överskrider tids gränsen för begär Anden loggar Event Hubs begäran som begränsad och svarar med en tom uppsättning poster och ingen felkod.

[Dedikerade kluster](event-hubs-dedicated-overview.md) har inga begränsnings metoder. Du är kostnads fri att använda alla dina kluster resurser.

## <a name="no-records-received"></a>Inga mottagna poster
Det kan hända att konsumenter inte hämtar några poster och ombalanserar hela tiden. I det här scenariot får inte konsumenter några poster och ombalansera hela tiden. Det finns inget undantag eller fel när det händer, men Kafka-loggarna visar att konsumenterna fastnar i försök att återansluta till gruppen och tilldela partitioner. Det finns några möjliga orsaker:

- Se till att minst ett av de `request.timeout.ms` rekommenderade värdena är 60000 och att `session.timeout.ms` det är minst det rekommenderade värdet 30000. Om du har dessa inställningar för låg kan det orsaka timeout för konsumenten, som sedan orsakar ombalanser (som sedan orsakar fler tids gränser, vilket orsakar mer ombalansering osv.) 
- Om konfigurationen stämmer överens med de rekommenderade värdena och du fortfarande ser en konstant ombalansering, är det dags att öppna ett problem (se till att ta med hela konfigurationen i problemet så att vi kan hjälpa dig att felsöka)!

## <a name="compressionmessage-format-version-issue"></a>Problem med komprimering/meddelande format version
Kafka stöder komprimering och Event Hubs för Kafka för närvarande inte. Fel som nämner en meddelande format version (till exempel `The message format version on the broker does not support the request.` ) orsakas när en klient försöker skicka komprimerade Kafka-meddelanden till våra mäklare.

Om komprimerade data är nödvändiga komprimerar du dina data innan du skickar dem till utlösarna och dekomprimerar när du har fått en giltig lösning. Meddelande texten är bara en byte mat ris till tjänsten, så att komprimering/expandering på klient sidan inte orsakar några problem.

## <a name="unknownserverexception"></a>UnknownServerException
Du kan få en UnknownServerException från Kafka-klient bibliotek som liknar följande exempel: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Öppna ett ärende med Microsoft support.  Fel söknings-och undantags tids stämpling i UTC är till hjälp vid fel sökning av problemet. 

## <a name="other-issues"></a>Andra problem
Kontrol lera följande objekt om du ser problem när du använder Kafka på Event Hubs.

- **Brand vägg som blockerar trafik** – kontrol lera att port **9093** inte blockeras av brand väggen.
- **TopicAuthorizationException** – de vanligaste orsakerna till detta undantag är:
    - Ett skrivfel i anslutnings strängen i konfigurations filen, eller
    - Försöker använda Event Hubs för Kafka på ett Basic-skikts namn område. Funktionen Event Hubs för Kafka [stöds bara för namn områden som är standard och dedikerade](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Kafka-versionen matchar inte** -Event Hubs för Kafka-eko system stöder Kafka-versionerna 1,0 och senare. Vissa program som använder Kafka version 0,10 och senare kan ibland fungera på grund av Kafka-protokollets bakåtkompatibla kompatibilitet, men vi rekommenderar starkt att du använder gamla API-versioner. Kafka version 0,9 och tidigare stöder inte de SASL-protokoll som krävs och kan inte ansluta till Event Hubs.
- **Onormala kodningar i AMQP-huvuden vid användning med Kafka** – när du skickar händelser till en Event Hub via AMQP serialiseras alla AMQP nytto Last rubriker i AMQP encoding. Kafka-konsumenter deserialiserar inte huvudena från AMQP. Om du vill läsa rubrik värden avkodar du AMQP-huvudena manuellt. Alternativt kan du undvika att använda AMQP-huvuden om du vet att du kommer att använda Kafka-protokollet. Mer information finns i [det här GitHub-problemet](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Sasl-autentisering** – få ditt ramverk att samar beta med sasl-autentiseringsprotokollet som krävs av Event Hubs kan vara svårare än vad som uppfyller ögat. Se om du kan felsöka konfigurationen med hjälp av ramverkets resurser på SASL-autentisering. 

## <a name="limits"></a>Gränser
Apache Kafka vs. Event Hubs Kafka. Azure Event Hubs-gränssnittet för Kafka har till exempel samma standardinställningar, egenskaper, felkoder och allmänt beteende som Apache Kafka gör. Instanserna som dessa två skiljer sig åt (eller där Event Hubs tillämpar en gräns som Kafka inte) visas nedan:

- `group.id`Egenskapens max längd är 256 tecken
- Max storleken på `offset.metadata.max.bytes` är 1024 byte
- Offset-incheckningar begränsas till 4 anrop per sekund per partition med en maximal intern logg storlek på 1 MB


## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs och Event Hubs för Kafka finns i följande artiklar:  

- [Apache Kafka Developer Guide för Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migrations guide för Event Hubs](apache-kafka-migration-guide.md)
- [Vanliga frågor och svar – Event Hubs för Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Rekommenderade konfigurationer](apache-kafka-configurations.md)
