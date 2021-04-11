---
title: Felsöka Azure Cosmos DB Felaktiga begär ande undantag
description: Lär dig hur du diagnostiserar och åtgärdar Felaktiga undantag för begär Anden, till exempel inmatat innehåll eller partitionsnyckel är ogiltigt, partitionsnyckel matchar inte i Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556189"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnostisera och Felsök dåliga undantag i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP-statuskoden 400 representerar begäran innehåller ogiltiga data eller saknar nödvändiga parametrar.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>ID-egenskapen saknas
I det här scenariot är det vanligt att se felet:

*Inmatat innehåll är ogiltigt eftersom de obligatoriska egenskaperna-ID; "-saknas*

Ett svar med det här felet innebär att JSON-dokumentet som skickas till tjänsten saknar den obligatoriska ID-egenskapen.

### <a name="solution"></a>Lösning
Ange en `id` egenskap med ett sträng värde enligt rest- [specifikationen](/rest/api/cosmos-db/documents) som en del av dokumentet. SDK: erna genererar inte automatiskt värden för den här egenskapen.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Ogiltig typ av partitionsnyckel
I det här scenariot är det vanligt att se fel som:

*Partitionsnyckel... är ogiltig.*

Ett svar med det här felet innebär att värdet för partitionsnyckel är av en ogiltig typ.

### <a name="solution"></a>Lösning
Värdet för partitionsnyckel måste vara en sträng eller en siffra. kontrol lera att värdet är av den förväntade typen.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Felaktigt värde för partitionsnyckel
I det här scenariot är det vanligt att se felet:

*PartitionKey som extraherats från dokumentet matchar inte det som angetts i rubriken*

Ett svar med det här felet innebär att du kör en åtgärd och skickar ett nyckel värde för en partition som inte matchar dokumentets Body-värde för den förväntade egenskapen. Om samlingens partitions nyckel Sök väg är `/myPartitionKey` , har dokumentet en egenskap `myPartitionKey` som kallas med ett värde som inte överensstämmer med det som angavs som PARTITIONSNYCKEL när SDK-metoden anropades.

### <a name="solution"></a>Lösning
Skicka parameter värde parametern för partitionen som matchar värdet för dokument egenskapen.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).
* [Diagnostisera och Felsök](troubleshoot-java-sdk-v4-sql.md) problem när du använder Azure Cosmos DB Java v4 SDK.
* Lär dig mer om prestanda rikt linjer för [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
