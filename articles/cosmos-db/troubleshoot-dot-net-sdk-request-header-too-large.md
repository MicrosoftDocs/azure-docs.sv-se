---
title: Felsök fel meddelandet "begäran är för stor" eller 400 Felaktig begäran i Azure Cosmos DB
description: Lär dig hur du diagnostiserar och korrigerar rubriken för ett stort undantag för begäran.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340530"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostisera och Felsök Azure Cosmos DB meddelande rubriken är för stor
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Meddelandet "begär ande huvud är för stort" har genererats med en HTTP-felkod 400. Det här felet uppstår om storleken på begär ande rubriken har vuxit så stor att den överskrider den maximalt tillåtna storleken. Vi rekommenderar att du använder den senaste versionen av SDK. Använd minst version 3. x eller 2. x, eftersom dessa versioner lägger till huvud storleks spårning i undantags meddelandet.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Meddelandet "begäran är för stor" visas om sessionen eller fortsättnings-token är för stor. I följande avsnitt beskrivs orsaken till problemet och dess lösning i varje kategori.

### <a name="session-token-is-too-large"></a>Sessionens token är för stor

#### <a name="cause"></a>Orsak:
En felaktig 400-begäran beror förmodligen på att sessionstoken är för stor. Om följande påståenden är sanna är sessionstoken för stor:

* Felet uppstår vid punkt åtgärder som att skapa, läsa och uppdatera där det inte finns någon fortsättnings-token.
* Undantaget startades utan att göra några ändringar i programmet. Sessionstoken ökar när antalet partitioner ökar i behållaren. Antalet partitioner ökar när mängden data ökar eller om data flödet ökar.

#### <a name="temporary-mitigation"></a>Temporär minskning: 
Starta om klient programmet för att återställa alla sessionstoken. Slutligen kommer sessionstoken att växa tillbaka till den tidigare storlek som orsakade problemet. Undvik det här problemet fullständigt genom att använda lösningen i nästa avsnitt.

#### <a name="solution"></a>Lösning:
1. Följ anvisningarna i artiklarna om prestanda tips för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) eller [.NET v2](performance-tips.md) . Konvertera programmet så att det använder direkt anslutnings läge med Transmission Control Protocol (TCP). Det direkta anslutnings läget med TCP-protokollet har inte begränsningar för rubrik storlek som HTTP-protokollet, så det undviker det här problemet. Se till att använda den senaste versionen av SDK, som har en korrigering för frågor om tjänst-interop inte är tillgängligt.
1. Om det direkta anslutnings läget med TCP-protokollet inte är ett alternativ för din arbets belastning kan du undvika det genom att ändra [klientens konsekvens nivå](how-to-manage-consistency.md). Sessionstoken används endast för konsekvens av sessioner, vilket är standard konsekvens nivån för Azure Cosmos DB. Andra konsekvens nivåer använder inte sessionstoken.

### <a name="continuation-token-is-too-large"></a>Fortsättnings-token är för stor

#### <a name="cause"></a>Orsak:
Den dåliga 400-begäran sker vid frågor om att den fortsatta token används om den fortsatta token har blivit för stor eller om olika frågor har olika storlekar för att ändra token.
    
#### <a name="solution"></a>Lösning:
1. Följ anvisningarna i artiklarna om prestanda tips för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) eller [.NET v2](performance-tips.md) . Konvertera programmet så att det använder det direkta anslutnings läget med TCP-protokollet. Det direkta anslutnings läget med TCP-protokollet har inte begränsningar för rubrik storlek som HTTP-protokollet, så det undviker det här problemet. 
1. Om det direkta anslutnings läget med TCP-protokollet inte är ett alternativ för din arbets belastning ställer du in `ResponseContinuationTokenLimitInKb` alternativet. Du kan hitta det här alternativet i `FeedOptions` i v2 eller `QueryRequestOptions` v3.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).
