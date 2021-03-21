---
title: Prestanda rekommendationer för Database Advisor för Azure SQL Database
description: Azure SQL Database ger rekommendationer för databaser som kan förbättra frågans prestanda i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500860"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Database Advisor prestanda rekommendationer för Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database lär sig och anpassar sig till ditt program. Azure SQL Database har ett antal databas rådgivare som ger anpassade rekommendationer som gör det möjligt att maximera prestanda. Dessa databas rådgivare bedömer kontinuerligt och analyserar användnings historiken och ger rekommendationer baserat på arbets belastnings mönster som bidrar till att förbättra prestandan.

## <a name="performance-overview"></a>Prestanda översikt

Prestanda översikt ger en översikt över databasens prestanda och hjälper dig med prestanda justering och fel sökning.

![Prestanda översikt för Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- Panelen **rekommendationer** ger en analys av justerings rekommendationerna för din databas (de tre främsta rekommendationerna visas om det finns mer). Genom att klicka på den här panelen kan du välja **[alternativ för prestanda rekommendation](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- Panelen **Justera aktivitet** innehåller en översikt över pågående och slutförda justerings åtgärder för din databas, vilket ger dig en snabb överblick över historiken för justerings aktivitet. Om du klickar på den här panelen går du till vyn fullständig fin justering för din databas.
- Panelen **Automatisk justering** visar **[konfigurationen för automatisk justering](automatic-tuning-enable.md)** för din databas (justerings alternativ som tillämpas automatiskt på databasen). Klicka på den här panelen för att öppna dialog rutan automatiserings konfiguration.
- Panelen **databas frågor** visar en sammanfattning av frågans prestanda för din databas (total DTU-användning och främsta resurs krävande frågor). Genom att klicka på den här panelen går du **[query Performance Insight](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Alternativ för prestanda rekommendation

Tillgängliga alternativ för prestanda rekommendation i Azure SQL Database:

| Prestanda rekommendation | Stöd för enkel databas och poolad databas | Stöd för instans databas |
| :----------------------------- | ----- | ----- |
| **Skapa index rekommendationer** – rekommenderar att du skapar index som kan förbättra arbets Belastningens prestanda. | Ja | Inga |
| **Ta bort index rekommendationer** – rekommenderar borttagning av redundanta och dubbla index dagligen, förutom unika index och index som inte har använts under en längre tid (>90 dagar). Observera att det här alternativet inte är kompatibelt med program som använder partitions växlings-och index tips. Det går inte att släppa oanvända index för Premium-och Affärskritisk tjänst nivåer. | Ja | Inga |
| **Parameterisera frågor rekommendationer (för hands version)** – rekommenderar tvingande Parameterisering i fall när du har en eller flera frågor som ständigt kompileras om, men som slutar med samma frågans körnings plan. | Ja | Inga |
| **Åtgärda rekommendationer för schema problem (för hands version)** – rekommendationer för schema korrigering visas när Azure SQL Database visar en avvikelse i antalet SCHEMAbaserade SQL-fel som inträffar i databasen. Microsoft är för närvarande inaktuellt "Fix schema Issue"-rekommendationer. | Ja | Inga |

![Prestanda rekommendationer för Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Information om hur du tillämpar prestanda rekommendationer finns i [använda rekommendationer](database-advisor-find-recommendations-portal.md#applying-recommendations). Information om hur du visar status för rekommendationer finns i [övervaknings åtgärder](database-advisor-find-recommendations-portal.md#monitoring-operations).

Du kan också hitta fullständig historik över justerings åtgärder som har tillämpats tidigare.

## <a name="create-index-recommendations"></a>Skapa index rekommendationer

Azure SQL Database övervakar kontinuerligt de frågor som körs och identifierar de index som kan förbättra prestandan. När det är tillräckligt säkert att ett visst index saknas, skapas en ny rekommendation för **create index** .

Azure SQL Database bygger förtroende genom att uppskatta prestanda ökningen som indexet skulle ta genom tiden. Beroende på den beräknade prestandavinsten kategoriseras rekommendationerna med nivån Hög, Medel eller Låg.

Index som skapas med hjälp av rekommendationer flaggas alltid som automatiskt skapade index. Du kan se vilka index som skapas automatiskt genom att titta på [vyn sys. index](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automatiskt skapade index blockerar inte ALTER/RENAME-kommandon.

Om du försöker släppa kolumnen som har ett automatiskt skapat index över den, skickas kommandot. Det automatiskt skapade indexet släpps även med kommandot. Vanliga index blockerar kommandot ALTER/RENAME på kolumner som är indexerade.

När rekommendationen skapa index har tillämpats jämför Azure SQL Database prestanda för frågorna med bas linje prestanda. Om det nya indexet ger bättre prestanda flaggas rekommendationen som lyckad och effekt rapporten är tillgänglig. Om indexet inte förbättrar prestandan återställs det automatiskt. Azure SQL Database använder den här processen för att säkerställa att rekommendationerna förbättrar databasens prestanda.

En rekommendation om att **skapa index** har en princip för att inte tillåta att tillämpa rekommendationen om resursanvändningen för en databas eller pool är hög. Säkerhets kopierings principen tar hänsyn till CPU, data-IO, logg-i/o och tillgängligt lagrings utrymme.

Om CPU, data-IO eller logg-IO är högre än 80% under de senaste 30 minuterna, senareläggs rekommendationen skapa index. Om det tillgängliga lagrings utrymmet är under 10% efter att indexet har skapats hamnar rekommendationen i ett fel tillstånd. Om du efter ett par dagar bedömer automatisk justering att indexet skulle bli fördelaktigt, startar processen igen.

Den här processen upprepas tills det finns tillräckligt med tillgängligt lagrings utrymme för att skapa ett index, eller tills indexet inte visas som en fördelaktighet längre.

## <a name="drop-index-recommendations"></a>Ta bort index rekommendationer

Förutom att identifiera saknade index, Azure SQL Database kontinuerligt analysera prestanda för befintliga index. Om ett index inte används rekommenderar Azure SQL Database att släppa det. Att släppa ett index rekommenderas i två fall:

- Indexet är en dubblett av ett annat index (samma indexerad och inkluderad kolumn, partition schema och filter).
- Indexet har inte använts under en längre period (93 dagar).

Ta bort index rekommendationer går också igenom verifieringen efter implementeringen. Om prestandan förbättras är effekt rapporten tillgänglig. Om prestanda försämras återställs rekommendationen.

## <a name="parameterize-queries-recommendations-preview"></a>Rekommendationer för Parameterisera-frågor (för hands version)

Rekommendationer för *Parameterisera-frågor* visas när du har en eller flera frågor som ständigt kompileras om, men som slutar med samma körnings plan för frågan. Det här villkoret skapar en möjlighet att tillämpa Tvingad Parameterisering. Tvingad Parameterisering, i sin tur, tillåter att fråge planer cachelagras och återanvänds i framtiden, vilket förbättrar prestanda och minskar resursanvändningen.

Varje fråga måste ursprungligen kompileras för att generera en körnings plan. Varje genererad plan läggs till i planens cacheminne. Efterföljande körningar av samma fråga kan återanvända den här planen från cachen, vilket eliminerar behovet av ytterligare kompilering.

Frågor med icke-parametriserade värden kan leda till pålägg på prestandan eftersom körningsplanen kompileras om varje gång de icke-parametriserade värden är annorlunda. I många fall genererar samma frågor med olika parametervärden samma körningsplaner. Dessa planer läggs däremot fortfarande till separat i plancachen.

Processen för att kompilera om körningsplaner använder databasresurser, ökar frågevaraktigheten och översvämmar plancachen. Dessa händelser leder i sin tur till att planer avlägsnas från cachen. Du kan ändra det här beteendet genom att ange alternativet Tvingad Parameterisering i databasen.

För att hjälpa dig att beräkna effekten av den här rekommendationen får du en jämförelse mellan den faktiska processor användningen och den beräknade processor användningen (som om rekommendationen tillämpades). Den här rekommendationen kan hjälpa dig att få CPU-besparingar. Det kan också hjälpa dig att minska frågans varaktighet och kostnader för planens cacheminne, vilket innebär att fler av planerna kan stanna kvar i cacheminnet och återanvändas. Du kan snabbt använda den här rekommendationen genom att välja kommandot **Apply** .

När du har tillämpat den här rekommendationen aktiverar den Tvingad Parameterisering inom några minuter på din databas. Den startar övervaknings processen, som varar i cirka 24 timmar. Efter den här perioden kan du se verifierings rapporten. Den här rapporten visar CPU-användningen av databasen 24 timmar före och efter att rekommendationen har tillämpats. Azure SQL Database Advisor har en säkerhetsmekanism som automatiskt återställer den tillämpade rekommendationen om prestanda regressionen har upptäckts.

## <a name="fix-schema-issues-recommendations-preview"></a>Åtgärda rekommendationer för schema problem (förhands granskning)

> [!IMPORTANT]
> Microsoft är för närvarande inaktuellt "Fix schema Issue"-rekommendationer. Vi rekommenderar att du använder [intelligent Insights](intelligent-insights-overview.md) för att övervaka databas prestanda problem, inklusive schema problem som rekommendationerna "åtgärda schema problem" tidigare täckt.

**Åtgärda problem med schema problem** visas när Azure SQL Database upptäcker en avvikelse i antalet schema-relaterade SQL-fel som uppstår i databasen. Den här rekommendationen visas vanligt vis när databasen påträffar flera schema-relaterade fel (Ogiltigt kolumn namn, ogiltigt objekt namn och så vidare) inom en timme.

"Schema problem" är en klass av syntaxfel. De inträffar när definitionen av SQL-frågan och definitionen av databasschemat inte är justerad. Till exempel kan en av de kolumner som förväntas av frågan saknas i mål tabellen eller vice versa.

Rekommendationen "åtgärda schema problem" visas när Azure SQL Database visar en avvikelse i antalet schema-relaterade SQL-fel som inträffar i databasen. I följande tabell visas de fel som rör schema problem:

| SQL-felkod | Meddelande |
| --- | --- |
| 201 |Proceduren eller funktionen *förväntar sig parametern*, som inte tillhandahölls. |
| 207 |Ogiltigt kolumn namn ' * '. |
| 208 |Ogiltigt objekt namn ' * '. |
| 213 |Kolumn namnet eller antalet angivna värden stämmer inte överens med tabell definitionen. |
| 2812 |Det gick inte att hitta den lagrade proceduren *. |
| 8144 |Procedur eller funktion * har för många angivna argument. |

## <a name="custom-applications"></a>Anpassade program

Utvecklare kan överväga att utveckla anpassade program med hjälp av prestanda rekommendationer för Azure SQL Database. Alla rekommendationer som visas i portalen för en databas kan nås via [Get-AzSqlDatabaseRecommendedAction-](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API: et.

## <a name="next-steps"></a>Nästa steg

- Mer information om automatisk justering av databas index och fråge körnings planer finns i [Azure SQL Database automatisk justering](automatic-tuning-overview.md).
- Mer information om automatisk övervakning av databas prestanda med automatiserad diagnostik och rotor Saks analys av prestanda problem finns i [Azure SQL intelligent Insights](intelligent-insights-overview.md).
- Läs mer i [frågor](query-performance-insight-use.md) och svar om du vill veta mer om och se prestanda påverkan för dina vanligaste frågor.