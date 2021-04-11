---
title: ta med fil
description: ta med fil
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 698219a446240950b9a661f923109fbb2f863556
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178517"
---
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s tabell-API:

|Funktion | Azure Table Storage | Tabell-API för Azure Cosmos DB |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid. | Ensiffrig millisekundsvarstid för läsningar och skrivningar med <10 ms svarstid för läsning och <15 ms svarstid vid skrivning vid 99:e percentilen, i valfri skala, var som helst i världen. |
| Dataflöde | Modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s. | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](../articles/cosmos-db/request-units.md) som understöds av serviceavtal. Konton har ingen övre gräns för data flöde och stöd >10 000 000 åtgärder/s per tabell (i det etablerade data flödes läget). |
| Global distribution | En enda region med en valfri läsbar sekundär Läs region för hög tillgänglighet som har stöd för automatisk och manuell växling av konto. | [Nyckelfärdig global distribution](../articles/cosmos-db/distribute-data-globally.md) mellan 1 och 30+ regioner. Stöd för [automatisk och manuell redundans](../articles/cosmos-db/high-availability.md) när som helst och var som helst i världen. |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index. | Automatisk och fullständig indexering för alla egenskaper, utan indexhantering. |
| Fråga | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. |
| Konsekvens | Stark inom primär region. Eventuell inom sekundär region. | [Fem väldefinierade konsekvens nivåer](../articles/cosmos-db/consistency-levels.md) för att handla om tillgänglighet, svars tid, data flöde och konsekvens baserat på dina program behov. |
| Priser | Förbruknings-baserade. | Tillgängligt i både [konsumtions-](../articles/cosmos-db/serverless.md) och [etablerade kapacitets](../articles/cosmos-db/set-throughput.md) lägen. |
| Serviceavtal | 99,99 % tillgänglighet. | Serviceavtal med 99,99 % tillgänglighet för alla konton med tillgång till en eller flera regioner med konsekvensmodellen ”relaxed” (avslappnad). 99,999 % läsningstillgänglighet för alla databaskonton med tillgång till flera regioner. [Branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) när detta blivit allmänt tillgängligt. |
