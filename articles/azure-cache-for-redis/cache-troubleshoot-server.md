---
title: Felsöka problem på Azure Cache for Redis-serversidan
description: Lär dig hur du löser vanliga problem på Server sidan med Azure cache för Redis, till exempel minnes tryck, hög processor, tids krävande kommandon eller bandbredds begränsningar.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008924"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Felsöka problem på Azure Cache for Redis-serversidan

I det här avsnittet beskrivs fel söknings problem som uppstår på grund av ett villkor i en Azure-cache för Redis eller för den eller de virtuella datorer som är värd för det.

- [Minnestryck på Redis-server](#memory-pressure-on-redis-server)
- [Hög CPU-användning eller server belastning](#high-cpu-usage-or-server-load)
- [Tidskrävande kommandon](#long-running-commands)
- [Bandbreddsbegränsning på serversidan](#server-side-bandwidth-limitation)

> [!NOTE]
> Flera av fel söknings stegen i den här hand boken innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestanda mått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Minnestryck på Redis-server

Minnes belastningen på Server sidan leder till alla typer av prestanda problem som kan fördröja bearbetning av begär Anden. När minnes trycks träffar kan systemet använda data på disk. Den här _sidfel_ gör att systemet saktas ned avsevärt. Det finns flera möjliga orsaker till det här minnes trycket:

- Cachen fylls med data nära sin maximala kapacitet.
- Redis ser högt fragmentering i minnet. Fragmenteringen orsakas oftast av att stora objekt lagras eftersom Redis är optimerat för små objekt.

Redis exponerar två statistik via kommandot [info](https://redis.io/commands/info) som kan hjälpa dig att identifiera det här problemet: "used_memory" och "used_memory_rss". Du kan [Visa dessa mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) med hjälp av portalen.

Det finns flera ändringar du kan göra för att hålla minnesanvändningen felfri:

- [Konfigurera en minnes princip](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) och ange förfallo tider för dina nycklar. Den här principen kanske inte räcker om du har fragmentering.
- [Konfigurera ett maxmemory-reserverat värde](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) som är tillräckligt stort för att kompensera för fragmentering av minne.
- Dela upp dina stora cachelagrade objekt i mindre relaterade objekt.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) för mått, till exempel använt minne, så att du i god tid informeras om potentiell påverkan.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer minnes kapacitet.

## <a name="high-cpu-usage-or-server-load"></a>Hög CPU-användning eller server belastning

En hög server belastning eller CPU-användning innebär att servern inte kan bearbeta begär Anden inom rimlig tid. Servern kan vara långsam att svara och kan inte fortsätta med begär ande frekvenser.

[Övervaka mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) som CPU eller server belastning. Titta efter toppar i CPU-användning som motsvarar tids gränser.

Det finns flera ändringar som du kan göra för att minimera hög server belastning:

- Undersök vad som orsakar CPU-toppar, till exempel [långvariga kommandon](#long-running-commands) som anges nedan eller sidfel på grund av hög minnes belastning.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) för mått som processor-eller server belastning för att bli informerad om potentiella påverkan.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer processor kapacitet.

## <a name="long-running-commands"></a>Tidskrävande kommandon

Vissa redis-kommandon är dyrare att köra än andra. I [dokumentationen för Redis-kommandon](https://redis.io/commands) visas tids komplexiteten för varje kommando. Eftersom bearbetning av Redis-kommandona är en enkel tråd, kommer ett kommando som tar tid att köras att blockera alla andra som kommer efter det. Du bör granska de kommandon som du utfärdar till Redis-servern för att förstå deras prestanda påverkan. Till exempel används kommandot [nycklar](https://redis.io/commands/keys) ofta utan att du vet att det är en O (N)-åtgärd. Du kan undvika nycklar med hjälp av [Sök](https://redis.io/commands/scan) funktionen för att minska processor toppar.

Med hjälp av kommandot [SLOWLOG](https://redis.io/commands/slowlog) kan du mäta dyra kommandon som körs mot servern.

## <a name="server-side-bandwidth-limitation"></a>Bandbreddsbegränsning på serversidan

Olika cachestorlek har olika bandbredds kapacitet för nätverk. Om servern överskrider den tillgängliga bandbredden så skickas inte data till klienten så snabbt. Förfrågningar om klienter kan ha nått tids gränsen på grund av att servern inte kan skicka data till klienten tillräckligt snabbt.

Måtten "cache Read" och "cache Write" kan användas för att se hur mycket bandbredd som används på Server sidan. Du kan [Visa dessa mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) i portalen.

För att undvika situationer där bandbredds användningen för nätverk är nära maximal kapacitet:

- Ändra klientens anrops beteende för att minska nätverks behovet.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) för mått såsom cacheläsning och cacheskrivning så att du tidigt varnas om potentiella problem.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer bandbredds kapacitet för nätverk.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-klientsidan](cache-troubleshoot-client.md)
- [Välja rätt nivå](cache-overview.md#choosing-the-right-tier)
- [Hur kan jag mäta och testa prestanda för mitt cacheminne?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Övervaka Azure Cache for Redis](cache-how-to-monitor.md)
- [Hur kan jag köra Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands)
