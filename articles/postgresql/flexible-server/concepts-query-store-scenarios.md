---
title: Query Store-scenarier – Azure Database for PostgreSQL – Flex Server
description: Den här artikeln beskriver några scenarier för Query Store i Azure Database for PostgreSQL – Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559277"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Användningsscenarier för Query Store – flexibel server

**Gäller för:** Azure Database for PostgreSQL – version 11, 12 av en server

Du kan använda Query Store i en mängd olika scenarier där spårning och underhåll av förutsägbara arbetsbelastningsprestanda är avgörande. Överväg följande exempel: 
- Identifiera och justera de dyraste frågorna 
- A/B-testning 
- Hålla prestanda stabila under uppgraderingar 
- Identifiera och förbättra ad hoc-arbetsbelastningar 

## <a name="identify-and-tune-expensive-queries"></a>Identifiera och finjustera dyra frågor 

### <a name="identify-longest-running-queries"></a>Identifiera långvarigaste frågor 
Använd Query Store-vyer på azure_sys på servern för att snabbt identifiera de frågor som körs längst. De här frågorna förbrukar vanligtvis mest resurser. Genom att optimera dina långvarigaste frågor kan du förbättra prestandan genom att frigöra resurser som används av andra frågor som körs i systemet. 

### <a name="target-queries-with-performance-deltas"></a>Rikta frågor med prestandadelta 
Query Store segmentar prestandadata i tidsfönster så att du kan spåra en frågas prestanda över tid. På så sätt kan du identifiera exakt vilka frågor som bidrar till en ökning av den totala tid som spenderas. Därför kan du göra riktad felsökning av din arbetsbelastning.

### <a name="tuning-expensive-queries"></a>Justera dyra frågor 
När du identifierar en fråga med icke-optimala prestanda beror den åtgärd du gör på problemets natur: 
- Kontrollera att statistiken är uppdaterad för de underliggande tabeller som används av frågan.
- Överväg att skriva om dyra frågor. Du kan till exempel dra nytta av frågeparameterisering och minska användningen av dynamisk SQL. Implementera optimal logik vid läsning av data som att tillämpa datafiltrering på databassidan, inte på programsidan. 


## <a name="ab-testing"></a>A/B-testning 
Använd Query Store för att jämföra arbetsbelastningsprestanda före och efter en programändring som du planerar att införa eller före och efter migreringen. Exempelscenarier för att använda Query Store för att utvärdera effekten av ändringar i arbetsbelastningens prestanda: 
- Migrering mellan PostgreSQL-versioner. 
- Distribuera en ny version av ett program. 
- Lägga till ytterligare resurser på servern. 
- Skapa saknade index för tabeller som refereras av dyra frågor. 
- Migrering från enskild server till Flex Server. 
 
I något av dessa scenarier använder du följande arbetsflöde: 
1. Kör din arbetsbelastning med Query Store före den planerade ändringen för att generera en prestandabaslinje. 
2. Tillämpa programändring(er) vid den kontrollerade tidpunkten. 
3. Fortsätt att köra arbetsbelastningen tillräckligt länge för att generera en prestandaavbildning av systemet efter ändringen. 
4. Jämför resultat från före och efter ändringen. 
5. Bestäm om du vill behålla ändringen eller återställningen. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifiera och förbättra ad hoc-arbetsbelastningar 
Vissa arbetsbelastningar har inte dominerande frågor som du kan finjustera för att förbättra den övergripande programprestandan. Dessa arbetsbelastningar kännetecknas vanligtvis av ett relativt stort antal unika frågor, där var och en av dem förbrukar en del av systemresurserna. Varje unik fråga körs sällan, så individuellt är deras körningsförbrukning inte kritisk. Med tanke på att programmet genererar nya frågor hela tiden läggs å andra sidan en stor del av systemresurserna på frågekompilering, vilket inte är optimalt. Den här situationen inträffar vanligtvis om ditt program genererar frågor (i stället för att använda lagrade procedurer eller parametriserade frågor) eller om det förlitar sig på ramverk för objektrelationsmappning som genererar frågor som standard. 
 
Om du har kontroll över programkoden kan du skriva om dataåtkomstlagret för att använda lagrade procedurer eller parametriserade frågor. Den här situationen kan dock också förbättras utan programändringar genom att tillämpa frågeparameterisering för hela databasen (alla frågor) eller för enskilda frågemallar med samma fråge-hash. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [metodtipsen för att använda Query Store](concepts-query-store-best-practices.md)
