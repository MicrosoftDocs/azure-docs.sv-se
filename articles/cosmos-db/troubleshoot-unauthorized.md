---
title: Felsöka Azure Cosmos DB obehöriga undantag
description: Lär dig hur du diagnostiserar och åtgärdar otillåtna undantag.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411175"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB obehöriga undantag
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: MAC-signaturen som hittades i HTTP-begäran är inte samma som den beräknade signaturen.
Om du har fått 401-fel meddelandet "MAC-signaturen som hittades i HTTP-förfrågan inte är samma som den beräknade signaturen, kan den orsakas av följande scenarier.

För äldre SDK: er kan undantaget visas som ett ogiltigt JSON-undantag i stället för rätt 401 obehörig undantag. Nyare SDK: er hanterar det här scenariot korrekt och ger ett giltigt fel meddelande.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för obehöriga undantag.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>Nyckeln roterades inte ordentligt i det vanligaste scenariot
401 MAC-signaturen visas strax efter en nyckel rotation och slutar att svara utan några ändringar. 

#### <a name="solution"></a>Lösning:
Nyckeln roterades och följer inte de [bästa metoderna](secure-access-to-data.md#key-rotation). Azure Cosmos DB konto nyckel rotationen kan ta var som helst från några sekunder till några dagar, beroende på Azure Cosmos DB kontots storlek.

### <a name="the-key-is-misconfigured"></a>Nyckeln är felkonfigurerad 
Problemet med 401 MAC-signaturen är konsekvent och inträffar för alla anrop som använder den nyckeln.

#### <a name="solution"></a>Lösning:
Nyckeln är felkonfigurerad i programmet och använder fel nyckel för kontot, eller så kopierades inte hela nyckeln.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Programmet använder skrivskyddade nycklar för Skriv åtgärder
Problemet 401 MAC-signatur sker bara för Skriv åtgärder som att skapa eller ersätta, men Läs begär Anden lyckades.

#### <a name="solution"></a>Lösning:
Växla programmet till att använda en Läs/skriv-nyckel för att utföra åtgärderna.

### <a name="race-condition-with-create-container"></a>Villkor för tävling med skapa behållare
Problemet 401 MAC-signaturen visas strax efter att containern har skapats. Det här problemet uppstår endast tills behållaren har skapats.

#### <a name="solution"></a>Lösning:
Det finns ett tävlings villkor med att skapa behållare. En program instans försöker komma åt behållaren innan behållaren har skapats. Det vanligaste scenariot för det här tävlings tillståndet är om programmet körs och behållaren tas bort och återskapas med samma namn. SDK försöker använda den nya behållaren, men behållar skapandet pågår fortfarande så att den inte har nycklarna.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).
* [Diagnostisera och Felsök](troubleshoot-java-sdk-v4-sql.md) problem när du använder Azure Cosmos DB Java v4 SDK.
* Lär dig mer om prestanda rikt linjer för [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).