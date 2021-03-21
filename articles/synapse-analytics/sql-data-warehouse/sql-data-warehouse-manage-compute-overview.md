---
title: Hantera beräknings resurser för dedikerad SQL-pool (tidigare SQL DW)
description: 'Lär dig mer om prestanda skalnings funktioner för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics. Skala ut genom att justera DWU: er eller minska kostnaderna genom att pausa den dedikerade SQL-poolen (tidigare SQL DW).'
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 20087fbf4d5c37f1501df08cc294a10ddb9118e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601822"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Hantera beräkning för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

Lär dig mer om att hantera beräknings resurser dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics. Sänk kostnaderna genom att pausa den dedikerade SQL-poolen eller skala den dedikerade SQL-poolen så att den uppfyller prestanda kraven.

## <a name="what-is-compute-management"></a>Vad är beräknings hantering?

Arkitekturen för dedikerad SQL-pool (tidigare SQL DW) särskiljer lagring och beräkning, vilket gör att de kan skalas oberoende av varandra. Det gör att du kan skala om beräkningsresurserna för att uppfylla prestandabehoven oberoende av datalagringen. Du kan också pausa och återuppta beräkningsresurser. En naturlig följd av denna arkitektur är att [faktureringen](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) för beräkning och lagring är separat. Om du inte behöver använda din dedikerade SQL-pool (tidigare SQL DW) en stund kan du spara beräknings kostnader genom att pausa beräkningarna.

## <a name="scaling-compute"></a>Skala beräkning

Du kan skala ut eller skala upp beräkningen genom att justera inställningen för [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) för din dedikerade SQL-pool (tidigare SQL DW). Prestanda för inläsning och körning av frågor ökar linjärt när du lägger till fler informationslagerenheter.

Instruktioner för att skala ut finns i snabb starterna för [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)eller [T-SQL](quickstart-scale-compute-tsql.md) . Du kan också utföra skalnings åtgärder med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

För att utföra en skalnings åtgärd, dedicerad SQL-pool (tidigare SQL DW) först omsorg alla inkommande frågor och återställer transaktioner för att säkerställa ett konsekvent tillstånd. Skalningen utförs först när transaktionerna har återställts. Vid en skalnings åtgärd kopplar systemet bort lagrings lagret från Compute-noderna, lägger till datornoderna och kopplar sedan om lagrings skiktet till beräknings skiktet. Varje dedikerad SQL-pool (tidigare SQL DW) lagras som 60-distributioner, som är jämnt distribuerade till Compute-noderna. Om du lägger till fler Compute-noder ökar beräknings kraften. Vartefter antalet beräknade noder ökar minskar antalet distributioner per Compute-nod och ger mer data bearbetnings kraft för dina frågor. På samma sätt minskar antalet beräknade data lager enheter antalet datornoder, vilket minskar beräknings resurserna för frågor.

Följande tabell visar hur antalet distributioner per beräknings nod ändras när data lagrets enheter ändras.  DW30000c tillhandahåller 60 Compute-noder och ger mycket högre frågeresultat än DW100c.

| Informationslagerenheter  | \# av Compute-noder | \# av distributioner per nod |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Hitta rätt storlek på informations lager enheter

Om du vill se prestanda fördelarna med att skala ut, särskilt för större informations lager enheter, vill du använda minst en data uppsättning på 1 TB. För att hitta det bästa antalet informations lager enheter för din dedikerade SQL-pool (tidigare SQL DW) kan du prova att skala upp och ned. Kör några frågor med olika antal informations lager enheter när du har läst in dina data. Eftersom skalning är snabbt kan du prova olika prestanda nivåer på en timme eller mindre.

Rekommendationer för att hitta det bästa antalet informations lager enheter:

- Börja med att välja ett mindre antal informations lager enheter för en dedikerad SQL-pool (tidigare SQL DW) i utvecklingen.  En lämplig start punkt är DW400c eller DW200c.
- Övervaka programmets prestanda och se hur många data lager enheter som valts jämfört med den prestanda du har.
- Anta en linjär skala och fastställ hur mycket du behöver för att öka eller minska data lager enheterna.
- Fortsätt att göra justeringar tills du når en optimal prestanda nivå för dina affärs behov.

## <a name="when-to-scale-out"></a>När du ska skala ut

