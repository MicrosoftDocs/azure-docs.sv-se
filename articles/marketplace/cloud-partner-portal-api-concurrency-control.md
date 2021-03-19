---
title: Concurrency-kontroll – Azure Marketplace
description: 'Samtidiga kontroll strategier för API: erna för Cloud Partner Portal Publishing.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88031656"
---
# <a name="concurrency-control"></a>Samtidighetskontroll

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Varje anrop till API: erna för Cloud Partner Portal publicering måste uttryckligen ange vilken strategi för concurrency-kontroll som ska användas. Om du inte kan ange **If-Match-** huvudet uppstår ett HTTP 400-felsvar. Vi erbjuder två strategier för samtidiga kontroller.

-   **Optimistiskt** – klienten som utför uppdateringen verifieras om data har ändrats sedan den senast läste data.
-   **Senaste en WINS** – klienten uppdaterar data direkt, oavsett om det har ändrats av ett annat program sedan den senaste läsnings tiden.

<a name="optimistic-concurrency-workflow"></a>Optimistiskt concurrency-arbetsflöde
-------------------------------

Vi rekommenderar att du använder en optimistisk samtidighets strategi, med följande arbets flöde, för att garantera att inga oväntade ändringar görs i dina resurser.

1.  Hämta en entitet med hjälp av API: erna. Svaret innehåller ett ETag-värde som identifierar den aktuella lagrade versionen av entiteten (vid tidpunkten för svaret).
2.  Vid tidpunkten för uppdateringen inkluderar du samma ETag-värde i det obligatoriska **If-Match-** huvudet för begäran.
3.  API: n jämför det ETag-värde som togs emot i begäran med det aktuella ETag-värdet för entiteten i en atomisk transaktion.
    *   Om ETag-värdena skiljer sig, returnerar API: et ett `412 Precondition Failed` http-svar. Det här felet anger att antingen en annan process har uppdaterat entiteten sedan klienten senast hämtade den eller att ETag-värdet som angavs i begäran är felaktigt.
    *  Om ETag-värdena är identiska, eller **om-match-** huvudet innehåller jokertecknet asterisk ( `*` ), utför API: et den begärda åtgärden. API-åtgärden uppdaterar även det lagrade ETag-värdet för entiteten.


> [!NOTE]
> Om du anger jokertecken (*) i rubriken **If-Match** i API: et används den sista engångs-WINS-strategin. I detta fall uppstår inte ETag-jämförelsen och resursen uppdateras utan några kontroller. 
