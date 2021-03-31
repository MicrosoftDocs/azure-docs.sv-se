---
title: Konvertera format för sessionstoken i .NET SDK – Azure Cosmos DB
description: Lär dig hur du konverterar format för sessionstoken för att säkerställa kompatibilitet mellan olika .NET SDK-versioner
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342053"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Konvertera format för sessionstoken i .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln förklarar hur du konverterar mellan olika format för sessionsläge för att säkerställa kompatibilitet mellan SDK-versioner.

> [!NOTE]
> Som standard håller SDK reda på sessionstoken automatiskt och använder den senaste sessionstoken.  Mer information hittar du i [använda token för sessioner](how-to-manage-consistency.md#utilize-session-tokens). Anvisningarna i den här artikeln gäller endast följande villkor:
> * Ditt Azure Cosmos DB-konto använder konsekvens för sessioner.
> * Du hanterar sessionens tokens manuellt.
> * Du använder flera versioner av SDK på samma tidpunkt.

## <a name="session-token-formats"></a>Format för sessionstoken

Det finns två format för sessionstoken: **enkla** och **vektorer**.  Dessa två format är inte utbytbara, vilket innebär att formatet ska konverteras vid överföring till klient programmet med olika versioner.
- Formatet för **enkel** session-token används av .NET SDK V1 (Microsoft.Azure.DocumentDB-version 1. x)
- Formatet för **Vector** session token används av .NET SDK V2 (Microsoft.Azure.DocumentDB-version 2. x)

### <a name="simple-session-token"></a>Enkel sessionstoken

En enkel session-token har följande format: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Vector-session-token

En Vector-sessions-token har följande format: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Konvertera till enkel sessionstoken

Om du vill skicka en sessionstoken till klienten med .NET SDK v1 använder du ett **enkelt** format för sessionstoken.  Använd exempelvis följande exempel kod för att konvertera den.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Konvertera till Vector session-token

Om du vill skicka en sessionstoken till klienten med .NET SDK v2 använder du formatet **Vector** session token.  Använd exempelvis följande exempel kod för att konvertera den.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar:

* [Använd sessionstoken för att hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Välj rätt konsekvens nivå i Azure Cosmos DB](./consistency-levels.md)
* [Konsekvens, tillgänglighet och prestanda kompromisser i Azure Cosmos DB](./consistency-levels.md)
* [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](./consistency-levels.md)