Att skala ut data lager enheter påverkar dessa aspekter av prestanda:

- Förbättrar systemets prestanda linjärt för genomsökningar, agg regeringar och CTAS-uttryck.
- Ökar antalet läsare och skribenter för att läsa in data.
- Maximalt antal samtidiga frågor och samtidiga platser.

Rekommendationer för när du ska skala ut data lager enheter:

- Innan du utför en tung data inläsnings-eller omvandlings åtgärd kan du skala ut för att göra data tillgängliga snabbare.
- Vid hög arbets tid kan du skala ut så att det tar upp fler mängder samtidiga frågor.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Vad händer om skala ut inte förbättrar prestandan?

Genom att lägga till informations lager enheter ökar du parallellt. Om arbetet jämnt delas mellan Compute-noderna, förbättrar den ytterligare parallellen frågans prestanda. Om skala ut inte ändrar prestandan finns det några orsaker till varför detta kan inträffa. Dina data kan skevas över distributionerna eller också kan frågor introducera en stor mängd data förflyttningar. Information om hur du undersöker frågor om prestanda finns i [fel sökning av prestanda](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Pausa och återuppta beräkningar

Om du pausar beräkning blir lagrings lagret frånkopplat från Compute-noderna. Beräknings resurserna släpps från ditt konto. Du debiteras inte för Compute medan Compute har pausats. Om du återupptar beräknings-och återaktiverar det lagrings utrymmet till datornoderna och återupptar avgifter för beräkning.
När du pausar en dedikerad SQL-pool (tidigare SQL DW):

- Beräknings-och minnes resurser returneras till poolen med tillgängliga resurser i data centret
- Kostnaderna för data lagrets enheter är noll under pausens varaktighet.
- Data lagringen påverkas inte och dina data förblir intakta.
- Alla pågående eller köade åtgärder avbryts.

När du återupptar en dedikerad SQL-pool (tidigare SQL DW):

- Den dedikerade SQL-poolen (tidigare SQL DW) hämtar beräknings-och minnes resurser för inställningen för data lager enheter.
- Beräknings avgifter för dina data lager enheter återupptas.
- Dina data blir tillgängliga.
- När den dedikerade SQL-poolen (tidigare SQL DW) är online måste du starta om dina arbets belastnings frågor.

Om du alltid vill att din dedikerade SQL-pool (tidigare SQL DW) ska vara tillgänglig kan du skala ned den till den minsta storleken i stället för att pausa.

Anvisningar för att pausa och återuppta finns i [Azure Portal](pause-and-resume-compute-portal.md)eller [PowerShell](pause-and-resume-compute-powershell.md) snabb starter. Du kan också använda [paus REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) eller [återuppta REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning

Vi rekommenderar att du låter befintliga transaktioner slutföras innan du startar en paus-eller skalnings åtgärd.

När du pausar eller skalar din dedikerade SQL-pool (tidigare SQL DW) avbryts dina frågor i bakgrunden när du startar paus-eller skalnings förfrågan. Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa. **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.** Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade. Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen. Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md)och [optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisera beräknings hantering

Information om hur du automatiserar beräknings hanterings åtgärderna finns i [Hantera beräkning med Azure Functions](manage-compute-with-azure-functions.md).

Var och en av åtgärderna för att skala ut, pausa och återuppta kan ta flera minuter att slutföra. Om du skalar, pausar eller återupptar automatiskt rekommenderar vi att du implementerar logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd. Genom att kontrol lera den dedikerade SQL-poolen (tidigare SQL DW) via olika slut punkter kan du implementera automatisering av sådana åtgärder på ett korrekt sätt.

Information om hur du kontrollerar den dedikerade SQL-poolen (tidigare SQL DW) finns i snabb starten för [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) eller [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) . Du kan också kontrol lera den dedikerade SQL-poolen (tidigare SQL DW) med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Behörigheter

Skalning av den dedikerade SQL-poolen (tidigare SQL DW) kräver de behörigheter som beskrivs i [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Pausa och återuppta kräver behörigheten [SQL DB-deltagare](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) , särskilt Microsoft. SQL/Servers/databaser/åtgärd.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar beräknings resurser för att [Hantera](manage-compute-with-azure-functions.md) beräknings resurser är att allokera olika beräknings resurser för enskilda frågor. Mer information finns i [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md).
