---
title: Återställningstester
description: Lär dig mer om vägledning och bästa praxis för att använda Azure SQL Database för att utföra haveri beredskap.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91321670"
---
# <a name="performing-disaster-recovery-drills"></a>Utföra granskningar av haveri beredskap
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Vi rekommenderar att validering av program beredskap för arbets flöde för återställning utförs med jämna mellanrum. Att kontrol lera programmets beteende och konsekvenserna av data förlust och/eller störningar i redundansväxlingen är en bra tekniker. Det är också ett krav för de flesta bransch standarder som en del av certifieringen för affärs kontinuitet.

Att utföra en haveri beredskaps granskning består av:

* Simulera avbrott i data skiktet
* Återställa
* Verifiera program integritet efter återställning

Beroende på hur du har [utformat ditt program för affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)kan arbets flödet för att köra detalj nivån variera. I den här artikeln beskrivs de bästa metoderna för att utföra en haveri beredskaps granskning i samband med Azure SQL Database.

## <a name="geo-restore"></a>Geo-återställning

För att förhindra data förlust vid haveri beredskap, utför du detalj granskningen med en test miljö genom att skapa en kopia av produktions miljön och använda den för att verifiera programmets arbets flöde för redundans.

### <a name="outage-simulation"></a>Simulering av avbrott

För att simulera avbrottet kan du byta namn på käll databasen. Den här namn ändringen orsakar program anslutnings fel.

### <a name="recovery"></a>Återställning

* Utför geo-återställning av databasen till en annan server enligt beskrivningen [här](disaster-recovery-guidance.md).
* Ändra program konfigurationen för att ansluta till den återställda databasen och följ återställnings hand boken [Konfigurera en databas efter återställnings](disaster-recovery-guidance.md) guide.

### <a name="validation"></a>Validering

Slutför detalj nivån genom att kontrol lera program integritet efter återställning (inklusive anslutnings strängar, inloggningar, grundläggande funktioner testning eller andra validerings delar av standard program signerings procedurer).

## <a name="failover-groups"></a>Redundansgrupper

För en databas som skyddas med hjälp av failover-grupper, innebär gransknings övningen Planerad redundans till den sekundära servern. Den planerade redundansväxlingen säkerställer att den primära databasen och de sekundära databaserna i gruppen för växling vid fel är synkroniserad när rollerna växlas. Till skillnad från oplanerad redundansväxling leder den här åtgärden inte till data förlust, så att detalj nivån kan utföras i produktions miljön.

### <a name="outage-simulation"></a>Simulering av avbrott

För att simulera avbrottet kan du inaktivera webb programmet eller den virtuella datorn som är ansluten till databasen. Den här simuleringen av avbrott resulterar i anslutnings problem för webb klienter.

### <a name="recovery"></a>Återställning

* Kontrol lera att program konfigurationen i DR-regionen pekar på den tidigare sekundära, som blir den fullt tillgängliga nya primära servern.
* Initiera [planerad redundansväxling](scripts/setup-geodr-and-failover-database-powershell.md) av gruppen för redundans från den sekundära servern.
* Slutför återställningen genom att följa guiden [Konfigurera en databas efter återställnings](disaster-recovery-guidance.md) guide.

### <a name="validation"></a>Validering

Slutför detalj nivån genom att verifiera program integritet efter återställning (inklusive anslutning, grundläggande funktioner testning eller andra verifieringar som krävs för gransknings signeringar).

## <a name="next-steps"></a>Nästa steg

* Mer information om affärs kontinuitets scenarier finns i [kontinuitets scenarier](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md)
* Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](recovery-using-backups.md).
* Läs mer om snabbare återställnings alternativ i aktiva grupper för [geo-replikering](active-geo-replication-overview.md) och [Automatisk redundans](auto-failover-group-overview.md).
