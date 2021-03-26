---
title: Översikt över automatisk justering
description: Azure SQL Database och Azure SQL-hanterad instans analyserar SQL-frågor och anpassar automatiskt till användar arbets belastning.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/23/2021
ms.openlocfilehash: 6bd8d6001fcd3bfa487259aa219ff771f26a8a94
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951291"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Automatisk justering i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med automatisk justering i Azure SQL Database och Azure SQL Managed instance får du högsta prestanda och stabila arbets belastningar genom kontinuerlig prestanda justering baserat på AI och maskin inlärning.

Automatisk justering är en helt hanterad intelligent prestandatjänst som använder inbyggd intelligens för att kontinuerligt övervaka frågor som körs mot en databas. Tjänsten kan förbättra prestanda automatiskt. Detta uppnås genom dynamisk anpassning av databasen till förändringar av arbets belastningarna och att tillämpa justerings rekommendationer. Automatisk justering lär sig vågrätt från alla databaser i Azure via AI och förbättrar justerings åtgärderna dynamiskt. Den längre en databas körs med automatisk justering på, desto bättre utförs.

Den automatiska justeringen för Azure SQL Database och Azure SQL Managed instance kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabila och högsta arbets belastningar för databasen.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering göras för

- Automatisk prestanda justering av databaser
- Automatiserad verifiering av prestanda vinster
- Automatisk återställning och själv korrigering
- Justerings historik
- Finjustera Transact-SQL-skript (T-SQL) för åtgärder för manuella distributioner
- Övervakning av proaktiva arbets belastnings prestanda
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="safe-reliable-and-proven"></a>Säkert, tillförlitligt och beprövat

Justerings åtgärder som tillämpas på databaser i Azure SQL Database är helt säkra för prestanda för dina mest intensiva arbets belastningar. Systemet har utformats med bryr sig om att inte störa användar arbets belastningarna. Automatiska justerings rekommendationer tillämpas bara vid tidpunkten för en låg belastning. Systemet kan också tillfälligt inaktivera automatiska justerings åtgärder för att skydda arbets belastnings prestandan. I sådana fall visas meddelandet "inaktiverat av systemet" i Azure Portal. Automatisk justering avser arbets belastningar med den högsta resurs prioriteten.

Automatiska justerings mekanismer är mogna och har blivit utmärkta för flera miljoner databaser som körs på Azure. Automatiska justerings åtgärder som tillämpas verifieras automatiskt för att se till att det finns en positiv förbättring av arbets belastnings prestandan. Prestanda rekommendationerna för försämrat identifieras dynamiskt och snabbt. Genom den registrerade justerings historiken finns det en tydlig spårning av de justerings förbättringar som gjorts för varje databas i Azure SQL Database och Azure SQL-hanterad instans.

