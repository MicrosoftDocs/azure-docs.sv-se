---
title: Förfaller data i Azure Cosmos DB med Time to Live
description: Med TTL ger Microsoft Azure Cosmos DB möjlighet att automatiskt rensa dokument från systemet efter en viss tids period.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: cf9d0aea9ab9e79a5f184a42e1bb785b6fb870a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360096"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med **Time to Live** eller TTL ger Azure Cosmos DB möjlighet att ta bort objekt automatiskt från en behållare efter en viss tids period. Som standard kan du ange Time to Live på behållar nivån och åsidosätta värdet per objekt-basis. När du har ställt in TTL på en behållare eller på en objekt nivå, tar Azure Cosmos DB automatiskt bort objekten efter tids perioden, sedan den tid de senast ändrades. Time to Live-värdet är konfigurerat på några sekunder. När du konfigurerar TTL tar systemet automatiskt bort de utgångna objekten baserat på TTL-värdet, utan att det krävs någon borttagnings åtgärd som uttryckligen utfärdas av klient programmet. Det maximala värdet för TTL är 2147483647.

Borttagning av utgångna objekt är en bakgrunds aktivitet som förbrukar [enheter](request-units.md)med överdrivet utrymme, det vill säga enheter som inte har använts av användar förfrågningar. Även om TTL-värdet har upphört att gälla, om behållaren överbelastas med begär Anden och om det inte finns tillräckligt många RU-objekt, så fördröjs data borttagningen. Data tas bort när det finns tillräckligt med ru: er tillgängliga för att utföra borttagnings åtgärden. Även om data borttagningen fördröjs, returneras inte data av några frågor (med valfritt API) När TTL har upphört att gälla.

> Det här innehållet är relaterat till Azure Cosmos DB transaktions arkivets TTL. Om du letar efter analys lagrings-TTL, som möjliggör NoETL HTAP-scenarier via [Azure Synapse-länken](./synapse-link.md), klickar du [här](./analytical-store-introduction.md#analytical-ttl).

## <a name="time-to-live-for-containers-and-items"></a>Tid till Live för behållare och objekt

TTL-värdet är inställt på några sekunder och tolkas som en delta från den tidpunkt då ett objekt senast ändrades. Du kan ställa in Time to Live på en behållare eller ett objekt i behållaren:

1. **Time to Live på en behållare** (anges med `DefaultTimeToLive` ):

   - Om det saknas (eller är inställt på null), kommer objekten inte att förfalla automatiskt.

   - Om det är tillgängligt och värdet har angetts till "-1", är det lika med oändlighet och objekt förfaller inte som standard.

   - Om detta är tillgängligt och värdet är inställt på *ett tal som* *inte är noll* , kommer objekten att förfalla *"n"* sekunder efter deras senaste ändrings tid.

2. **Time to Live på ett objekt** (anges med `ttl` ):

   - Den här egenskapen gäller bara om `DefaultTimeToLive` finns och är inte inställd på null för den överordnade behållaren.

   - Om den är tillgänglig åsidosätter den den `DefaultTimeToLive` överordnade behållarens värde.

## <a name="time-to-live-configurations"></a>Time to Live konfigurationer

- Om TTL är inställt på *"n"* i en behållare går objekten i den behållaren ut efter *n* sekunder.  Om det finns objekt i samma behållare som har sin egen tid att leva, anger du till-1 (indikerar att de inte går ut) eller om vissa objekt har åsidosatt tiden till Live-inställningen med ett annat antal, förfaller dessa objekt baserat på sitt eget konfigurerade TTL-värde.

- Om TTL inte är inställt på en behållare, har tiden till Live på ett objekt i den här behållaren ingen påverkan.

- Om TTL på en behållare är inställt på-1, kommer ett objekt i den här behållaren som har TTL-värdet n att förfalla efter n sekunder, och återstående objekt upphör inte att gälla.

## <a name="examples"></a>Exempel

I det här avsnittet visas några exempel på olika tidpunkter för Live-värden som tilldelas behållare och objekt:

### <a name="example-1"></a>Exempel 1

TTL för container är inställt på null (DefaultTimeToLive = null)

|TTL för objekt| Resultat|
|---|---|
|TTL = null|TTL har inaktiverats. Objektet upphör aldrig att gälla (standard).|
|TTL =-1|TTL har inaktiverats. Objektet upphör aldrig att gälla.|
|TTL = 2000|TTL har inaktiverats. Objektet upphör aldrig att gälla.|

### <a name="example-2"></a>Exempel 2

TTL för container är inställt på-1 (DefaultTimeToLive =-1)

|TTL för objekt| Resultat|
|---|---|
|TTL = null|TTL har Aktiver ATS. Objektet upphör aldrig att gälla (standard).|
|TTL =-1|TTL har Aktiver ATS. Objektet upphör aldrig att gälla.|
|TTL = 2000|TTL har Aktiver ATS. Objektet upphör att gälla efter 2000 sekunder.|

### <a name="example-3"></a>Exempel 3

TTL på container är inställt på 1000 (DefaultTimeToLive = 1000)

|TTL för objekt| Resultat|
|---|---|
|TTL = null|TTL har Aktiver ATS. Objektet upphör att gälla efter 1000 sekunder (standard).|
|TTL =-1|TTL har Aktiver ATS. Objektet upphör aldrig att gälla.|
|TTL = 2000|TTL har Aktiver ATS. Objektet upphör att gälla efter 2000 sekunder.|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar Time to Live i följande artiklar:

- [Så här konfigurerar du Time to Live](how-to-time-to-live.md)