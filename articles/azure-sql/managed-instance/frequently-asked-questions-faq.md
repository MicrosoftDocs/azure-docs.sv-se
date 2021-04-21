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
ms.openlocfilehash: 16f937286b967aaea8ec6a16e97835b2de5a0331
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765511"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) om Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller de vanligaste frågorna om [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funktioner som stöds

**Var hittar jag en lista över funktioner som stöds på SQL Managed Instance?**

En lista över funktioner som stöds i SQL Managed Instance finns i [Azure SQL Managed Instance funktioner](../database/features-comparison.md).

Skillnader i syntax och beteende mellan Azure SQL Managed Instance och SQL Server finns i [T-SQL skillnader från SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Teknisk specifikation, resursbegränsningar och andra begränsningar
 
**Var hittar jag tekniska egenskaper och resursbegränsningar för SQL Managed Instance?**

Tillgängliga egenskaper för maskinvarugenerering finns i [Tekniska skillnader i maskinvarugenerationer.](resource-limits.md#hardware-generation-characteristics)
Information om tillgängliga tjänstnivåer och deras egenskaper finns [i Tekniska skillnader mellan tjänstnivåer.](resource-limits.md#service-tier-characteristics)

**Vilken tjänstnivå är jag berättigad till?**

Alla kunder är berättigade till alla tjänstnivåer. Men om du vill byta ut dina befintliga licenser mot rabatterade priser på Azure SQL Managed Instance med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/)bör du komma ihåg att SQL Server Enterprise Edition-kunder med Software Assurance är berättigade till [prestandanivåerna Generell användning](../database/service-tier-general-purpose.md) eller [Affärskritisk](../database/service-tier-business-critical.md) och SQL Server Standard Edition-kunder med Software Assurance är berättigade till prestandanivån Generell användning. Mer information finns i [Specifika rättigheter för AHB.](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)

**Vilka prenumerationstyper stöds för SQL Managed Instance?**

En lista över prenumerationstyper som stöds finns i [Prenumerationstyper som stöds.](resource-limits.md#supported-subscription-types) 

**Vilka Azure-regioner stöds?**

Hanterade instanser kan skapas i de flesta Azure-regioner. Se [Regioner som stöds för SQL Managed Instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du behöver en hanterad instans i en region som för närvarande inte stöds skickar du [en supportbegäran via Azure Portal](../database/quota-increase-request.md).

**Finns det några kvotbegränsningar för SQL Managed Instance distributioner?**

Hanterad instans har två standardgränser: gränsen för antalet undernät som du kan använda och en gräns för hur många virtuella kärnor du kan etablera. Gränserna varierar mellan prenumerationstyper och regioner. En lista över regionala resursbegränsningar efter prenumerationstyp finns i tabellen regional [resursbegränsning](resource-limits.md#regional-resource-limitations). Det här är mjuka gränser som kan ökas på begäran. Om du behöver etablera fler hanterade instanser i dina aktuella regioner skickar du en supportbegäran för att öka kvoten med hjälp av Azure Portal. Mer information finns i Begär [kvotökningar för Azure SQL Database](../database/quota-increase-request.md).

**Kan jag öka antalet databaser (100) på min hanterade instans på begäran?**

Nej, och för närvarande finns det inga planer på att öka antalet databaser på SQL Managed Instance. 

**Var kan jag migrera om jag har mer än 8 TB data?**
Du kan migrera till andra Azure-varianter som passar din arbetsbelastning: [Azure SQL Database Hyperskala](../database/service-tier-hyperscale.md) [eller SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Var kan jag migrera om jag har specifika maskinvarukrav som större RAM-minne till vCore-förhållande eller flera processorer?**
Du kan överväga att migrera till [SQL Server i Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) eller [Azure SQL Database](../database/sql-database-paas-overview.md) minnes-/processoroptimerad.

## <a name="known-issues-and-defects"></a>Kända problem och defekter

**Var hittar jag kända problem och defekter?**

Information om produktfel och kända problem finns i [Kända problem.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Nya funktioner

**Var hittar jag de senaste funktionerna och funktionerna i den offentliga förhandsversionen?**

Nya funktioner och förhandsgranskningsfunktioner finns i [Release Notes (Information om förhandsversioner).](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Skapa, uppdatera, ta bort eller flytta SQL Managed Instance

**Hur etablerar jag SQL Managed Instance?**

Du kan etablera en instans från [Azure Portal,](instance-create-quickstart.md) [PowerShell,](scripts/create-configure-managed-instance-powershell.md) [Azure CLI och](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) [ARM-mallar.](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)

**Kan jag etablera hanterade instanser i en befintlig prenumeration?**

Ja, du kan etablera en hanterad instans i en befintlig prenumeration om prenumerationen tillhör [prenumerationstyperna som stöds.](resource-limits.md#supported-subscription-types)

**Varför kan jag inte etablera en hanterad instans i undernätet, vilket namn börjar med en siffra?**

Det här är en aktuell begränsning för underliggande komponent som verifierar undernätsnamnet mot regex ^[a-zA-Z_][^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ]*(?<![ \. \s])$. Alla namn som skickar regex och är giltiga undernätsnamn stöds för närvarande.

**Hur skalar jag min hanterade instans?**

Du kan skala din hanterade instans [från Azure Portal,](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation) [PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell) [Azure CLI eller](/cli/azure/sql/mi#az_sql_mi_update) [ARM-mallar.](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)

**Kan jag flytta min hanterade instans från en region till en annan?**

Ja, det kan du. Anvisningar finns i Flytta [resurser mellan regioner.](../database/move-resources-across-regions.md)

**Hur tar jag bort min hanterade instans?**

Du kan ta bort hanterade instanser via Azure Portal, [PowerShell,](/powershell/module/az.sql/remove-azsqlinstance) [Azure CLI](/cli/azure/sql/mi#az_sql_mi_delete) eller [Resource Manager REST-API:er.](/rest/api/sql/managedinstances/delete)

**Hur lång tid tar det att skapa eller uppdatera en instans eller återställa en databas?**

Förväntad tid för att skapa en ny hanterad instans eller ändra tjänstnivåer (virtuella kärnor, lagring) beror på flera faktorer. Se [Hanteringsåtgärder.](sql-managed-instance-paas-overview.md#management-operations)
 
## <a name="naming-conventions"></a>Namngivningskonventioner

**Kan en hanterad instans ha samma namn som en SQL Server lokal instans?**

Det går inte att ändra namnet på en hanterad instans.

**Kan jag ändra DNS-zonprefix?**

Ja, .database.windows.net *DNS-zonens* standardvärde kan ändras. 

Om du vill använda en annan DNS-zon i stället för standardvärdet, till exempel *.contoso.com*: 
- Använd CliConfig för att definiera ett alias. Verktyget är bara en omser för registerinställningar, så det kan även göras med hjälp av grupprinciper eller skript.
- Använd *CNAME* med *alternativet TrustServerCertificate=true.*

## <a name="migration-options"></a>Migreringsalternativ

**Hur migrerar jag från Azure SQL Database enskild eller elastisk pool till SQL Managed Instance?**

Hanterad instans erbjuder samma prestandanivåer per beräkning och lagringsstorlek som andra distributionsalternativ för Azure SQL Database. Om du vill konsolidera data på en enda instans, eller om du bara behöver en funktion som stöds exklusivt i en hanterad instans, kan du migrera dina data med hjälp av funktionen export/import (BACPAC). Här är några andra sätt att överväga SQL Database migrering till SQL Managed Instance: 
- Använda [extern datakälla](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Använda [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Använda [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Hur migrerar jag min instansdatabas till en enda Azure SQL Database?**

Ett alternativ är att [exportera en databas till BACPAC](../database/database-export.md) och sedan importera [BACPAC-filen](../database/database-import.md). Det här är den rekommenderade metoden om databasen är mindre än 100 GB.

[Transaktionsreplikering](replication-two-instances-and-sql-server-configure-tutorial.md) kan användas om alla  tabeller i databasen har primära nycklar och det inte finns några minnesinspelade OLTP-objekt i databasen.

Inbyggda COPY_ONLY säkerhetskopior som tas från en hanterad instans kan inte återställas till SQL Server eftersom den hanterade instansen har en högre databasversion jämfört med SQL Server. Mer information finns i [Kopiera endast säkerhetskopiering.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Hur migrerar jag min SQL Server instans till SQL Managed Instance?**

Om du vill migrera SQL Server-instansen kan [du SQL Server migrering till Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

**Hur migrerar jag från andra plattformar till SQL Managed Instance?**

Migreringsinformation om migrering från andra plattformar finns i [Migreringsguide för Azure Database.](https://datamigration.microsoft.com/)

## <a name="switch-hardware-generation"></a>Växla maskinvarugenerering 

**Kan jag byta maskinvarugenerering för min hanterade instans mellan Gen 4 och Gen 5 online?**

Automatisk onlineväxling från Gen4 till Gen5 är möjligt om Gen5-maskinvara är tillgänglig i den region där din hanterade instans har etablerats. I det här fallet kan du kontrollera [översiktssidan för vCore-modellen](../database/service-tiers-vcore.md) som förklarar hur du växlar mellan maskinvarugenerationer.

Det här är en långvarig åtgärd eftersom en ny hanterad instans etableras i bakgrunden och databaserna överförs automatiskt mellan den gamla och den nya instansen med en snabb redundans i slutet av processen.

Obs! Gen4-maskinvaran fasas ut och är inte längre tillgänglig för nya distributioner. Alla nya databaser måste distribueras på Gen5-maskinvara. Det går inte heller att växla från Gen5 till Gen4.

## <a name="performance"></a>Prestanda 

**Hur kan jag jämföra prestanda för Managed Instance SQL Server prestanda?**

För en prestandajämförelse mellan hanterad instans SQL Server en bra startpunkt är artikeln Best practices for performance comparison between Azure SQL managed instance and SQL Server (Metodtips för prestandajämförelse mellan hanterad [instans och SQL Server).](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)

**Vad orsakar prestandaskillnader mellan Managed Instance och SQL Server?**

Se [Viktiga orsaker till prestandaskillnader mellan SQL Managed Instance och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Mer information om hur loggfilens storlek påverkar prestandan Generell användning managed instance finns i Påverkan av [loggfilsstorlek på Generell användning](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Hur gör jag för att justera prestanda för min hanterade instans?**

Du kan optimera prestanda för din hanterade instans genom att:
- [Automatisk justering](../database/automatic-tuning-overview.md) som ger högsta prestanda och stabila arbetsbelastningar genom kontinuerlig prestandajustering baserad på AI och maskininlärning.
-    [Minnes in memory OLTP](../in-memory-oltp-overview.md) som förbättrar dataflödet och svarstiden för transaktionsbearbetningsarbetsbelastningar och ger snabbare affärsinsikter. 

Överväg att tillämpa några av de  bästa metoderna för program- och databasjustering för att finjustera [prestandan ytterligare.](../database/performance-guidance.md#tune-your-database)
Om din arbetsbelastning består av många små transaktioner bör du överväga att byta anslutningstyp från [proxy](connection-types-overview.md#changing-connection-type) till omdirigeringsläge för kortare svarstider och högre dataflöde.

## <a name="monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar

**Vilka är alternativen för övervakning och avisering för min hanterade instans?**

Alla möjliga alternativ för att övervaka och varna SQL Managed Instance användning och prestanda finns [i Azure SQL Managed Instance för övervakningsalternativ i blogginlägget](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Mer information om prestandaövervakning i realtid för SQL MI finns i [Prestandaövervakning i realtid för Azure SQL DB Managed Instance](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Kan jag använda SQL Profiler för prestandaspårning?**

Ja, SQL Profiler stöds eller SQL Managed Instance. Mer information finns i [SQL Profiler.](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)

**Stöds Database Advisor och Query Performance Insight för Managed Instance-databaser?**

Nej, de stöds inte. Du kan använda [DMV:er](../database/monitoring-with-dmvs.md) [och Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) tillsammans med SQL [Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) och [XEvents för](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) att övervaka dina databaser.

**Kan jag skapa måttaviseringar på SQL Managed Instance?**

Ja. Anvisningar finns i Skapa [aviseringar för SQL Managed Instance](alerts-create.md).

**Kan jag skapa måttaviseringar för en databas i en hanterad instans?**

Du kan inte aviseringsmått endast för hanterade instanser. Aviseringsmått för enskilda databaser i hanterade instanser är inte tillgängliga.

## <a name="storage-size"></a>Lagringsstorlek

**Vilken är den maximala lagringsstorleken för SQL Managed Instance?**

Lagringsstorleken för SQL Managed Instance beror på den valda tjänstnivån (Generell användning eller Affärskritisk). Lagringsbegränsningar för dessa tjänstnivåer finns i [Egenskaper för tjänstnivå.](../database/service-tiers-general-purpose-business-critical.md)

**Vilken är den minsta lagringsstorleken som är tillgänglig för en hanterad instans?**

Den minsta mängden tillgängligt lagringsutrymme i en instans är 32 GB. Lagring kan läggas till i steg om 32 GB upp till den maximala lagringsstorleken. De första 32 GBna är kostnadsfria.

**Kan jag öka det lagringsutrymme som tilldelas till en instans, oberoende av beräkningsresurser?**

Ja, du kan i viss utsträckning köpa tilläggslagring oberoende av beräkning. Se *Maximalt reserverat lagringsutrymme för* instanser i [tabellen](resource-limits.md#hardware-generation-characteristics).

**Hur optimerar jag lagringsprestanda på Generell användning tjänstnivå?**

Information om hur du optimerar [lagringsprestanda finns i Metodtips för lagring i Generell användning](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

**Dras säkerhetskopieringslagringen av från min hanterade instanslagring?**

Nej, säkerhetskopieringslagring dras inte av från lagringsutrymmet för den hanterade instansen. Säkerhetskopieringslagringen är oberoende av instansens lagringsutrymme och är inte begränsad i storlek. Lagring av säkerhetskopior begränsas av tidsperioden för att behålla säkerhetskopian av dina instansdatabaser, som kan konfigureras i upp till 35 dagar. Mer information finns i [Automatisk säkerhetskopiering.](../database/automated-backups-overview.md)

**Hur ser jag när automatiska säkerhetskopieringar görs på min hanterade instans?**

Information om hur du spårar när automatiserade säkerhetskopieringar har utförts på hanterad instans finns i Så här spårar du den automatiserade [säkerhetskopieringen för en Azure SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Stöds säkerhetskopiering på begäran?**

Ja, du kan skapa en fullständig säkerhetskopia som endast kopieras i Azure Blob Storage, men den kan bara återställas i hanterad instans. Mer information finns i [Kopiera endast säkerhetskopiering.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) Säkerhetskopiering med endast kopiering är dock omöjligt om databasen krypteras av tjänst-hanterad TDE eftersom det certifikat som används för kryptering inte är tillgänglig. I sådana fall kan du använda funktionen för återställning till tidpunkt för att flytta databasen till en annan SQL Managed Instance eller växla till kund hanterad nyckel.

**Stöds intern återställning (från .bak-filer) till hanterad instans?**

Ja, det stöds och är tillgängligt för SQL Server version 2005+.  Om du vill använda intern återställning laddar du upp .bak-filen till Azure Blob Storage och kör T-SQL-kommandon. Mer information finns i [Intern återställning från URL.](./migrate-to-instance-from-sql-server.md#native-restore-from-url)

## <a name="business-continuity"></a>Verksamhetskontinuitet

**Replikeras mina systemdatabaser till den sekundära instansen i en redundansgrupp?**

Systemdatabaser replikeras inte till den sekundära instansen i en redundansgrupp. Scenarier som är beroende av objekt från systemdatabaserna är därför omöjliga på den sekundära instansen såvida inte objekten skapas manuellt på den sekundära instansen. En tillfällig lösning finns [i Aktivera scenarier som är beroende av objektet från systemdatabaserna](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Nätverkskrav 

**Vilka är de aktuella NSG-begränsningarna för inkommande/utgående trafik i undernätet för den hanterade instansen?**

De nödvändiga NSG- och UDR-reglerna [dokumenteras här](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)och anges automatiskt av tjänsten.
Tänk på att dessa regler bara är de som vi behöver för att underhålla tjänsten. Om du vill ansluta till en hanterad instans och använda olika funktioner måste du ange ytterligare funktionsspecifika regler som du behöver underhålla.

**Hur ställer jag in inkommande NSG-regler på hanteringsportar?**

SQL Managed Instance ansvarar för att ange regler för hanteringsportar. Detta uppnås med hjälp av funktioner med [namnet service-aided subnet configuration ( tjänststödd undernätskonfiguration).](connectivity-architecture-overview.md#service-aided-subnet-configuration)
Detta är för att säkerställa oavbrutet flöde av hanteringstrafik för att uppfylla ett serviceavtal.

**Kan jag hämta källans IP-intervall som används för inkommande hanteringstrafik?**

Ja. Du kan analysera trafik som kommer genom din nätverkssäkerhetsgrupp genom [att konfigurera Network Watcher flödesloggar](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Kan jag ange NSG för att styra åtkomsten till dataslutpunkten (port 1433)?**

Ja. När en hanterad instans har etablerats kan du ange en NSG som styr inkommande åtkomst till port 1433. Vi rekommenderar att du begränsar IP-intervallet så mycket som möjligt.

**Kan jag ange nva eller lokal brandvägg för att filtrera utgående hanteringstrafik baserat på FQDN?**

Nej. Detta stöds inte av flera orsaker:
-    Routning av trafik som representerar svar på inkommande hanteringsbegäran skulle vara asymmetrisk och kunde inte fungera.
-    Routning av trafik som går till lagring skulle påverkas av dataflödesbegränsningar och svarstider så att vi inte kan tillhandahålla förväntad tjänstkvalitet och tillgänglighet.
-    Baserat på erfarenhet är dessa konfigurationer felbenägna och kan inte stödjas.

**Kan jag ange NVA eller brandväggen för utgående icke-hanteringstrafik?**

Ja. Det enklaste sättet att uppnå detta är att lägga till en 0/0-regel till en UDR som är associerad med undernätet för hanterad instans för att dirigera trafik via NVA.
 
**Hur många IP-adresser behöver jag för en hanterad instans?**

Undernätet måste ha tillräckligt många tillgängliga [IP-adresser.](connectivity-architecture-overview.md#network-requirements) Information om hur du fastställer storleken på det virtuella SQL Managed Instance finns i Fastställa nödvändig storlek och intervall för [undernät för hanterad instans.](./vnet-subnet-determine-size.md) 

**Vad händer om det inte finns tillräckligt med IP-adresser för att utföra instansuppdateringsåtgärden?**

Om det inte finns tillräckligt med [IP-adresser](connectivity-architecture-overview.md#network-requirements) i undernätet där din hanterade instans har etablerats måste du skapa ett nytt undernät och en ny hanterad instans i det. Vi föreslår också att det nya undernätet skapas med fler tilldelade IP-adresser så att liknande situationer kan undvikas vid framtida uppdateringsåtgärder. När den nya instansen har etablerats kan du manuellt backa upp och återställa data mellan de gamla och nya instanserna eller utföra återställning till tidpunkt mellan [instanser.](point-in-time-restore.md?tabs=azure-powershell)

**Behöver jag ett tomt undernät för att skapa en hanterad instans?**

Nej. Du kan använda antingen ett tomt undernät eller ett undernät som redan innehåller hanterade instanser. 

**Kan jag ändra adressintervallet för undernätet?**

Inte om det finns hanterade instanser inuti. Det här är en begränsning för Nätverksinfrastruktur i Azure. Du kan bara lägga [till ytterligare adressutrymme i ett tomt undernät](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Kan jag flytta min hanterade instans till ett annat undernät?**

Nej. Det här är en aktuell designbegränsning för Managed Instance. Du kan dock etablera en ny instans i ett annat undernät och manuellt backa upp och återställa data mellan den gamla och den nya instansen eller utföra återställning till tidpunkt mellan [instanser.](point-in-time-restore.md?tabs=azure-powershell)

**Behöver jag ett tomt virtuellt nätverk för att skapa en hanterad instans?**

Det här krävs inte. Du kan [antingen Skapa ett virtuellt nätverk för Azure SQL Managed Instance](./virtual-network-subnet-create-arm-template.md) eller Konfigurera ett befintligt [virtuellt nätverk för Azure SQL Managed Instance](./vnet-existing-add-subnet.md).

**Kan jag placera en hanterad instans med andra tjänster i ett undernät?**

Nej. För närvarande stöder vi inte placering av hanterade instanser i ett undernät som redan innehåller andra resurstyper.

## <a name="connectivity"></a>Anslutning 

**Kan jag ansluta till min hanterade instans med en IP-adress?**

Nej, detta stöds inte. Värdnamnet för en hanterad instans mappar till lastbalanseraren framför den hanterade instansens virtuella kluster. Eftersom ett virtuellt kluster kan vara värd för flera hanterade instanser går det inte att dirigera en anslutning till rätt hanterad instans utan att ange dess namn.
Mer information om hur SQL Managed Instance arkitekturen för virtuella kluster finns i [Anslutningsarkitektur för virtuella kluster.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Kan min hanterade instans ha en statisk IP-adress?**

Detta stöds inte för närvarande.

I sällsynta men nödvändiga situationer kan vi behöva göra en onlinemigrering av en hanterad instans till ett nytt virtuellt kluster. Vid behov beror migreringen på ändringar i vår teknikstack som syftar till att förbättra tjänstens säkerhet och tillförlitlighet. Om du migrerar till ett nytt virtuellt kluster ändras IP-adressen som mappas till värdnamnet för den hanterade instansen. Den hanterade instanstjänsten begär inte stöd för statisk IP-adress och förbehåller sig rätten att ändra den utan föregående meddelande som en del av regelbundna underhållscykler.

Därför avråder vi starkt från att förlita oss på oföränderlighet för IP-adressen eftersom det kan orsaka onödigt driftstopp.

**Har Managed Instance en offentlig slutpunkt?**

Ja. Managed Instance har en offentlig slutpunkt som som standard endast används för Service Management, men en kund kan även aktivera den för dataåtkomst. Mer information finns i [Använda SQL Managed Instance med offentliga slutpunkter.](./public-endpoint-overview.md) Om du vill konfigurera en offentlig slutpunkt går [du till Konfigurera offentlig slutpunkt i SQL Managed Instance](public-endpoint-configure.md).

**Hur kontrollerar hanterad instans åtkomsten till den offentliga slutpunkten?**

Hanterad instans styr åtkomsten till den offentliga slutpunkten på både nätverksnivå och programnivå.

Hanterings- och distributionstjänster ansluter till en hanterad instans med hjälp av en [hanteringsslutpunkt](./connectivity-architecture-overview.md#management-endpoint) som mappar till en extern lastbalanserare. Trafiken dirigeras endast till noderna om den tas emot på en fördefinierad uppsättning portar som endast den hanterade instansens hanteringskomponenter använder. En inbyggd brandvägg på noderna konfigureras för att endast tillåta trafik från Microsofts IP-intervall. Certifikat autentiserar all kommunikation mellan hanteringskomponenter och hanteringsplanet. Mer information finns i [Anslutningsarkitektur för SQL Managed Instance](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kan jag använda den offentliga slutpunkten för att komma åt data i managed instance-databaser?**

Ja. Kunden måste aktivera offentlig slutpunktsdataåtkomst från [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShell/ARM och konfigurera NSG för att låsa åtkomsten till  /  [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) dataporten (portnummer 3342). Mer information finns i Konfigurera [offentlig slutpunkt i Azure SQL Managed Instance](public-endpoint-configure.md) och Använda Azure SQL Managed Instance säkert med offentlig [slutpunkt](public-endpoint-overview.md). 

**Kan jag ange en anpassad port för SQL-dataslutpunkter?**

Nej, det här alternativet är inte tillgängligt.  För privata dataslutpunkter använder Managed Instance standardportnummer 1433 och för offentliga dataslutpunkter använder hanterad instans standardportnummer 3342.

**Vilket är det rekommenderade sättet att ansluta hanterade instanser i olika regioner?**

Express Route-krets-peering är det bästa sättet att göra det på. Global peering för virtuella nätverk stöds med den begränsning som beskrivs i kommentaren nedan.  

> [!IMPORTANT]
> [2020-09-22 presenterade global peering för virtuella nätverk för nyligen skapade virtuella kluster](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Det innebär att global peering för virtuella nätverk stöds för SQL-hanterade instanser som skapats i tomma undernät efter meddelandedatumet samt för alla efterföljande hanterade instanser som skapats i dessa undernät. För alla andra SQL Managed Instances-peering är stödet begränsat till nätverken i samma region på grund av begränsningarna för global [peering för virtuella nätverk.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Se även relevant avsnitt i artikeln med [vanliga frågor och svar om virtuella Azure-nätverk](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

Om Express Route-krets-peering och global peering för virtuella nätverk inte är möjligt är det enda andra alternativet att skapa EN VPN-anslutning från plats till plats ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Minimera data exfiltreringsrisker  

**Hur kan jag minimera risker med data exfiltrering?**

För att minimera eventuella data exfiltreringsrisker rekommenderar vi att kunderna tillämpar en uppsättning säkerhetsinställningar och kontroller:

- Aktivera transparent datakryptering [(TDE)](../database/transparent-data-encryption-tde-overview.md) på alla databaser.
- Inaktivera CLR (Common Language Runtime). Detta rekommenderas även lokalt.
- Använd Azure Active Directory (Azure AD)-autentisering.
- Få åtkomst till instansen med ett lågbehörighetskonto för DBA.
- Konfigurera JIT-jumpboxåtkomst för sysadmin-kontot.
- Aktivera [SQL-granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)och integrera den med aviseringsmekanismer.
- Aktivera [Hotidentifiering](../database/threat-detection-configure.md) från Azure Defender [för SQL-sviten.](../database/azure-defender-for-sql.md)

## <a name="dns"></a>DNS

**Kan jag konfigurera en anpassad DNS för SQL Managed Instance?**

Ja. Se [Så här konfigurerar du en anpassad DNS för Azure SQL Managed Instance](./custom-dns-configure.md).

**Kan jag göra EN DNS-uppdatering?**

Ja. Se [Inställningen Synkronisera DNS-servrar för virtuellt nätverk SQL Managed Instance virtuella klustret.](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)

## <a name="change-time-zone"></a>Ändra tidszon

**Kan jag ändra tidszonen för en befintlig hanterad instans?**

Tidszonskonfiguration kan anges när en hanterad instans etableras för första gången. Det finns inte stöd för att ändra tidszonen för en befintlig hanterad instans. Mer information finns i [Tidszonsbegränsningar.](timezones-overview.md#limitations)

Lösningar omfattar att skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhetskopiering och återställning, eller vad vi rekommenderar, utföra en återställning till tidpunkt mellan [instanser.](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)


## <a name="security-and-database-encryption"></a>Säkerhet och databaskryptering

**Är serverrollen sysadmin tillgänglig för SQL Managed Instance?**

Ja, kunder kan skapa inloggningar som är medlemmar i sysadmin-rollen.  Kunder som antar sysadmin-behörigheten tar också ansvar för driften av instansen, vilket kan påverka SLA-åtagandet negativt. Information om hur du lägger till inloggning till sysadmin-serverrollen finns [i Azure AD-autentisering.](./aad-security-configure-tutorial.md#azure-ad-authentication)

**Stöds transparent datakryptering för SQL Managed Instance?**

Ja, transparent datakryptering stöds för SQL Managed Instance. Mer information finns i [transparent datakryptering för SQL Managed Instance](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Kan jag använda modellen "Bring Your Own Key" för TDE?**

Ja, Azure Key Vault BYOK-scenario är tillgängligt för Azure SQL Managed Instance. Mer information finns i [transparent datakryptering med kund hanterad nyckel.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

**Kan jag migrera en krypterad SQL Server databas?**

Ja, det kan du. Om du vill migrera en SQL Server-databas måste du exportera och importera dina befintliga certifikat till managed instance och sedan göra en fullständig säkerhetskopia av databasen och återställa den i Managed Instance. 

Du kan också använda [Azure Database Migration Service för](https://azure.microsoft.com/services/database-migration/) att migrera TDE-krypterade databaser.

**Hur konfigurerar jag rotation av TDE-skydd för SQL Managed Instance?**

Du kan rotera TDE-skydd för hanterad instans med Azure Cloud Shell. Instruktioner finns i transparent datakryptering [i SQL Managed Instance med din egen nyckel från Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Kan jag återställa min krypterade databas till SQL Managed Instance?**

Ja, du behöver inte dekryptera databasen för att återställa den till SQL Managed Instance. Du behöver ange ett certifikat/en nyckel som används som krypteringsnyckelskydd på källsystemet för att SQL Managed Instance ska kunna läsa data från den krypterade säkerhetskopian. Det finns två möjliga sätt att göra det:

- *Ladda upp certifikatskydd till SQL Managed Instance*. Det kan bara göras med Hjälp av PowerShell. [Exempelskriptet](./tde-certificate-migrate.md) beskriver hela processen.
- *Ladda upp asymmetriskt nyckelskydd för att Azure Key Vault och peka SQL Managed Instance till det*. Den här metoden liknar BYOK-användningsfallet (Bring Your Own Key) som även använder Key Vault för att lagra krypteringsnyckeln. Om du inte vill använda nyckeln som ett krypteringsnyckelskydd och bara vill göra nyckeln tillgänglig för SQL Managed Instance för att återställa krypterade databaser följer du anvisningarna för att konfigurera [BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)och markerar inte kryssrutan Gör den valda nyckeln till standardskydd mot **TDE.**

När du gör krypteringsskydd tillgängligt för SQL Managed Instance kan du fortsätta med standardproceduren för databasåterställning.

## <a name="purchasing-models-and-benefits"></a>Köpmodeller och fördelar

**Vilka inköpsmodeller är tillgängliga för SQL Managed Instance?**

SQL Managed Instance erbjuder [köpmodellen vCore-baserad](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Vilka kostnadsfördelar är tillgängliga för SQL Managed Instance?**

Du kan spara kostnader med Azure SQL fördelar på följande sätt:
-    Maximera befintliga investeringar i lokala licenser och spara upp till 55 procent med [Azure Hybrid-förmån](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-    Genomför en reservation för beräkningsresurser och spara upp till 33 procent med [Reserved Instance Benefit](../database/reserved-capacity-overview.md). Kombinera detta med Azure Hybrid-förmånen för besparingar på upp till 82 procent. 
-    Spara upp till 55 procent jämfört med listpriser med [Azure Dev/Test Pricing Benefit](https://azure.microsoft.com/pricing/dev-test/) som erbjuder rabatterade priser för dina pågående arbetsbelastningar för utveckling och testning.

**Vem är berättigad till reserverad instans-förmån?**

För att vara berättigad till reserverad instansförmån måste din prenumerationstyp vara ett Enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med betala per användning-priser (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). Mer information om reservationer finns i [Reserved Instance Benefit](../database/reserved-capacity-overview.md). 

**Går det att avbryta, byta ut eller återbetala reservationer?**

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fakturering för hanterad instans och lagring av säkerhetskopior

**Vilka är SQL Managed Instance prisalternativen?**

Information om prissättningsalternativen för Managed Instance finns [på sidan Med priser.](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

**Hur spårar jag faktureringskostnaden för min hanterade instans?**

Du kan göra det med hjälp [Azure Cost Management lösningen](../../cost-management-billing/index.yml). Gå till **Prenumerationer** i [Azure Portal](https://portal.azure.com) och välj **Kostnadsanalys.** 

Använd alternativet **Accumulated costs (Ackumulerade** kostnader) och filtrera sedan **efter Resurstyp** som `microsoft.sql/managedinstances` .

**Hur mycket kostar automatiserad säkerhetskopiering?**

Du får lika mycket ledigt lagringsutrymme för säkerhetskopiering som det köpta reserverade datalagringsutrymmet, oavsett vilken kvarhållningsperiod för säkerhetskopiering som har angetts. Om din förbrukning av lagringsutrymme för säkerhetskopiering ligger inom det allokerade kostnadsfria lagringsutrymmet för säkerhetskopiering har automatiserade säkerhetskopieringar på hanterad instans ingen extra kostnad för dig, och därför är det kostnadsfritt. Om du överskrider användningen av säkerhetskopieringslagring över det lediga utrymmet resulterar det i kostnader på cirka 0,20–0,24 USD per GB/månad i amerikanska regioner, eller så kan du se prissättningssidan för din region. Mer information finns i Förklaring [av lagringsförbrukning för säkerhetskopiering.](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Hur övervakar jag faktureringskostnaden för min förbrukning av säkerhetskopieringslagring?**

Du kan övervaka kostnaden för lagring av säkerhetskopior via Azure Portal. Anvisningar finns i [Övervaka kostnader för automatiserade säkerhetskopieringar.](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs) 

**Hur optimerar jag kostnaderna för lagring av säkerhetskopior på den hanterade instansen?**

Information om hur du optimerar kostnaderna för lagring av säkerhetskopior [finns i Finjustering av säkerhetskopiering SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Kostnadsbesparande användningsfall

**Var hittar jag användningsfall och resulterande kostnadsbesparingar med SQL Managed Instance?**

SQL Managed Instance fallstudier:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

För att få en bättre förståelse för fördelarna, kostnaderna och riskerna med att distribuera Azure SQL Managed Instance finns även en Forrester-studie: [The Total Economic Impact of Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Lösenordsprincip 

**Vilka lösenordsprinciper tillämpas för SQL Managed Instance SQL-inloggningar?**

SQL Managed Instance lösenordsprincip för SQL-inloggningar ärver Azure-plattformsprinciper som tillämpas på de virtuella datorer som bildar ett virtuellt kluster som innehar den hanterade instansen. För tillfället går det inte att ändra någon av de här inställningarna eftersom de här inställningarna definieras av Azure och ärvs av en hanterad instans.

 > [!IMPORTANT]
 > Azure-plattformen kan ändra principkrav utan att meddela tjänster som förlitar sig på dessa principer.

**Vad är aktuella principer för Azure-plattformen?**

Varje inloggning måste ange sitt lösenord vid inloggningen och ändra lösenordet när den har uppnått den högsta åldern.

| **Princip** | **Säkerhetsinställning** |
| --- | --- |
| Högsta ålder för lösenord | 42 dagar |
| Minsta lösenordsålder | 1 dag |
| Minsta längd på lösenord | 10 tecken |
| Lösenordet måste uppfylla komplexitetskraven | Enabled |

**Är det möjligt att inaktivera lösenordskomplexitet och förfallotid SQL Managed Instance på inloggningsnivå?**

Ja, det är möjligt att styra CHECK_POLICY och CHECK_EXPIRATION fält på inloggningsnivå. Du kan kontrollera aktuella inställningar genom att köra följande T-SQL-kommando:

```sql
SELECT *
FROM sys.sql_logins
```

Därefter kan du ändra angivna inloggningsinställningar genom att köra :

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(ersätt "test" med önskat inloggningsnamn och justera princip- och förfallovärden)


## <a name="service-updates"></a>Tjänstuppdateringar

**Vad är rot-CA-ändringen för Azure SQL Database & SQL Managed Instance?**

Se [Certifikatrotation för Azure SQL Database & SQL Managed Instance](../updates/ssl-root-certificate-expiring.md). 

**Vad är en planerad underhållshändelse för SQL Managed Instance?**

Se [Planera för Azure-underhållshändelser i SQL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Feedback och support för Azure

**Var kan jag lämna mina idéer för SQL Managed Instance förbättringar?**

Du kan rösta på en ny funktion för hanterad instans eller lägga till en ny förbättringsidé om röstning [i SQL Managed Instance Feedback Forum](https://feedback.azure.com/forums/915676-sql-managed-instance). På så sätt kan du bidra till produktutvecklingen och hjälpa oss att prioritera våra potentiella förbättringar.

**Hur skapar jag en Azure-supportbegäran?**

Information om hur du skapar en Azure-supportbegäran finns i [Så här skapar du en Azure-supportbegäran.](../../azure-portal/supportability/how-to-create-azure-support-request.md)