![Hur fungerar automatisk justering?](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL Database automatisk justering delar sin kärn logik med SQL Server funktionen för automatisk justering i databas motorn. Ytterligare teknisk information om den inbyggda intelligens mekanismen finns [SQL Server automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning).

En översikt över hur automatisk justering fungerar och för vanliga användnings scenarier finns i den inbäddade videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

- Du [aktiverar automatisk justering för Azure SQL Database i Azure Portal](automatic-tuning-enable.md) eller med hjälp av instruktionen [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) T-SQL.
- Du aktiverar automatisk justering för Azure SQL-hanterad instans med instruktionen [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current&preserve-view=true) T-SQL.

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

Alternativen för automatisk justering är tillgängliga i Azure SQL Database och Azure SQL-hanterad instans:

| Alternativ för automatisk justering | Stöd för enkel databas och poolad databas | Stöd för instans databas |
| :----------------------------- | ----- | ----- |
| **Skapa index** – identifierar index som kan förbättra prestandan för din arbets belastning, skapar index och automatiskt verifierar att prestandan för frågor har förbättrats. | Ja | Inga |
| **Drop index** som inte används (under de senaste 90 dagarna) och duplicerade index. Unika index, inklusive index som stöder primär nyckel och unika begränsningar, tas aldrig bort. Det här alternativet kan inaktive ras automatiskt när frågor med index tips finns i arbets belastningen eller när arbets belastningen utför partition växling. På Premium-och Affärskritisk-tjänst nivåerna tar det här alternativet aldrig bort oanvända index, men eventuella dubbletter av index visas. | Ja | Inga |
| **FRAMTVINGA senaste fungerande plan** (automatisk plan korrigering) – identifierar Azure SQL-frågor med hjälp av en körnings plan som är långsammare än den tidigare fungerande planen, och frågor som använder det senaste fungerande schemat i stället för försämrat-planen. | Ja | Ja |

### <a name="automatic-tuning-for-sql-database"></a>Automatisk justering för SQL Database

Automatisk justering för Azure SQL Database använder rekommendationerna **skapa index**, **Drop index** och **tvinga senaste schema** rekommendationer för databas rekommendationer för att optimera databasens prestanda. Mer information finns i [rekommendationer för databas rådgivare i Azure Portal](database-advisor-find-recommendations-portal.md), i [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)och i [REST API](/rest/api/sql/serverautomatictuning).

Du kan antingen manuellt tillämpa justerings rekommendationer med hjälp av Azure Portal eller så kan du låta automatisk justering tillämpa justerings rekommendationer åt dig. Fördelarna med att låta systemet tillämpa justerings rekommendationer för dig är att den automatiskt validerar att det finns en positiv vinst för arbets belastnings prestandan, och om det inte finns någon betydande prestanda förbättring, återställs justerings rekommendationen automatiskt. Observera att om frågor påverkas av justerings rekommendationer som inte körs ofta kan validerings fasen ta upp till 72 timmar efter design.

Om du använder justerings rekommendationer via T-SQL, är den automatiska prestanda valideringen och återförings metoderna inte tillgängliga. Rekommendationer som tillämpas på detta sätt förblir aktiva och visas i listan över justerings rekommendationer för 24-48 timmar. innan systemet återkallar dem automatiskt. Om du vill ta bort en rekommendation tidigare kan du ta bort den från Azure Portal.

Automatiska justerings alternativ kan aktive ras oberoende eller inaktive ras per databas, eller så kan de konfigureras på server nivå och tillämpas på alla databaser som ärver inställningar från servern. Servrar kan ärva Azure-standardvärden för automatiska justerings inställningar. Azure-standardvärden för tillfället är inställt på FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX har inaktiverats.

> [!IMPORTANT]
> Från och med mars kommer 2020 ändringar i Azure-standardvärden för automatisk justering att gälla enligt följande:
>
> - Nya Azure-standardvärden är FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled och DROP_INDEX = inaktive rad.
> - Befintliga servrar utan inställningar för automatisk justering konfigureras automatiskt för att ärva nya Azure-standardvärden. Detta gäller för alla kunder som för närvarande har Server inställningar för automatisk justering i ett odefinierat tillstånd.
> - Nya servrar som skapas konfigureras automatiskt för att ärva nya Azure-standardvärden (till skillnad från tidigare när konfigurationen för automatisk justering var i ett odefinierat tillstånd när en ny server skapas).

Att konfigurera alternativ för automatisk justering på en server och ärva inställningar för databaser som tillhör den överordnade servern är en rekommenderad metod för att konfigurera automatisk justering eftersom det fören klar hanteringen av automatiska justerings alternativ för ett stort antal databaser.

Information om hur du skapar e-postaviseringar för automatiska justerings rekommendationer finns i [e-postaviseringar för automatisk justering](automatic-tuning-email-notifications-configure.md).

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Automatisk justering för Azure SQL-hanterad instans

Automatisk justering för SQL-hanterad instans stöder endast **tvångs sista fin plan**. Mer information om hur du konfigurerar alternativ för automatisk justering via T-SQL finns i [Automatisk justering inför automatisk plan korrigering](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [Automatisk plan korrigering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction).

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om inbyggd intelligens som används i automatisk justering, se [artificiell intelligens-justering Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Information om hur automatisk justering fungerar under huven finns i avsnittet [om att indexera miljon tals databaser automatiskt i Microsoft Azure SQL Database](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).