---
title: Scenarier för Query Store – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs några scenarier för Frågearkivet i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710557"
---
# <a name="usage-scenarios-for-query-store"></a>Användnings scenarier för Frågearkivet

**Gäller för:** Azure Database for PostgreSQL-enskild server version 9,6, 10, 11

Du kan använda Query Store i många olika scenarier där det är viktigt att spåra och underhålla prestanda för förutsägbara arbets belastningar. Överväg följande exempel: 
- Identifiera och justera de dyraste frågorna 
- A/B-testning 
- Hålla prestanda stabila under uppgraderingar 
- Identifiera och förbättra ad hoc-arbetsbelastningar 

## <a name="identify-and-tune-expensive-queries"></a>Identifiera och finjustera dyra frågor 

### <a name="identify-longest-running-queries"></a>Identifiera de längsta körnings frågorna 
Använd vyn [query Performance Insight](concepts-query-performance-insight.md) i Azure Portal för att snabbt identifiera de längsta frågor som körs. Dessa frågor brukar ofta förbruka en betydande mängd resurser. Optimering av dina längsta frågor kan förbättra prestanda genom att frigöra resurser för användning av andra frågor som körs på systemet. 

### <a name="target-queries-with-performance-deltas"></a>Mål frågor med prestanda delta 
Query Store delar upp prestanda data i tid Windows, så att du kan spåra en frågas prestanda över tid. Detta hjälper dig att identifiera exakt vilka frågor som bidrar till en ökning av den totala tiden som ägnats åt. Därför kan du göra en fel sökning av din arbets belastning.

### <a name="tuning-expensive-queries"></a>Finjustera dyra frågor 
När du identifierar en fråga med optimala prestanda beror åtgärden som du vidtar på problemets typ: 
- Använd [prestanda rekommendationer](concepts-performance-recommendations.md) för att avgöra om det finns några föreslagna index. Om du väljer Ja skapar du indexet och använder sedan Query Store för att utvärdera frågeresultaten när du har skapat indexet. 
- Kontrol lera att statistiken är uppdaterad för de underliggande tabellerna som används av frågan.
- Överväg att skriva om dyra frågor. Du kan till exempel dra nytta av Query parameterisering och minska användningen av dynamisk SQL. Implementera optimal logik vid läsning av data som att använda data filtrering på databas sidan, inte på program sidan. 


## <a name="ab-testing"></a>A/B-testning 
Använd Query Store för att jämföra arbets belastnings prestanda innan och efter ett program som du planerar att införa. Exempel på scenarier för att använda Query Store för att utvärdera påverkan på miljön eller program ändringar i arbets belastnings prestanda: 
- Distribuera en ny version av ett program. 
- Lägger till ytterligare resurser på servern. 
- Skapa saknade index för tabeller som refereras av dyra frågor. 
 
Använd följande arbets flöde i något av dessa scenarion: 
1. Kör din arbets belastning med Frågearkivet före den planerade ändringen för att skapa en bas linje för prestanda. 
2. Tillämpa program ändringar vid den kontrollerade tidpunkten. 
3. Fortsätt att köra arbets belastningen tillräckligt länge för att generera systemets prestanda avbildning efter ändringen. 
4. Jämför resultat från före och efter ändringen. 
5. Bestäm om du vill behålla ändringen eller återställningen. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifiera och förbättra ad hoc-arbetsbelastningar 
Vissa arbets belastningar har inga dominerande frågor som du kan finjustera för att förbättra övergripande program prestanda. Dessa arbets belastningar karakteriseras vanligt vis med ett relativt stort antal unika frågor, där var och en av dem förbrukar en del av system resurserna. Varje unik fråga körs sällan, så att körnings förbrukningen är för tillfället inte kritisk. Å andra sidan, med tanke på att programmet genererar nya frågor hela tiden, används en stor del av system resurserna i frågans kompilering, vilket inte är optimalt. Den här situationen inträffar vanligt vis om ditt program genererar frågor (i stället för att använda lagrade procedurer eller parametriserade frågor) eller om det förlitar sig på objekt Relations mappnings ramverk som genererar frågor som standard. 
 
Om du har kontroll över program koden kan du överväga att skriva om data åtkomst lagret för att använda lagrade procedurer eller parametriserade frågor. Den här situationen kan dock också förbättras utan program ändringar genom att tvinga fram Parameterisering för hela databasen (alla frågor) eller för de enskilda certifikatmallarna med samma fråga-hash. 

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [metod tips för att använda Query Store](concepts-query-store-best-practices.md)