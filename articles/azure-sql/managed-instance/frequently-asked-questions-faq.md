---
title: Vanliga frågor och svar (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Vanliga frågor och svar (FAQ) om Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 17e97503996b53868f12e74ed5a38dba63285426
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503206"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) om Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller de vanligaste frågorna om [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funktioner som stöds

**Var kan jag hitta en lista över funktioner som stöds på SQL-hanterad instans?**

En lista över funktioner som stöds i SQL-hanterad instans finns i [funktioner för Azure SQL-hanterad instans](../database/features-comparison.md).

Skillnader i syntax och beteende mellan Azure SQL-hanterad instans och SQL Server finns i [skillnader i T-SQL från SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Teknisk specifikation, resurs gränser och andra begränsningar
 
**Var kan jag hitta tekniska egenskaper och resurs gränser för SQL-hanterad instans?**

Information om tillgängliga egenskaper för maskin varu skapande finns i [tekniska skillnader i maskin varu generationer](resource-limits.md#hardware-generation-characteristics).
För tillgängliga tjänst nivåer och deras egenskaper, se [tekniska skillnader mellan tjänst nivåer](resource-limits.md#service-tier-characteristics).

**Vilken service nivå är jag berättigad till?**

Alla kunder är berättigade till alla tjänst nivåer. Men om du vill byta ut befintliga licenser för rabatterade priser på Azure SQL-hanterad instans med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/), bör du tänka på att SQL Server Enterprise Edition-kunder med Software Assurance är berättigade till [generell användning](../database/service-tier-general-purpose.md) eller [affärskritisk](../database/service-tier-business-critical.md) prestanda nivåer och SQL Server Standard Edition-kunder med Software Assurance är berättigade till generell användning prestanda nivå. Mer information finns i [vissa rättigheter för AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Vilka prenumerations typer stöds för SQL-hanterad instans?**

En lista över prenumerations typer som stöds finns i [prenumerations typer som stöds](resource-limits.md#supported-subscription-types). 

**Vilka Azure-regioner stöds?**

Hanterade instanser kan skapas i de flesta Azure-regioner. Se [regioner som stöds för SQL-hanterad instans](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du behöver hanterad instans i en region som för närvarande inte stöds [skickar du en supportbegäran via Azure Portal](../database/quota-increase-request.md).

**Finns det några kvot begränsningar för distributioner av SQL-hanterade instanser?**

Den hanterade instansen har två standard gränser: begränsa antalet undernät som du kan använda och en gräns för antalet virtuella kärnor som du kan etablera. Gränserna varierar mellan olika prenumerations typer och regioner. En lista över begränsningar för regionala resurser efter prenumerations typ finns i tabell från [regional resurs begränsning](resource-limits.md#regional-resource-limitations). Detta är mjuka gränser som kan ökas på begäran. Om du behöver etablera fler hanterade instanser i dina aktuella regioner skickar du en supportbegäran för att öka kvoten med hjälp av Azure Portal. Mer information finns i [begäran om kvot ökning för Azure SQL Database](../database/quota-increase-request.md).

**Kan jag öka antalet databas gränser (100) på min hanterade instans på begäran?**

Nej, och för närvarande finns det inga bekräftade planer för att öka antalet databaser på SQL-hanterad instans. 

**Var kan jag migrera om jag har mer än 8TB data?**
Du kan överväga att migrera till andra Azure-varianter som passar din arbets belastning: [Azure SQL Database storskalig](../database/service-tier-hyperscale.md) eller [SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Var kan jag migrera om jag har särskilda maskin varu krav som större RAM till vCore-förhållande eller flera processorer?**
Du kan överväga att migrera till [SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) eller [Azure SQL Database](../database/sql-database-paas-overview.md) minne/CPU optimerad.

## <a name="known-issues-and-defects"></a>Kända problem och fel

**Var hittar jag kända problem och fel?**

För produkt fel och kända problem, se [kända problem](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nya funktioner

**Var hittar jag de senaste funktionerna och funktionerna i den offentliga för hands versionen?**

För nya och förhands gransknings funktioner, se [viktig information](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Skapa, uppdatera, ta bort eller flytta SQL-hanterad instans

**Hur kan jag etablera SQL-hanterad instans?**

Du kan etablera en instans från [Azure Portal](instance-create-quickstart.md), [POWERSHELL](scripts/create-configure-managed-instance-powershell.md), [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) och [arm-mallar](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Kan jag etablera hanterade instanser i en befintlig prenumeration?**

Ja, du kan etablera en hanterad instans i en befintlig prenumeration om prenumerationen tillhör de [prenumerations typer som stöds](resource-limits.md#supported-subscription-types).

**Varför kunde jag inte etablera en hanterad instans i under nätet vars namn börjar med en siffra?**

Detta är en aktuell begränsning för den underliggande komponenten som verifierar under nätets namn mot regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Alla namn som skickar regex och är giltiga under näts namn stöds för närvarande.

**Hur kan jag skala min hanterade instans?**

Du kan skala din hanterade instans från [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [POWERSHELL](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure CLI](/cli/azure/sql/mi#az-sql-mi-update) eller [arm-mallar](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Kan jag flytta min hanterade instans från en region till en annan?**

Ja, det kan du. Instruktioner finns i [Flytta resurser mellan regioner](../database/move-resources-across-regions.md).

**Hur kan jag ta bort min hanterade instans?**

Du kan ta bort hanterade instanser via Azure Portal, [PowerShell](/powershell/module/az.sql/remove-azsqlinstance), [Azure CLI](/cli/azure/sql/mi#az-sql-mi-delete) eller [Resource Manager REST API: er](/rest/api/sql/managedinstances/delete).

**Hur lång tid tar det att skapa eller uppdatera en instans eller återställa en databas?**

Förväntad tid för att skapa en ny hanterad instans eller ändra tjänst nivåerna (virtuella kärnor, Storage), är beroende av flera faktorer. Se [hanterings åtgärder](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Namngivningskonventioner

**Kan en hanterad instans ha samma namn som en SQL Server lokal instans?**

Det finns inte stöd för att ändra namnet på en hanterad instans.

**Kan jag ändra DNS-zonens prefix?**

Ja, standard-DNS-zon för hanterade instanser *. Database.Windows.net* kan ändras. 

Om du vill använda en annan DNS-zon i stället för standard, till exempel *. contoso.com*: 
- Använd CliConfig för att definiera ett alias. Verktyget är bara ett gränssnitt för register inställningar, så det kan göras med hjälp av en grup princip eller ett skript.
- Använd *CNAME* med alternativet *TrustServerCertificate = True* .

## <a name="migration-options"></a>Migreringsalternativ

**Hur migrerar jag från Azure SQL Database en eller elastisk pool till SQL-hanterad instans?**

Den hanterade instansen ger samma prestanda nivåer per beräknings-och lagrings storlek som andra distributions alternativ för Azure SQL Database. Om du vill konsolidera data på en enskild instans, eller om du bara behöver en funktion som stöds enbart i hanterade instanser, kan du migrera dina data med hjälp av funktionen för att exportera/importera (BACPAC). Här följer andra sätt att överväga för SQL Database migrering till SQL-hanterad instans: 
- Använda [extern data källa](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Använda [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Använda [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Hur kan jag migrera min instans databas till en enda Azure SQL Database?**

Ett alternativ är att [Exportera en databas till BACPAC](../database/database-export.md) och sedan [Importera BACPAC-filen](../database/database-import.md). Detta är den rekommenderade metoden om databasen är mindre än 100 GB.

[Transaktionsreplikering](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) kan användas om alla tabeller i databasen har *primära* nycklar och det inte finns några minnesbaserade OLTP-objekt i databasen.

Det går inte att återställa interna COPY_ONLY säkerhets kopieringar från hanterade instanser till SQL Server eftersom den hanterade instansen har en högre databas version jämfört med SQL Server. Mer information finns i [Kopiera endast säkerhets kopiering](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Hur kan jag migrera min SQL Server-instans till SQL-hanterad instans?**

Information om hur du migrerar din SQL Server instans finns i [SQL Server instans migrering till en Azure SQL-hanterad instans](migrate-to-instance-from-sql-server.md).

**Hur kan jag migrera från andra plattformar till SQL-hanterad instans?**

Information om migrering om migrering från andra plattformar finns i [guiden Migrera Azure Database](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Växla maskin varu generering 

**Kan jag byta maskin vara för den hanterade instansen mellan generation 4 och gen 5 Online?**

Automatisk Online-växling från Gen4 till Gen5 är möjligt om Gen5-maskinvaran är tillgänglig i den region där din hanterade instans är etablerad. I det här fallet kan du kontrol lera [vCore modell översikts sida](../database/service-tiers-vcore.md) som förklarar hur du växlar mellan maskin varu generationer.

Detta är en långvarig åtgärd som en ny hanterad instans skapas i bakgrunden och databaserna överförs automatiskt mellan den gamla och nya instansen med en snabb redundans i slutet av processen.

Obs! Gen4-maskinvara föråldras och är inte längre tillgänglig för nya distributioner. Alla nya databaser måste distribueras på Gen5 maskin vara. Växling från Gen5 till Gen4 är inte heller tillgängligt.

## <a name="performance"></a>Prestanda 

**Hur kan jag jämföra prestanda för hanterade instanser för att SQL Server prestanda?**

För en prestanda jämförelse mellan hanterade instanser och SQL Server är en bra start punkt [bästa praxis för prestanda jämförelse mellan Azure SQL-hanterad instans och SQL Server](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) artikel.

**Vad orsakar prestanda skillnader mellan hanterade instanser och SQL Server?**

Se [viktiga orsaker till prestanda skillnader mellan SQL-hanterad instans och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Mer information om storleken på logg filen påverkar Generell användning hanterade instans prestandan finns i [effekt av logg fils storlek på generell användning](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Hur gör jag för att finjustera prestanda för min hanterade instans?**

Du kan optimera prestanda för din hanterade instans genom att:
- [Automatisk justering](../database/automatic-tuning-overview.md) som ger högsta prestanda och stabila arbets belastningar genom kontinuerlig prestanda justering baserat på AI och maskin inlärning.
-   [Minnes intern OLTP](../in-memory-oltp-overview.md) som ger bättre data flöde och svars tid på transaktions bearbetnings arbets belastningar och ger snabbare affärs insikter. 

Överväg att använda några av de *rekommenderade metoderna* för [program-och databas justering](../database/performance-guidance.md#tune-your-database)för att finjustera prestanda ytterligare.
Om din arbets belastning består av många små transaktioner bör du överväga att [byta Anslutnings typ från proxy till omdirigeringsläge](connection-types-overview.md#changing-connection-type) för kortare latens och högre data flöde.

## <a name="monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar

**Vilka är alternativen för övervakning och aviseringar för min hanterade instans?**

Alla möjliga alternativ för att övervaka och varna för användning och prestanda för SQL-hanterad instans finns i [blogg inlägget om hanterings alternativ för Azure SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). För prestanda övervakning i real tid för SQL MI, se [prestanda övervakning i real tid för Azure SQL DB-hanterad instans](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Kan jag använda SQL profileraren för prestanda spårning?**

Ja, SQL-profiler stöds eller SQL-hanterad instans. Mer information finns i [SQL](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)profiler.

**Är Database Advisor och Query Performance Insight stöd för hanterade instans databaser?**

Nej, de stöds inte. Du kan använda [DMV: er](../database/monitoring-with-dmvs.md) och [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) tillsammans med [SQL profilerare](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) och [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) för att övervaka dina databaser.

**Kan jag skapa mått varningar på SQL-hanterad instans?**

Ja. Instruktioner finns i [skapa aviseringar för SQL-hanterad instans](alerts-create.md).

**Kan jag skapa mått varningar för en databas i en hanterad instans?**

Du kan inte, aviserings mått är bara tillgängliga för hanterade instanser. Aviserings mått för enskilda databaser i den hanterade instansen är inte tillgängliga.

## <a name="storage-size"></a>Lagrings storlek

**Vad är den maximala lagrings storleken för SQL-hanterad instans?**

Lagrings storleken för SQL-hanterad instans beror på den valda tjänst nivån (Generell användning eller Affärskritisk). Information om lagrings begränsningar för dessa tjänst nivåer finns i [Egenskaper för tjänst nivå](../database/service-tiers-general-purpose-business-critical.md).

**Vilken minsta lagrings storlek är tillgänglig för en hanterad instans?**

Den minsta mängden lagring som är tillgänglig i en instans är 32 GB. Lagrings utrymme kan läggas till i steg om 32 GB upp till den maximala lagrings storleken. Första 32 GB är kostnads fritt.

**Kan jag öka det lagrings utrymme som är tilldelat till en instans, oberoende av beräknings resurser?**

Ja, du kan köpa tilläggs lagring, oberoende från beräkning, i viss omfattning. Se *Max reserverad instans* i [tabellen](resource-limits.md#hardware-generation-characteristics).

**Hur kan jag optimera min lagrings prestanda i Generell användning tjänst nivån?**

Information om hur du optimerar lagrings prestanda finns [i metod tips för lagring i generell användning](https://techcommunity.microsoft.com).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

**Dras lagringen av säkerhets kopian från min hanterade instans lagring?**

Nej, lagring av säkerhets kopior dras inte av från lagrings utrymmet för den hanterade instansen. Lagrings utrymmet för säkerhets kopian är oberoende från instans lagrings utrymmet och är inte begränsat. Säkerhets kopierings lagringen begränsas av tids perioden för att behålla säkerhets kopieringen av dina instans databaser, vilket kan konfigureras upp till 35 dagar. Mer information finns i [automatiska säkerhets kopieringar](../database/automated-backups-overview.md).

**Hur kan jag se när automatiska säkerhets kopieringar görs på min hanterade instans?**

Information om hur du spårar när automatiska säkerhets kopieringar har utförts på en hanterad instans finns i [spåra den automatiserade säkerhets kopieringen för en hanterad Azure SQL-instans](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)

**Stöds säkerhets kopiering på begäran?**

Ja, du kan skapa en fullständig kopia av en fullständig säkerhets kopia i Azure-Blob Storage, men den kommer bara att återställas i en hanterad instans. Mer information finns i [Kopiera endast säkerhets kopiering](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). Det är dock omöjligt att kopiera säkerhets kopior om databasen krypteras av tjänstehanterade TDE eftersom det inte går att komma åt certifikatet som används för kryptering. I sådana fall använder du funktionen för att återställa en plats i taget för att flytta databasen till en annan SQL-hanterad instans eller växla till kundhanterad nyckel.

**Stöds intern återställning (från. bak-filer) till hanterad instans?**

Ja, det stöds och är tillgängligt för SQL Server 2005 +-versioner.  Om du vill använda intern återställning laddar du upp. bak-filen till Azure Blob Storage och kör T-SQL-kommandon. Mer information finns i [Native Restore from URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Verksamhetskontinuitet

**Replikeras mina system databaser till den sekundära instansen i en grupp för växling vid fel?**

System databaser replikeras inte till den sekundära instansen i en failover-grupp. Därför går det inte att använda scenarier som är beroende av objekt från system databaser på den sekundära instansen om inte objekten skapas manuellt på den sekundära. Information om lösningar finns i [Aktivera scenarier som är beroende av objektet från system databaserna](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Nätverkskrav 

**Vilka är de aktuella inkommande/utgående NSG-begränsningarna i under nätet för hanterade instanser?**

De nödvändiga reglerna för NSG och UDR dokumenteras [här](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)och anges automatiskt av tjänsten.
Tänk på att dessa regler är bara de som vi behöver för att underhålla tjänsten. Om du vill ansluta till en hanterad instans och använda olika funktioner måste du ange ytterligare, funktions regler som du behöver underhålla.

**Hur kan jag ange regler för inkommande NSG på hanterings portar?**

SQL-hanterad instans ansvarar för att ange regler för hanterings portar. Detta uppnås med hjälp av funktioner som heter [service-Aided under näts konfiguration](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Detta är för att säkerställa oavbrutet flöde av hanterings trafik för att uppfylla ett service avtal.

**Kan jag hämta de käll-IP-intervall som används för inkommande hanterings trafik?**

Ja. Du kan analysera trafiken som kommer via nätverks säkerhets gruppen genom att [konfigurera Network Watcher flödes loggar](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Kan jag ange NSG för att kontrol lera åtkomsten till data slut punkten (port 1433)?**

Ja. När en hanterad instans har tillhandahållits kan du ange NSG som styr inkommande åtkomst till port 1433. Vi rekommenderar att du begränsar dess IP-intervall så mycket som möjligt.

**Kan jag ange NVA eller lokal brand vägg för att filtrera utgående hanterings trafik baserat på FQDN?**

Nej. Detta stöds inte av flera skäl:
-   Routnings trafik som representerar svar på inkommande hanterings begär Anden blir asymmetrisk och fungerar inte.
-   Routning av trafik som går till lagring påverkas av begränsningar och svars tider för data flödet så på det sättet att vi inte kan tillhandahålla förväntade tjänste kvalitet och tillgänglighet.
-   De här konfigurationerna är beroende av erfarenheten fel känsliga och stöds inte.

**Kan jag ange NVA eller brand vägg för utgående trafik som inte hanterar hanteringen?**

Ja. Det enklaste sättet att åstadkomma detta är att lägga till 0/0-regeln i en UDR som är kopplad till under nätet för hanterade instanser för att dirigera trafik via NVA.
 
**Hur många IP-adresser behöver jag för en hanterad instans?**

Under nätet måste ha tillräckligt många tillgängliga [IP-adresser](connectivity-architecture-overview.md#network-requirements). Information om hur du fastställer VNet-undernätets storlek för SQL-hanterad instans finns i [bestämma storlek och intervall för en hanterad instans](./vnet-subnet-determine-size.md). 

**Vad händer om det inte finns tillräckligt med IP-adresser för att utföra instans uppdaterings åtgärden?**

Om det inte finns tillräckligt med [IP-adresser](connectivity-architecture-overview.md#network-requirements) i under nätet där din hanterade instans är etablerad måste du skapa ett nytt undernät och en ny hanterad instans i det. Vi föreslår också att det nya undernätet skapas med fler tilldelade IP-adresser så att liknande situationer kan undvikas vid framtida uppdateringsåtgärder. När den nya instansen har allokerats kan du manuellt säkerhetskopiera och återställa data mellan de gamla och nya instanserna eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell).

**Behöver jag ett tomt undernät för att skapa en hanterad instans?**

Nej. Du kan antingen använda ett tomt undernät eller ett undernät som redan innehåller hanterade instanser. 

**Kan jag ändra under nätets adress intervall?**

Inte om det finns hanterade instanser i. Det här är en begränsning i Azure nätverks infrastrukturen. Du får bara [lägga till ytterligare adress utrymme i ett tomt undernät](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Kan jag flytta min hanterade instans till ett annat undernät?**

Nej. Det här är en aktuell design begränsning för hanterade instanser. Du kan dock etablera en ny instans i ett annat undernät och manuellt säkerhetskopiera och återställa data mellan den gamla och den nya instansen eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell).

**Behöver jag ett tomt virtuellt nätverk för att skapa en hanterad instans?**

Det här krävs inte. Du kan antingen [skapa ett virtuellt nätverk för Azure SQL-hanterad instans](./virtual-network-subnet-create-arm-template.md) eller [Konfigurera ett befintligt virtuellt nätverk för Azure SQL-hanterad instans](./vnet-existing-add-subnet.md).

**Kan jag placera en hanterad instans med andra tjänster i ett undernät?**

Nej. För närvarande har vi inte stöd för att placera en hanterad instans i ett undernät som redan innehåller andra resurs typer.

## <a name="connectivity"></a>Anslutning 

**Kan jag ansluta till min hanterade instans med hjälp av IP-adress?**

Nej, detta stöds inte. En hanterad Instanss värdnamn mappar till belastningsutjämnaren framför den hanterade instansens virtuella kluster. Eftersom ett virtuellt kluster kan vara värd för flera hanterade instanser, kan en anslutning inte dirigeras till rätt hanterad instans utan att ange dess namn.
Mer information om arkitektur för virtuella kluster i SQL-hanterad instans finns i [arkitektur för virtuella kluster anslutningar](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kan min hanterade instans ha en statisk IP-adress?**

Detta stöds inte för närvarande.

I sällsynta men nödvändiga situationer kan vi behöva utföra en online-migrering av en hanterad instans till ett nytt virtuellt kluster. Vid behov är migreringen på grund av ändringar i vår teknik stack som syftar till att förbättra säkerheten och tillförlitligheten för tjänsten. När du migrerar till ett nytt virtuellt kluster ändras IP-adressen som är mappad till värd namnet för den hanterade instansen. Den hanterade instans tjänsten tillåter inte stöd för statisk IP-adress och förbehåller sig rätten att ändra den utan att Observera att den är en del av vanliga underhålls cykler.

Därför förhindrar vi starkt att du förlitar dig på oföränderlighets i IP-adressen eftersom det kan orsaka onödigt drift stopp.

**Har den hanterade instansen en offentlig slut punkt?**

Ja. Den hanterade instansen har en offentlig slut punkt som används som standard endast för Service Management, men en kund kan även aktivera den för data åtkomst. Mer information finns i [använda SQL-hanterad instans med offentliga slut punkter](./public-endpoint-overview.md). Om du vill konfigurera en offentlig slut punkt går du till [Konfigurera offentlig slut punkt i SQL-hanterad instans](public-endpoint-configure.md).

**Hur kontrollerar hanterad instans åtkomst till den offentliga slut punkten?**

Den hanterade instansen styr åtkomst till den offentliga slut punkten på både nätverks-och program nivå.

Hanterings-och distributions tjänster ansluter till en hanterad instans med hjälp av en [hanterings slut punkt](./connectivity-architecture-overview.md#management-endpoint) som mappar till en extern belastningsutjämnare. Trafiken dirigeras till noderna endast om den tas emot på en fördefinierad uppsättning portar som endast används av hanterings komponenter för den hanterade instansen. En inbyggd brand vägg på noderna har kon figurer ATS för att tillåta trafik enbart från Microsoft IP-intervall. Certifikat autentiserar all kommunikation mellan hanterings komponenter och hanterings planet gemensamt. Mer information finns i [anslutnings arkitektur för SQL-hanterad instans](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kan jag använda den offentliga slut punkten för att komma åt data i hanterade instans databaser?**

Ja. Kunden måste aktivera offentlig slut punkts data åtkomst från [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /arm och konfigurera NSG för att låsa åtkomsten till data porten (port nummer 3342). Mer information finns i [Konfigurera offentlig slut punkt i Azure SQL-hanterad instans](public-endpoint-configure.md) och [använda Azure SQL Managed instance på ett säkert sätt med offentlig slut punkt](public-endpoint-overview.md). 

**Kan jag ange en anpassad port för SQL data-slutpunkter?**

Nej, det här alternativet är inte tillgängligt.  För privat data slut punkt använder den hanterade instansen standard port nummer 1433 och för offentlig data slut punkt använder den hanterade instansen standard port nummer 3342.

**Vilket är det rekommenderade sättet att ansluta hanterade instanser i olika regioner?**

ExpressRoute-peering i ExpressRoute är det bästa sättet att göra det. Global peering för virtuella nätverk stöds med den begränsning som beskrivs i Obs!  

> [!IMPORTANT]
> [Den 9/22/2020 vi presenterade global peering för virtuella nätverk för nyligen skapade virtuella kluster](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Det innebär att global virtuell nätverks-peering stöds för SQL-hanterade instanser som skapats i tomma undernät efter meddelandets datum, även för alla efterföljande hanterade instanser som skapats i dessa undernät. För alla andra SQL-hanterade instanser är peering-stödet begränsat till nätverken i samma region på grund av [begränsningarna i den globala virtuella nätverks-peeringen](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Se även det relevanta avsnittet i artikeln [vanliga frågor och svar om Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

Om ExpressRoute-kretsen för ExpressRoute och globala virtuella nätverk inte är möjlig, är det enda alternativet att skapa plats-till-plats-VPN-anslutning ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Minimera data exfiltrering-risker  

**Hur kan jag minimera data exfiltrering riskerna?**

För att minimera eventuella data exfiltrerings risker rekommenderar vi att du använder en uppsättning säkerhets inställningar och kontroller:

- Aktivera [Transparent datakryptering (TDE)](../database/transparent-data-encryption-tde-overview.md) på alla databaser.
- Inaktivera CLR (Common Language Runtime). Detta rekommenderas även lokalt.
- Använd endast Azure Active Directory (Azure AD)-autentisering.
- Få åtkomst till instansen med ett konto med låg privilegie rad DBA.
- Konfigurera åtkomst till JIT-bygel för kontot sysadmin.
- Aktivera [SQL-granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)och integrera den med aviserings metoder.
- Aktivera [hot identifiering](../database/threat-detection-configure.md) från [Azure Defender för SQL](../database/azure-defender-for-sql.md) Suite.

## <a name="dns"></a>DNS

**Kan jag konfigurera en anpassad DNS för SQL-hanterad instans?**

Ja. Se [Konfigurera en anpassad DNS för Azure SQL-hanterad instans](./custom-dns-configure.md).

**Kan jag göra DNS-uppdatering?**

Ja. Se [Synkronisera virtuella nätverk DNS-servrar på SQL-hanterad instans virtuellt kluster](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Ändra tidszon

**Kan jag ändra tids zonen för en befintlig hanterad instans?**

Tids zons konfigurationen kan ställas in när en hanterad instans skapas för första gången. Det finns inte stöd för att ändra tids zonen för en befintlig hanterad instans. Mer information finns i [begränsningar för tids zoner](timezones-overview.md#limitations).

I lösningarna ingår att skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhets kopiering och återställning, eller vad vi rekommenderar, utföra en [kors inaktive rad återställning av en viss tidpunkt](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Säkerhet och databas kryptering

**Är sysadmin-serverrollen tillgänglig för SQL-hanterad instans?**

Ja, kunder kan skapa inloggningar som är medlemmar i sysadmin-rollen.  Kunder som antar behörigheten sysadmin förutsätter också ansvar för drift av instansen, vilket kan påverka SLA-åtagandet negativt. Information om hur du lägger till inloggning till sysadmin-serverrollen finns i [Azure AD-autentisering](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Stöds transparent datakryptering för SQL-hanterad instans?**

Ja, transparent datakryptering stöds för SQL-hanterad instans. Mer information finns i [Transparent datakryptering för SQL-hanterad instans](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Kan jag utnyttja modellen "ta med din egen nyckel" för TDE?**

Ja, Azure Key Vault för BYOK-scenario är tillgängligt för Azure SQL-hanterad instans. Mer information finns i [Transparent datakryptering med kundhanterad nyckel](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Kan jag migrera en krypterad SQL Server databas?**

Ja, det kan du. Om du vill migrera en krypterad SQL Server databas måste du exportera och importera dina befintliga certifikat till en hanterad instans och sedan ta en fullständig säkerhets kopia av databasen och återställa den i en hanterad instans. 

Du kan också använda [Azure Database migration service](https://azure.microsoft.com/services/database-migration/) för att migrera TDE-krypterade databaser.

**Hur konfigurerar jag TDE skydds rotation för SQL-hanterad instans?**

Du kan rotera TDE-skydd för en hanterad instans med hjälp av Azure Cloud Shell. Instruktioner finns i [Transparent datakryptering i SQL-hanterad instans med hjälp av din egen nyckel från Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Kan jag återställa min krypterade databas till SQL-hanterad instans?**

Ja, du behöver inte dekryptera databasen för att återställa den till SQL-hanterad instans. Du måste ange ett certifikat/en nyckel som används som krypterings nyckel skydd på käll systemet till SQL-hanterad instans för att kunna läsa data från den krypterade säkerhets kopierings filen. Det finns två möjliga sätt att göra det:

- *Ladda upp certifikat – skydd mot SQL-hanterad instans*. Det kan bara göras med PowerShell. I [exempel skriptet](./tde-certificate-migrate.md) beskrivs hela processen.
- *Överför asymmetrisk nyckel-skydd till Azure Key Vault-och Point SQL-hanterad instans till den*. Den här metoden liknar BYOK-TDE, som också använder Key Vault-integrering för att lagra krypterings nyckeln. Om du inte vill använda nyckeln som krypterings nyckel skydd och bara vill göra nyckeln tillgänglig för SQL-hanterad instans för att återställa krypterade databaser, följ instruktionerna för att [Konfigurera BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)och markera inte kryss rutan **gör den markerade nyckeln till standard TDE-skyddet**.

När du har gjort krypterings skyddet tillgängligt för SQL-hanterad instans kan du fortsätta med standard proceduren för databas återställning.

## <a name="purchasing-models-and-benefits"></a>Inköps modeller och förmåner

**Vilka inköps modeller är tillgängliga för SQL-hanterad instans?**

SQL-hanterad instans erbjuder [vCore-baserad inköps modell](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Vilka kostnads förmåner är tillgängliga för SQL-hanterad instans?**

Du kan spara kostnader med Azure SQL-förmåner på följande sätt:
-   Maximera befintliga investeringar i lokala licenser och Spara upp till 55 procent med [Azure Hybrid-förmån](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-   Genomför en reservation för beräknings resurser och Spara upp till 33 procent med [reserverad instans förmån](../database/reserved-capacity-overview.md). Kombinera detta med Azure Hybrid-förmånen och Spara upp till 82 procent. 
-   Spara upp till 55 procent jämfört med priser för [Azure dev/test-](https://azure.microsoft.com/pricing/dev-test/) priser som erbjuder rabatterade priser för dina kontinuerliga utvecklings-och testnings arbets belastningar.

**Vem är berättigad till reserverad instans?**

För att vara kvalificerad för reserverad instans förmån måste prenumerations typen vara ett Enterprise Agreement (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Mer information om reservationer finns i [reserverad instans förmån](../database/reserved-capacity-overview.md). 

**Är det möjligt att avbryta, byta ut eller återbetala reservationer?**

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fakturering för hanterad instans och lagring av säkerhets kopior

**Vad är pris alternativen för SQL-hanterad instans?**

Se [prissättnings sida](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)för att utforska pris alternativ för hanterade instanser.

**Hur kan jag spåra fakturerings kostnader för min hanterade instans?**

Du kan göra det med hjälp av [Azure Cost Management-lösningen](../../cost-management-billing/index.yml). Gå till **prenumerationer** i [Azure Portal](https://portal.azure.com) och välj **kostnads analys**. 

Använd alternativet **ackumulerade kostnader** och filtrera sedan efter **resurs typ** som `microsoft.sql/managedinstances` .

**Hur mycket kostnader för automatisk säkerhets kopiering?**

Du får lika stor mängd ledigt lagrings utrymme som det reserverade lagrings utrymmet för lagrings utrymmet, oavsett vilken kvarhållning av säkerhets kopior som angetts. Om lagrings förbrukningen för säkerhets kopian finns inom det allokerade lagrings utrymmet för ledig säkerhets kopiering får du ingen extra kostnad för automatiska säkerhets kopieringar på den hanterade instansen. därför är det kostnads fritt. Om du överskrider användningen av lagrings utrymme för säkerhets kopior över det lediga utrymmet kommer kostnaderna för cirka $0,20-$0,24 per GB/månad i USA-regioner, eller så kan du se sidan med priser för information om din region. Mer information finns i avsnittet om [förklarad lagrings förbrukning](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Hur kan jag övervaka fakturerings kostnader för lagrings förbrukningen för säkerhets kopiering?**

Du kan övervaka kostnaden för lagring av säkerhets kopior via Azure Portal. Mer information finns i [övervaka kostnader för automatisk säkerhets kopiering](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Hur kan jag optimera mina kostnader för säkerhets kopiering av lagring på den hanterade instansen?**

Information om hur du optimerar kostnaderna för lagring av säkerhets kopior finns i [fin justering av säkerhets kopiering på SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Användnings fall med kostnads besparingar

**Var kan jag hitta användnings fall och resulterande kostnads besparingar med SQL-hanterad instans?**

Fallstudier om SQL-hanterade instanser:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

För att få en bättre förståelse för de fördelar, kostnader och risker som är kopplade till distributionen av den hanterade Azure SQL-instansen finns det också en Forrester-undersökning: [den totala ekonomiska effekten av Microsoft Azure SQL Database hanterade instansen](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Lösen ords princip 

**Vilka lösen ords principer används för SQL-inloggningar med SQL-hanterade instanser?**

SQL-hanterad instans lösen ords princip för SQL-inloggningar ärver Azures plattforms principer som tillämpas på virtuella datorer som utgör den hanterade instansen. För tillfället går det inte att ändra någon av dessa inställningar eftersom inställningarna definieras av Azure och ärvs av en hanterad instans.

 > [!IMPORTANT]
 > Azure-plattformen kan ändra princip kraven utan att meddela tjänster som förlitar sig på dessa principer.

**Vad är nuvarande Azure Platform-principer?**

Varje inloggning måste ange sitt lösen ord vid inloggningen och ändra dess lösen ord när den når maximal ålder.

| **Princip** | **Säkerhetsinställning** |
| --- | --- |
| Högsta ålder för lösen ord | 42 dagar |
| Lägsta ålder för lösen ord | 1 dag |
| Minsta längd på lösenord | 10 tecken |
| Lösen ordet måste uppfylla komplexitets kraven | Enabled |

**Är det möjligt att inaktivera lösen ords komplexitet och förfallo datum i SQL-hanterad instans på inloggnings nivå?**

Ja, det är möjligt att kontrol lera CHECK_POLICY och CHECK_EXPIRATION fält på inloggnings nivå. Du kan kontrol lera aktuella inställningar genom att köra följande T-SQL-kommando:

```sql
SELECT *
FROM sys.sql_logins
```

Därefter kan du ändra angivna inloggnings inställningar genom att köra:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Ersätt "test" med önskat inloggnings namn och justera princip-och förfallo värden)


## <a name="service-updates"></a>Tjänstuppdateringar

**Vilken ändring av rot certifikat utfärdare för Azure SQL Database & SQL-hanterad instans?**

Se [certifikat rotation för Azure SQL Database & SQL-hanterad instans](../updates/ssl-root-certificate-expiring.md). 

**Vad är en planerad underhålls händelse för SQL-hanterad instans?**

Se [Planera för Azures underhålls händelser i SQL-hanterad instans](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Feedback och support för Azure

**Var kan jag lämna mina idéer för förbättringar av SQL-hanterade instanser?**

Du kan rösta på en ny hanterad instans funktion eller införa en ny förbättrings idé om röstning på [SQL Managed instance feedback forum](https://feedback.azure.com/forums/915676-sql-managed-instance). På så sätt kan du bidra till produkt utvecklingen och hjälpa oss att prioritera våra potentiella förbättringar.

**Hur gör jag för att skapa en support förfrågan för Azure?**

Information om hur du skapar en support förfrågan för Azure finns i [så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
