---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 5f2b77c7d8e1a2da9517183043231b717b6cceab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734075"
---
### <a name="general-query-limits"></a>Allmänna begränsningar för frågor

| Gräns | Beskrivning |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [frågespråk för Kusto](/azure/kusto/query/) som Azure datautforskaren. Se [Azure Monitor logg frågor språk skillnader](/azure/data-explorer/kusto/query/) för KQL språk element som inte stöds i Azure Monitor. |
| Azure-regioner | Logg frågor kan uppleva onödig belastning när data sträcker sig Log Analytics arbets ytor i flera Azure-regioner. Mer information finns i [fråga om begränsningar](../articles/azure-monitor/logs/scope.md#query-scope-limits) . |
| Frågor mellan resurser | Maximalt antal Application Insights-resurser och Log Analytics arbets ytor i en enda fråga som är begränsade till 100.<br>Frågan över resurser stöds inte i View Designer.<br>Frågan över resurser i logg aviseringar stöds i det nya scheduledQueryRules-API: et.<br>Se [gränser för kors resurs frågor](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits) för mer information. |

### <a name="user-query-throttling"></a>Begränsning av användar frågor
Azure Monitor har flera begränsnings gränser som skyddar mot användare som skickar ett stort antal frågor. Detta beteende kan eventuellt överbelasta systemets Server dels resurser och äventyra tjänstens svars tider. Följande gränser är utformade för att skydda kunder mot avbrott och säkerställa konsekvent service nivå. Användarens begränsning och begränsningar har utformats för att endast påverka extrema användnings scenarier och bör inte vara relevanta för typisk användning.


| Mått | Begränsa per användare | Beskrivning |
|:---|:---|:---|
| Samtidiga frågor | 5 | Om det redan finns 5 frågor som körs för användaren placeras alla nya frågor i en transaktionskö per användare. När en av de frågor som körs avslutas kommer nästa fråga att hämtas från kön och startas. Detta omfattar inte frågor från varnings regler.
| Tid i samtidighets kön | 3 minuter | Om en fråga finns i kön i mer än tre minuter utan att startas, avbryts den med ett HTTP-felsvar med koden 429. |
| Totalt antal frågor i samtidighets kön | 200 | När antalet frågor i kön når 200 kommer eventuella ytterligare frågor att avvisas med en HTTP-felkod 429. Det här talet är förutom de 5 frågor som kan köras samtidigt. |
| Frågefrekvens | 200 frågor per 30 sekunder | Detta är den övergripande hastigheten som frågor kan skickas av en enskild användare till alla arbets ytor.  Den här gränsen gäller för programmatiska frågor eller frågor som initieras av visualiserings delar som Azure-instrumentpaneler och sammanfattnings sidan Log Analytics-arbetsyta. |

- Optimera dina frågor enligt beskrivningen i [optimera logg frågor i Azure Monitor](../articles/azure-monitor/logs/query-optimization.md).
- Instrument paneler och arbets böcker kan innehålla flera frågor i en enda vy som genererar en burst med frågor varje gång de läser in eller uppdaterar. Överväg att dela upp dem i flera vyer som läses in på begäran. 
- I Power BI kan du bara extrahera sammansatta resultat i stället för obehandlade loggar.