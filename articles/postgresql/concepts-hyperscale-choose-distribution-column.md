---
title: Välj distributions kolumner – storskalig (citus)-Azure Database for PostgreSQL
description: Lär dig hur du väljer distributions kolumner i vanliga scenarier i Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929344"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Välj distributions kolumner i Azure Database for PostgreSQL – storskalig (citus)

Att välja distributionskolumn för varje tabell är ett av de viktigaste besluten om modellering. Azure Database for PostgreSQL – citus) lagrar rader i Shards baserat på värdet för radernas distributions kolumn.

Rätt alternativ grupper relaterade data tillsammans på samma fysiska noder, vilket gör att frågor snabbt och lägger till stöd för alla SQL-funktioner. Ett felaktigt val gör att systemet körs långsamt och inte stöder alla SQL-funktioner på noderna.

Den här artikeln innehåller tips för distributions kolumner för de två vanligaste citus-scenarierna.

### <a name="multi-tenant-apps"></a>Appar för flera klienter

Arkitekturen för flera innehavare använder en form av hierarkisk databas modellering för att distribuera frågor över noder i Server gruppen. Överst i Datahierarkin kallas *klient-ID* och måste lagras i en kolumn i varje tabell.

Citus (storskalig) kontrollerar frågor för att se vilket klient-ID de inkluderar och hittar matchande tabell-Shard. Den dirigerar frågan till en enda arbetsnoden som innehåller Shard. Att köra en fråga med alla relevanta data som placeras på samma nod kallas samplacering.

Följande diagram illustrerar samplacering i data modellen för flera innehavare. Den innehåller två tabeller, konton och kampanjer, som var och en distribueras av `account_id` . De skuggade rutorna representerar Shards. Gröna Shards lagras tillsammans på en arbetsnoden och blå Shards lagras på en annan arbetsnod. Observera att en kopplings fråga mellan konton och kampanjer har alla data som krävs tillsammans på en nod när båda tabellerna är begränsade till samma konto \_ -ID.

![Samplacering för flera klienter](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Om du vill använda den här designen i ditt eget schema, identifierar du vad som utgör en klient i ditt program. Vanliga instanser är företag, konto, organisation eller kund. Kolumn namnet kommer att vara något som liknar `company_id` eller `customer_id` . Undersök var och en av dina frågor och fråga dig själv, så fungerar det om det hade ytterligare WHERE-satser för att begränsa alla tabeller som ingår i rader med samma klient-ID?
Frågor i modellen för flera klienter är begränsade till en klient. Till exempel är frågor om försäljning eller lager begränsade inom en viss butik.

#### <a name="best-practices"></a>Bästa praxis

-   **Partitionera distribuerade tabeller med en gemensam klient \_ -ID-kolumn.** I ett SaaS-program där klienter är företag, \_ är klient-ID: t sannolikt företags-ID: t \_ .
-   **Konvertera små kors klient tabeller till referens tabeller.** När flera klienter delar en liten tabell med information distribuerar du den som en referens tabell.
-   **Begränsa filtreringen av alla program frågor efter klient \_ -ID.** Varje fråga bör begära information för en klient i taget.

Läs [själv studie kursen om flera innehavare](./tutorial-design-database-hyperscale-multi-tenant.md) för ett exempel på hur du skapar den här typen av program.

### <a name="real-time-apps"></a>Realtidsappar

Arkitekturen för flera innehavare introducerar en hierarkisk struktur och använder data på samma plats för att dirigera frågor per klient. I motsatsen är real tids arkitekturer beroende av vissa distributions egenskaper för data för att uppnå hög parallell bearbetning.

Vi använder "entitets-ID" som en term för distributions kolumner i real tids modellen. Typiska entiteter är användare, värdar eller enheter.

Real tids frågor efterfrågar vanligt vis numeriska mängder grupperade efter datum eller kategori. Citus (storskalig) skickar dessa frågor till varje Shard för partiella resultat och sammanställer det slutliga svaret på koordinator-noden. Frågor körs snabbast när så många noder bidrar som möjligt, och när ingen enskild nod måste göra en oproportionerlig mängd arbete.

#### <a name="best-practices"></a>Bästa praxis

-   **Välj en kolumn med hög kardinalitet som distributions kolumn.** För jämförelse är ett status fält i en order tabell med värdena New, betald och levererad ett dåligt val av distributions kolumn. Det förutsätter bara dessa få värden, vilket begränsar antalet Shards som kan innehålla data och antalet noder som kan bearbeta det. Mellan kolumner med hög kardinalitet är det också bäst att välja de kolumner som ofta används i Group by-satser eller som kopplings nycklar.
-   **Välj en kolumn med jämn fördelning.** Om du distribuerar en tabell i en kolumn som skevas till vissa vanliga värden, kommer data i tabellen att kunna ackumuleras i vissa Shards. Noderna som innehåller dessa Shards Slutför mer arbete än andra noder.
-   **Distribuera fakta-och dimensions tabeller på sina vanliga kolumner.**
    Fakta tabellen kan bara ha en distributions nyckel. Tabeller som är kopplade till en annan nyckel samplaceras inte med fakta tabellen. Välj en dimension som ska samplaceras baserat på hur ofta den är ansluten och storleken på de kopplade raderna.
-   **Ändra vissa dimensions tabeller till referens tabeller.** Om en dimensions tabell inte kan befinna sig i fakta tabellen kan du förbättra frågeresultaten genom att distribuera kopior av dimensions tabellen till alla noder i form av en referens tabell.

I [självstudierna i real tids instrument panelen](./tutorial-design-database-hyperscale-realtime.md) finns ett exempel på hur du skapar den här typen av program.

### <a name="time-series-data"></a>Time Series-data

I en tids serie arbets belastning frågar programmet efter den senaste informationen när de arkiverar gammal information.

Det vanligaste misstaget vid modellering av Time-Series-information i storskaliga (citus) är att använda själva tidsstämpeln som en distributions kolumn. En hash-distribution baserad på tid distribuerar tider som är ganska slumpmässiga i olika Shards, i stället för att hålla tidsintervallen sammantaget i Shards. Frågor som avser tid vanligt vis referens tider, till exempel de senaste data. Den här typen av hash-distribution leder till nätverks kostnader.

#### <a name="best-practices"></a>Bästa praxis

-   **Välj inte en tidstämpel som distributions kolumn.** Välj en annan distributions kolumn. I en app för flera klient organisationer använder du klient-ID: t eller i en app i real tid använder du entitets-ID: t.
-   **Använd PostgreSQL Table partitioning för tid i stället.** Använd tabell partitionering för att dela upp en stor tabell med tidsordnade data i flera ärvda tabeller med varje tabell som innehåller olika tidsintervall. Genom att distribuera en postgres tabell i citus () skapas Shards för de ärvda tabellerna.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [samplaceringen](concepts-hyperscale-colocation.md) mellan distribuerade data hjälper frågor att köras snabbt.
