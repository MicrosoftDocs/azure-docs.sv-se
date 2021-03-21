---
title: Migrera ditt program till att använda Azure Cosmos DB .NET SDK 2,0 (Microsoft. Azure. Cosmos)
description: Lär dig hur du uppgraderar ditt befintliga .NET-program från v1 SDK till .NET SDK v2 för Core-API (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94550108"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrera ditt program till att använda Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> Det är viktigt att Observera att v3 för .NET SDK är för närvarande tillgängligt och att [det finns en](migrate-dotnet-v3.md)migrering från v2 till v3. Mer information om Azure Cosmos DB .NET SDK v2 finns i [versions anteckningar](sql-api-sdk-dotnet.md), [.net GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmos-dotnet-v2), [prestanda tips](performance-tips.md)för .NET SDK v2 och [fel söknings guiden](troubleshoot-dot-net-sdk.md).
>

I den här artikeln beskrivs några av övervägandena för att uppgradera ditt befintliga v1 .NET-program till Azure Cosmos DB .NET SDK v2 för Core-API (SQL). Azure Cosmos DB .NET SDK v2 motsvarar `Microsoft.Azure.DocumentDB` namn området. Du kan använda informationen i det här dokumentet om du migrerar programmet från någon av följande Azure Cosmos DB .NET-plattformar för att använda v2 SDK `Microsoft.Azure.Cosmos` :

* Azure Cosmos DB .NET Framework v1 SDK för SQL API
* Azure Cosmos DB .NET Core SDK v1 för SQL API

## <a name="whats-available-in-the-net-v2-sdk"></a>Vad är tillgängligt i .NET v2 SDK

V2-SDK: n innehåller många förbättringar för användbarhet och prestanda, inklusive:

* Stöd för TCP Direct-läge för klienter som inte är Windows-klienter
* Skriv stöd för flera regioner
* Förbättringar av frågans prestanda
* Stöd för geospatiala/geometri samlingar och indexering
* Förbättrade förbättringar för direkt-/TCP-transportprovidern
* Uppdateringar i direkt TCP transport stack för att minska antalet anslutningar som upprättats
* Förbättringar i tids fördröjnings minskning i RequestTimeout

De flesta av logiken för omprövning och lägre nivåer av SDK förblir i stort sett oförändrade.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Varför migrera till .NET v2 SDK

Förutom de många prestanda förbättringarna kommer nya funktioner för funktioner som görs i den senaste SDK inte att flyttas till äldre versioner.

Dessutom kommer de äldre SDK: erna att ersättas av nyare versioner och v1 SDK kommer att gå i [underhålls läge](sql-api-sdk-dotnet.md). För bästa utvecklings upplevelse rekommenderar vi att du migrerar ditt program till en senare version av SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Större ändringar från v1 SDK till v2 SDK

### <a name="direct-mode--tcp"></a>Direkt läge + TCP

.NET v2 SDK stöder nu både Direct-och gateway-läge. Direct-läget stöder anslutning via TCP-protokollet och ger bättre prestanda när den ansluts direkt till Server dels replikerna med färre nätverks hopp.

Mer information finns i [hand boken för Azure Cosmos DB SQL SDK Connectivity Modes](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formatering av sessionstoken

V2-SDK: n använder inte längre formatet *enkel* sessions-token som används i v1, i stället SDK: n använder *vektor* format. Formatet ska konverteras vid överföring till klient programmet med olika versioner eftersom formaten inte är utbytbara.

Mer information finns i [konvertera format för sessionstillstånd i .NET SDK](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Använda .NET Change feed processor SDK

.NET Change feed processor Library 2.1. x kräver `Microsoft.Azure.DocumentDB` 2,0 eller senare.

2.1. x-biblioteket har följande viktiga ändringar:

* Förbättringar av stabilitet och diagnos
* Förbättrad hantering av fel och undantag
* Ytterligare stöd för partitionerade leasing samlingar
* Avancerade tillägg för att implementera `ChangeFeedDocument` gränssnittet och klassen för ytterligare fel hantering och spårning
* Stöd har lagts till för att använda ett anpassat Arkiv för att spara fortsättnings-token per partition

Mer information finns i [versions](sql-api-sdk-dotnet-changefeed.md)information om Change feed processor Library.

### <a name="using-the-bulk-executor-library"></a>Använda bulk utförar-biblioteket

Det finns för närvarande ett beroende på den Azure Cosmos DB .NET SDK-2.5.1 eller senare i utförar-biblioteket i v2.

Mer information finns i [Översikt över Azure Cosmos DB-biblioteket för utförar-bibliotek](bulk-executor-overview.md) och [versions information](sql-api-sdk-bulk-executor-dot-net.md)för .net bulk utförar Library.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [ytterligare prestanda tips](sql-api-get-started.md) med Azure Cosmos dB för SQL API v2 för optimering av ditt program för att uppnå högsta prestanda
* Lär dig mer om [vad du kan göra med v2 SDK](sql-api-dotnet-samples.md)