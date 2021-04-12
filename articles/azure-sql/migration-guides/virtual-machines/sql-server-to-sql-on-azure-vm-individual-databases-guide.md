---
title: 'SQL Server SQL Server på Azure-Virtual Machines: migrations guide'
description: I den här guiden får du lära dig hur du migrerar dina enskilda SQL Server-databaser till SQL Server på Azure-Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550905"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Guide för migrering: SQL Server att SQL Server på Azure Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

I den här guiden får du lära dig hur du *identifierar* *, utvärderar och* *migrerar* användar databaser från SQL Server till en instans av SQL Server på Azure Virtual Machines med hjälp av säkerhets kopiering och återställning och logg överföring som använder [Data Migration Assistant](/sql/dma/dma-overview) för utvärdering.

Du kan migrera SQL Server som körs lokalt eller på:

- SQL Server på virtuella datorer (VM).
- Amazon Web Services (AWS) EC2.
- Amazon Relations databas tjänst (AWS RDS).
- Compute Engine (Google Cloud Platform [GCP]).

Mer information om ytterligare strategier för migrering finns i [Översikt över SQL Server VM migrering](sql-server-to-sql-on-azure-vm-migration-overview.md). Andra guider för migrering finns i hand boken för [Azure Database migration](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagram som visar ett process flöde för migrering.":::

## <a name="prerequisites"></a>Förutsättningar

Att migrera till SQL Server på Azure Virtual Machines kräver följande resurser:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Ett [Azure Migrate-projekt](../../../migrate/create-manage-projects.md).
- Ett för berett mål [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/create-sql-vm-portal.md) -instansen som är samma eller högre version än SQL Server källa.
- [Anslutning mellan Azure och lokalt](/azure/architecture/reference-architectures/hybrid-networking).
- [Välja en lämplig migrations strategi](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Före migrering

Innan du påbörjar migreringen måste du identifiera topologin för din SQL-miljö och bedöma möjligheten för din avsedda migrering.

### <a name="discover"></a>Identifiera

Azure Migrate bedömer att migreringen är lämplig för lokala datorer, utför prestandabaserade storleks ändringar och ger kostnads beräkningar för att köra lokalt. Om du vill planera för migreringen använder Azure Migrate för att [identifiera befintliga data källor och information om de funktioner](../../../migrate/concepts-assessment-calculation.md) som dina SQL Server-instanser använder. Den här processen innebär att genomsöka nätverket för att identifiera alla SQL Server instanser i din organisation med den version och de funktioner som används.

> [!IMPORTANT]
> När du väljer en virtuell Azure-dator för din SQL Server instans bör du tänka på [rikt linjerna för prestanda för SQL Server på Azure-Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Mer information om identifierings verktyg finns i avsnittet [tjänster och verktyg](../../../dms/dms-tools-matrix.md#business-justification-phase) som är tillgängliga för data migrations scenarier.

### <a name="assess"></a>Utvärdera

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

När du har identifierat alla data källor använder du [Data Migration Assistant](/sql/dma/dma-overview) för att utvärdera lokala SQL Server-instanser migrera till en instans av SQL Server på Azure Virtual Machines för att förstå luckorna mellan käll-och mål instanserna.

> [!NOTE]
> Om du _inte_ uppgraderar versionen av SQL Server hoppar du över det här steget och flyttar till avsnittet [migrera](#migrate) .

#### <a name="assess-user-databases"></a>Utvärdera användar databaser

Data Migration Assistant underlättar migreringen till en modern data plattform genom att identifiera kompatibilitetsproblem som kan påverka databas funktioner i den nya versionen av SQL Server. Data Migration Assistant rekommenderar förbättringar av prestanda och tillförlitlighet för mål miljön och du kan också flytta dina schema-, data-och inloggnings objekt från käll servern till mål servern.

Mer information finns i [utvärderingen](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>Beroende på typen av utvärdering kan de behörigheter som krävs på käll SQL Server vara olika:
   > - För *funktionen paritets* kontroll måste de autentiseringsuppgifter som angetts för att ansluta till käll SQL Server databasen vara medlem i Server rollen *sysadmin* .
   > - För klassificering av *kompatibilitetsproblem* måste de angivna autentiseringsuppgifterna ha minst `CONNECT SQL` , `VIEW SERVER STATE` -och- `VIEW ANY DEFINITION` behörighet.
   > - Data Migration Assistant markerar de behörigheter som krävs för den valda Advisor-tjänsten innan utvärderingen körs.

#### <a name="assess-the-applications"></a>Utvärdera programmen

Ett program lager får normalt åtkomst till användar databaser för att bevara och ändra data. Data Migration Assistant kan utvärdera data åtkomst nivån för ett program på två sätt:

- Genom att använda fångade [utökade händelser](/sql/relational-databases/extended-events/extended-events) eller [SQL Server profiler spår](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) av användar databaser. Du kan också använda [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) för att skapa en spårnings logg som också kan användas för a/B-testning.
- Med hjälp av [datamigreringshanteraren för data åtkomst (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), som tillhandahåller identifiering och utvärdering av SQL-frågor i koden och används för att migrera program käll kod från en databas plattform till en annan. Det här verktyget stöder populära filtyper som C#, Java, XML och oformaterad text. En guide om hur du utför en utvärdering av data åtkomst verktyg för migrering finns i blogg inlägget [använd data migration assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Under utvärderingen av användar databaser använder du Data Migration Assistant för att [Importera](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) fångade spårningsfiler eller filer för migrering av data åtkomst.

#### <a name="assessments-at-scale"></a>Utvärderingar i skala

Om du har flera servrar som kräver en Data Migration Assistant utvärdering kan du automatisera processen med hjälp av [kommando rads gränssnittet](/sql/dma/dma-commandline). Med hjälp av gränssnittet kan du förbereda bedömnings kommandon i förväg för varje SQL Server instans i omfånget för migrering.

För sammanfattande rapportering på stora fastigheter kan Data Migration Assistant utvärderingar nu samlas [in i Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Återfaktas databaser med Data Migration Assistant

Utifrån Data Migration Assistant utvärderings resultat kan du ha en serie rekommendationer för att se till att dina användar databaser fungerar och fungerar som de ska efter migreringen. Data Migration Assistant innehåller information om de påverkade objekten och resurserna för att lösa de olika problemen. Se till att lösa alla bryta ändringar och beteenden innan du påbörjar en produktions migrering.

För inaktuella funktioner kan du välja att köra användar databaser i sitt ursprungliga [kompatibilitetsläge](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) om du vill undvika att göra dessa ändringar och påskynda migreringen. Med den här åtgärden kan du inte [Uppgradera databasens kompatibilitet](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) förrän de inaktuella objekten har lösts.

Du måste skripta alla Data Migration Assistant korrigeringar och tillämpa dem på mål SQL Servers databasen under fasen [efter migreringen](#post-migration) .

> [!CAUTION]
> Alla SQL Server-versioner har inte stöd för alla kompatibilitetsläge. Kontrol lera att [mål SQL Server-versionen](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) har stöd för den valda databasens kompatibilitet. SQL Server 2019 stöder till exempel inte databaser med nivå 90-kompatibilitet (som är SQL Server 2005). Dessa databaser kräver minst en uppgradering till kompatibilitetsnivån 100.
>

## <a name="migrate"></a>Migrera

När du har slutfört stegen före migreringen är du redo att migrera användar databaser och-komponenter. Migrera dina databaser med hjälp av önskad [metod för migrering](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

I följande avsnitt beskrivs hur du utför en migrering med hjälp av säkerhets kopiering och återställning eller en minimal nertid-migrering med hjälp av säkerhets kopiering och återställning tillsammans med logg överföring.

### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Utföra en standardmigrering med hjälp av säkerhets kopiering och återställning:

1. Konfigurera anslutning till SQL Server på Azure Virtual Machines utifrån dina behov. Mer information finns i [ansluta till en virtuell SQL Server virtuell dator på Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Pausa eller stoppa alla program som använder databaser som är avsedda för migrering.
1. Se till att användar databaser är inaktiva genom att använda [enanvändarläge](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Utför en fullständig säkerhets kopiering av databasen till en lokal plats.
1. Kopiera dina lokala säkerhetskopierade filer till den virtuella datorn med hjälp av ett fjärr skrivbord, [Azure datautforskaren](/azure/data-explorer/data-explorer-overview)eller [kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Större än 2 TB säkerhets kopiering rekommenderas.)
1. Återställa fullständiga säkerhets kopior av databasen till SQL Server på Azure Virtual Machines.

### <a name="log-shipping-minimize-downtime"></a>Logg överföring (minimera stillestånds tid)

Så här utför du en minimal nedtid-migrering med hjälp av säkerhets kopiering och återställning och logg överföring:

1. Konfigurera anslutningen till SQL Server på Azure Virtual Machines utifrån dina behov. Mer information finns i [ansluta till en virtuell SQL Server virtuell dator på Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Se till att lokala användar databaser som ska migreras finns i fullständig eller Mass återställnings modell.
1. Utför en fullständig säkerhets kopiering av databasen till en lokal plats och ändra eventuella befintliga säkerhets kopierings jobb i hela databasen för att använda nyckelordet [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) för att bevara logg kedjan.
1. Kopiera dina lokala säkerhetskopierade filer till den virtuella datorn med hjälp av ett fjärr skrivbord, [Azure datautforskaren](/azure/data-explorer/data-explorer-overview)eller [kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy-v10.md). (Större än 1 TB säkerhets kopiering rekommenderas.)
1. Återställa fullständiga säkerhets kopior av databasen på SQL Server på Azure Virtual Machines.
1. Konfigurera [logg överföring](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) mellan den lokala databasen och SQL Server på Azure Virtual Machines. Se till att inte initiera databaserna på grund av att den här uppgiften redan har slutförts i föregående steg.
1. Klipp ut över till mål servern.
   1. Pausa eller stoppa program med hjälp av databaser som ska migreras.
   1. Se till att användar databaser är inaktiva genom att använda [enanvändarläge](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. När du är klar kan du utföra en kontrollerad logg överföring av en [kontrollerad redundansväxling](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) av lokala databaser till SQL Server på Azure Virtual Machines.

### <a name="migrate-objects-outside-user-databases"></a>Migrera objekt utanför användar databaser

Fler SQL Server objekt kan krävas för den sömlösa driften av användar databaserna efter migreringen.

Följande tabell innehåller en lista över komponenter och rekommenderade metoder för migrering som kan slutföras före eller efter migreringen av användar databaserna.

| **Funktion** | **Komponent** | **Metoder för migrering** |
| --- | --- | --- |
| **Databaser** | Modell | Skript med SQL Server Management Studio. |
|| TempDB | Planera för att flytta tempDB till [Azure VM Temporary disk (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)för bästa prestanda. Se till att välja en VM-storlek som har en tillräckligt lokal SSD för att kunna hantera din tempDB. |
|| Användar databaser med FileStream | Använd [säkerhets kopierings-och återställnings](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) metoder för migrering. Data Migration Assistant stöder inte databaser med FileStream. |
| **Säkerhet** | SQL Server-och Windows-inloggningar | Använd Data Migration Assistant för att [migrera användar inloggningar](/sql/dma/dma-migrateserverlogins). |
|| SQL Server roller | Skript med SQL Server Management Studio. |
|| Kryptografiproviders | Rekommendera [att konvertera för att använda Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). I den här proceduren används [resurs leverantören för SQL-VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Server objekt** | Säkerhetskopiera enheter | Ersätt med säkerhets kopia av databasen med hjälp av [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)eller Skriv säkerhets kopior till [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). I den här proceduren används [resurs leverantören för SQL-VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Länkade servrar | Skript med SQL Server Management Studio. |
|| Server utlösare | Skript med SQL Server Management Studio. |
| **Replikering** | Lokala publikationer | Skript med SQL Server Management Studio. |
|| Lokala prenumeranter | Skript med SQL Server Management Studio. |
| **PolyBase** | PolyBase | Skript med SQL Server Management Studio. |
| **Hantering** | Database-mail | Skript med SQL Server Management Studio. |
| **SQL Server Agent** | Jobb | Skript med SQL Server Management Studio. |
|| Aviseringar | Skript med SQL Server Management Studio. |
|| Operatorer | Skript med SQL Server Management Studio. |
|| Proxy | Skript med SQL Server Management Studio. |
| **Operativsystem** | Filer, fil resurser | Anteckna alla andra filer eller fil resurser som används av dina SQL-servrar och replikeras på Azure Virtual Machines-målet. |

## <a name="post-migration"></a>Efter migreringen

När du har slutfört migreringen måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Att utföra den här uppgiften kan kräva ändringar i programmen i vissa fall.

Använd eventuella korrigeringar som rekommenderas av Data Migration Assistant i användar databaser. Du måste skriva över de här korrigeringarna för att säkerställa konsekvens och möjliggöra automatisering.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Skapa validerings frågor som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
1. **Konfigurera en test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester**: kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

> [!TIP]
> Använd [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) för att hjälpa till att utvärdera mål SQL Server prestanda.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att stämma av data precisions problem, kontrol lera att de är klara och åtgärda potentiella prestanda problem med arbets belastningen.

Mer information om de här problemen och stegen för att minimera dem finns i:

- [Guide för validering och optimering efter migreringen](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Justera prestanda i virtuella Azure SQL-datorer](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Kostnads optimerings Center för Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Nästa steg

- För att kontrol lera tillgängligheten för tjänster som gäller för SQL Server, se [Azures globala infrastruktur Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- En matris med tjänster och verktyg från Microsoft och tjänster från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).
- Mer information om Azure SQL finns i:
   - [Distributionsalternativ](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Räknare för total ägande kostnad (TCO) för Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   - [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Information om licensiering finns i:
   - [Bring your own license med Azure Hybrid-förmån](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Få kostnads fri utökad support för SQL Server 2008 och SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Information om hur du utför ett/B-test för data åtkomst lagret finns i [Översikt över Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
