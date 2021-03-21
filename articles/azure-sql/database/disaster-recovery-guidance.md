---
title: Haveriberedskap
description: Lär dig hur du återställer en databas från ett regionalt Data Center avbrott eller fel med Azure SQL Database aktiv geo-replikering och geo-återställnings funktioner.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 322ef3b8ca30396bd4772850ff651f7545a21257
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580758"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Återställa Azure SQL Database eller redundans till en sekundär
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database erbjuder följande funktioner för att återskapa från ett avbrott:

- [Aktiv geo-replikering](active-geo-replication-overview.md)
- [Automatiska redundansgrupper](auto-failover-group-overview.md)
- [Geo-återställning](recovery-using-backups.md#point-in-time-restore)
- [Zoner-redundanta databaser](high-availability-sla.md)

Läs mer om affärs kontinuitets scenarier och de funktioner som stöder dessa scenarier i [verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Om du använder zoner med redundanta Premium-eller Affärskritisk databaser eller pooler, är återställnings processen automatiserad och resten av det här materialet gäller inte.
>
> Både primära och sekundära databaser måste ha samma tjänst nivå. Vi rekommenderar också starkt att den sekundära databasen skapas med samma beräknings storlek (DTU: er eller virtuella kärnor) som primär. Mer information finns i [uppgradera eller nedgradera som primär databas](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Använd en eller flera grupper för växling vid fel för att hantera redundans för flera databaser.
> Om du lägger till en befintlig Geo-replikeringsrelation i gruppen redundans kontrollerar du att geo-Secondary är konfigurerat med samma tjänste nivå och beräknings storlek som den primära. Mer information finns i [använda grupper för automatisk redundans för att aktivera transparent och samordnad redundansväxling av flera databaser](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Förbered dig för ett avbrott

För att det ska gå att återställa till ett annat data område med hjälp av antingen redundans grupper eller geo-redundanta säkerhets kopieringar måste du förbereda en server i ett annat data Center avbrott för att bli den nya primära servern, om behovet uppstår och ha väldefinierade steg dokumenterade och testade för att säkerställa en smidig återställning. Dessa förberedelse steg omfattar:

- Identifiera servern i en annan region så att den blir den nya primära servern. För geo-återställning är detta vanligt vis en server i den [kopplade regionen](../../best-practices-availability-paired-regions.md) för den region där databasen finns. Detta eliminerar den ytterligare trafik kostnaden under geo-återställnings åtgärderna.
- Identifiera och alternativt definiera de regler för IP-brandvägg på server nivå som krävs för att användarna ska kunna komma åt den nya primära databasen.
- Bestäm hur du ska omdirigera användare till den nya primära servern, t. ex. genom att ändra anslutnings strängar eller genom att ändra DNS-poster.
- Identifiera och eventuellt skapa, inloggningar som måste finnas i huvud databasen på den nya primära servern och se till att dessa inloggningar har rätt behörigheter i huvud databasen, om det finns några. Mer information finns i [SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md)
- Identifiera aviserings regler som måste uppdateras för att mappas till den nya primära databasen.
- Dokumentera gransknings konfigurationen för den aktuella primära databasen
- Utföra en [granskning av haveri beredskap](disaster-recovery-drills.md). För att simulera ett avbrott för geo-återställning kan du ta bort eller byta namn på käll databasen för att orsaka program anslutnings fel. Om du vill simulera ett avbrott med hjälp av failover-grupper kan du inaktivera webb programmet eller den virtuella datorn som är ansluten till databasen eller redundansväxla databasen för att orsaka program anslutnings fel.

## <a name="when-to-initiate-recovery"></a>När återställningen ska påbörjas

Återställnings åtgärden påverkar programmet. Det krävs en ändring av SQL-anslutningssträngen eller omdirigeringen med DNS och kan resultera i permanent data förlust. Därför bör det bara göras när avbrott är troligt vis längre än ditt programs återställnings tids mål. När programmet distribueras till produktion bör du utföra en regelbunden övervakning av programmets hälsa och använda följande data punkter för att kontrol lera att återställningen är berättigad:

1. Permanent anslutnings problem från program nivån till databasen.
2. Azure Portal visar en avisering om en incident i regionen med bred påverkan.

> [!NOTE]
> Om du använder failover-grupper och väljer automatisk redundansväxling, är återställnings processen automatiserad och transparent för programmet.

Beroende på din program tolerans för stillestånds tid och eventuellt företags ansvar kan du tänka på följande återställnings alternativ.

Använd Hämta återställnings [bara databaser](/previous-versions/azure/reference/dn800985(v=azure.100)) (*LastAvailableBackupDate*) för att hämta den senaste geo-replikerade återställnings punkten.

## <a name="wait-for-service-recovery"></a>Vänta på tjänst återställning

Azure-teamen arbetar samtidigt för att återställa tjänstens tillgänglighet så fort som möjligt, men beroende på rotor saken kan det ta flera timmar eller dagar.  Om ditt program kan tolerera betydande stillestånds tid kan du bara vänta tills återställningen har slutförts. I det här fallet krävs ingen åtgärd på din del. Du kan se aktuell status för tjänsten på vår [Azure Service Health-instrumentpanel](https://azure.microsoft.com/status/). Efter återställningen av regionen återställs programmets tillgänglighet.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Redundansväxla till geo-replikerad sekundär server i gruppen redundans

Om ditt programs stillestånd kan leda till företags ansvar, bör du använda grupper för växling vid fel. Det gör det möjligt för programmet att snabbt återställa tillgänglighet i en annan region vid ett strömavbrott. En själv studie kurs finns i [implementera en geo-distribuerad databas](geo-distributed-application-configure-tutorial.md).

Om du vill återställa tillgänglighet för databaserna måste du initiera redundansväxlingen till den sekundära servern med hjälp av någon av de metoder som stöds.

Använd någon av följande guider för att redundansväxla till en geo-replikerad sekundär databas:

- [Redundansväxla till en geo-replikerad sekundär server med hjälp av Azure Portal](active-geo-replication-configure-portal.md)
- [Redundansväxla till den sekundära servern med hjälp av PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Redundansväxla till en sekundär server med hjälp av Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Återställa med geo-återställning

Om programmets nedtid inte leder till företags ansvar kan du använda [geo-återställning](recovery-using-backups.md) som en metod för att återställa dina program databaser. Den skapar en kopia av databasen från den senaste geo-redundanta säkerhets kopian.

## <a name="configure-your-database-after-recovery"></a>Konfigurera din databas efter återställning

Om du använder geo-återställning för att återställa från ett avbrott måste du kontrol lera att anslutningarna till de nya databaserna är korrekt konfigurerade så att den normala program funktionen kan återupptas. Det här är en check lista med uppgifter för att få din återställda databas produktion klar.

### <a name="update-connection-strings"></a>Uppdatera anslutnings strängar

Eftersom den återställda databasen finns på en annan server måste du uppdatera programmets anslutnings sträng så att den pekar på den servern.

Mer information om hur du ändrar anslutnings strängar finns i lämpligt utvecklings språk för [anslutnings biblioteket](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Du måste kontrol lera att brand Väggs reglerna som kon figurer ATS på servern och databasen matchar de som har kon figurer ATS på den primära servern och den primära databasen. Mer information finns i [så här gör du för att: Konfigurera brand Väggs inställningar (Azure SQL Database)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Konfigurera inloggningar och databas användare

Du måste se till att alla inloggningar som används av ditt program finns på den server som är värd för den återställda databasen. Mer information finns i [säkerhets konfiguration för geo-replikering](active-geo-replication-security-configure.md).

> [!NOTE]
> Du bör konfigurera och testa Server brand Väggs reglerna och inloggningar (och deras behörigheter) under en haveri återställnings granskning. Dessa objekt på server nivå och deras konfiguration kanske inte är tillgängliga under avbrottet.

### <a name="setup-telemetry-alerts"></a>Konfigurera telemetri aviseringar

Du måste se till att dina befintliga inställningar för varnings regler uppdateras för att mappas till den återställda databasen och den andra servern.

Mer information om databas varnings regler finns i [ta emot varnings meddelanden](../../azure-monitor/alerts/alerts-overview.md) och [spåra service Health](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Aktivera granskning

Om granskning krävs för att få åtkomst till din databas måste du aktivera granskning efter databas återställningen. Mer information finns i [databas granskning](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md)
- Information om design och återställnings scenarier för affärs kontinuitet finns i [kontinuitets scenarier](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](recovery-using-backups.md)