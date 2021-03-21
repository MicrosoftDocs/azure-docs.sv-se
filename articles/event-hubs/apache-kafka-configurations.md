---
title: Rekommenderade konfigurationer för Apache Kafka klienter – Azure Event Hubs
description: Den här artikeln innehåller rekommenderade Apache Kafka konfigurationer för klienter som interagerar med Azure Event Hubs för Apache Kafka.
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: be009aae41b2cb26ab02fdbe14bc4e18311ad235
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042359"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Rekommenderade konfigurationer för Apache Kafka klienter
Här är de rekommenderade konfigurationerna för att använda Azure Event Hubs från Apache Kafka klient program. 

## <a name="java-client-configuration-properties"></a>Egenskaper för Java-klient konfiguration

### <a name="producer-and-consumer-configurations"></a>Tillverkare och konsument konfiguration

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (ungefärligt) | < 240000 | Kan sänkas för att hämta metadata ändringar snart.
`connections.max.idle.ms`   | 180000 | < 240000 | Azure stänger inkommande TCP-inaktiv > 240 000 MS, vilket kan leda till att de skickas vid död anslutningar (visas som utgångna batchar på grund av sändnings tids gräns).

### <a name="producer-configurations-only"></a>Endast producerande konfigurationer
Du hittar Producer-konfiguration [här](https://kafka.apache.org/documentation/#producerconfigs).

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Tjänsten stänger anslutningar om förfrågningar som är större än 1 046 528 byte skickas.  *Det här värdet **måste** ändras och orsakar problem i scenarier med stora data flöden.*
`retries` | > 0 | | Kan kräva ökande delivery.timeout.ms-värde, se dokumentationen.
`request.timeout.ms` | 30000.. 60000 | > 20000| HÄNDELSEHUBBNAMNOMRÅDE kommer internt att vara standard till minst 20 000 MS.  *När begär Anden med lägre timeout-värden godkänns, är klient beteendet inte garanterat.*
`metadata.max.idle.ms` | 180000 | > 5000 | Styr hur länge producenten ska cachelagra metadata för ett ämne som är inaktivt. Om den förflutna tiden sedan ett ämne senast producerades överskrider varaktigheten för metadata, är ämnets metadata bortglömt och nästa åtkomst till den kommer att tvinga en begäran om hämtning av metadata.
`linger.ms` | > 0 | | För stora data flödes scenarier ska Linger-värdet vara lika med det högsta tillåtna värdet för att dra nytta av batching.
`delivery.timeout.ms` | | | Ange enligt formeln ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`compression.type` | `none` | | Komprimering stöds inte för närvarande..

### <a name="consumer-configurations-only"></a>Endast konsument konfiguration
Konsument konfiguration hittar du [här](https://kafka.apache.org/documentation/#consumerconfigs).

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 är standardvärdet och bör inte ändras.
`session.timeout.ms` | 30000 |6000.. 300000| Börja med 30000, öka om du ser frekvent ombalansering på grund av missade pulsslag.


## <a name="librdkafka-configuration-properties"></a>konfigurations egenskaper för librdkafka
Huvud `librdkafka` konfigurations filen ([länk](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) innehåller utökade beskrivningar för egenskaperna nedan.

### <a name="producer-and-consumer-configurations"></a>Tillverkare och konsument konfiguration

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|-----:|---
`socket.keepalive.enable` | true | | Krävs om anslutningen förväntas vara inaktiv.  Azure kommer att stänga inkommande TCP-inaktiv > 240 000 MS.
`metadata.max.age.ms` | ~ 180000| < 240000 | Kan sänkas för att hämta metadata ändringar snart.

### <a name="producer-configurations-only"></a>Endast producerande konfigurationer

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|-----:|---
`retries` | > 0 | | Standardvärdet är 2. Vi rekommenderar att du behåller det här värdet. 
`request.timeout.ms` | 30000.. 60000 | > 20000| HÄNDELSEHUBBNAMNOMRÅDE kommer internt att vara standard till minst 20 000 MS.  `librdkafka` Standardvärdet är 5000, vilket kan vara problematiskt. *När begär Anden med lägre timeout-värden godkänns, är klient beteendet inte garanterat.*
`partitioner` | `consistent_random` | Se librdkafka-dokumentationen | `consistent_random` är standard och bästa.  Tomma och null-nycklar hanteras helst i de flesta fall.
`compression.codec` | `none` || Komprimering stöds inte för närvarande.

### <a name="consumer-configurations-only"></a>Endast konsument konfiguration

Egenskap | Rekommenderade värden | Tillåtet intervall | Kommentarer
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 är standardvärdet och bör inte ändras.
`session.timeout.ms` | 30000 |6000.. 300000| Börja med 30000, öka om du ser frekvent ombalansering på grund av missade pulsslag.


## <a name="further-notes"></a>Ytterligare information

Se följande tabell med vanliga konfigurations fel scenarier.

Symtom | Problem | Lösning
----|---|-----
Misslyckade förskjutnings skrivningar på grund av ombalansering | Konsumenten väntar för lång tid på mellan anrop till pollning () och tjänsten håller konsumenten ur gruppen. | I det syftet har du flera alternativ: <ul><li>Öka tids gränsen för avsöknings bearbetning ( `max.poll.interval.ms` )</li><li>Minska storleken på meddelandets batch för att påskynda bearbetningen</li><li>Förbättra bearbetningen av parallellisering för att undvika att blockera Consumer. pollning ()</li></ul> Det är förmodligen bara att använda en kombination av de tre.
Nätverks undantag vid högt genererat data flöde | Använder du Java client + standard max. Request. size?  Dina begär Anden kan vara för stora. | Se Java configs ovan.

## <a name="next-steps"></a>Nästa steg
Se [Azure-prenumeration och tjänst begränsningar, kvoter](..//azure-resource-manager/management/azure-subscription-service-limits.md) och begränsningar för kvoter och begränsningar för alla Azure-tjänster. 
