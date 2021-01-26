---
title: SQL Server SQL Server på virtuella Azure-datorer (migreringsguiden)
description: Följ den här guiden för att migrera dina enskilda SQL Server-databaser till SQL Server på Azure-Virtual Machines (VM).
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: cc2a641cb017edace24db5df69bc4adf3a607524
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797875"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Guide för migrering: SQL Server till SQL Server på virtuella Azure-datorer 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Den här migreringsguiden lär dig att **identifiera**, **utvärdera** och **migrera** dina användar databaser från SQL Server till en SQL Server på Azure-Virtual Machines (VM) med hjälp av säkerhets kopierings-och återställnings-och logg leverans som använder [databasen Migration Assistant (DMA)](/sql/dma/dma-overview) för utvärdering. 

Du kan migrera SQL Server som körs lokalt eller på:

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)

Mer information om ytterligare migrerings strategier finns i [Översikt över SQL Server VM migrering](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Process flöde för migrering":::

## <a name="prerequisites"></a>Förutsättningar

Att migrera till SQL Server på virtuella Azure-datorer kräver följande: 

- [Databas Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- Ett [Azure Migrate-projekt](../../../migrate/create-manage-projects.md).
- En för beredd mål [SQL Server på en virtuell Azure-dator](../../virtual-machines/windows/create-sql-vm-portal.md) som är samma eller större än käll SQL Server.
- [Anslutning mellan Azure och lokalt](/azure/architecture/reference-architectures/hybrid-networking).
- [Välja en lämplig migrations strategi](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Före migrering

Innan du påbörjar migreringen ska du identifiera topologin för din SQL-miljö och bedöma möjligheten för din avsedda migrering. 

### <a name="discover"></a>Identifiera

Azure Migrate bedömer att migreringen är lämplig för lokala datorer, utför prestandabaserade storleks ändringar och ger kostnads beräkningar för att köra lokalt. Om du vill planera för migreringen använder Azure Migrate för att [identifiera befintliga data källor och information om de funktioner](../../../migrate/concepts-assessment-calculation.md) som dina SQL Server-instanser använder. Den här processen innebär att genomsöka nätverket för att identifiera alla SQL Server instanser i din organisation med den version och de funktioner som används. 

> [!IMPORTANT]
> När du väljer en virtuell Azure-dator för din SQL Server instans bör du tänka på [rikt linjerna för prestanda för SQL Server på virtuella Azure-datorer](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Ytterligare identifierings verktyg finns i [tjänster och verktyg](../../../dms/dms-tools-matrix.md#business-justification-phase) som är tillgängliga för data migrations scenarier.


### <a name="assess"></a>Utvärdera

När du har identifierat alla data källor använder du [Data Migration Assistant (DMA)](/sql/dma/dma-overview) för att utvärdera lokala SQL Server-instanser migreras till en instans av SQL Server på den virtuella Azure-datorn för att förstå luckorna mellan käll-och mål instanserna. 


> [!NOTE]
> Om du _inte_ uppgraderar versionen av SQL Server hoppar du över det här steget och flyttar till avsnittet [migrera](#migrate) . 


#### <a name="assess-user-databases"></a>Utvärdera användar databaser 

Data Migration Assistant (DMA) underlättar migreringen till en modern data plattform genom att identifiera kompatibilitetsproblem som kan påverka databas funktioner i den nya versionen av SQL Server. DMA rekommenderar förbättringar av prestanda och tillförlitlighet för mål miljön och du kan också flytta dina schema-, data-och inloggnings objekt från käll servern till mål servern.

Mer information finns i [utvärderingen](/sql/dma/dma-migrateonpremsql) . 


> [!IMPORTANT]
>Beroende på typen av utvärdering kan de behörigheter som krävs på käll SQL Server vara olika. 
   > - För **funktionen paritets** kontroll måste de autentiseringsuppgifter som angetts för att ansluta till käll SQL Server databasen vara medlem i Server rollen *sysadmin* .
   > - För klassificering av kompatibilitetsproblem måste de angivna autentiseringsuppgifterna ha minst `CONNECT SQL` `VIEW SERVER STATE` och `VIEW ANY DEFINITION` behörigheter.
   > - DMA kommer att markera de behörigheter som krävs för den valda Advisor-tjänsten innan du kör utvärderingen.


#### <a name="assess-applications"></a>Utvärdera program

Ett program lager får normalt åtkomst till användar databaser för att bevara och ändra data.  DMA kan utvärdera data åtkomst nivån för ett program på två sätt: 

- Använda fångade [utökade händelser](/sql/relational-databases/extended-events/extended-events) eller [SQL Server profiler spår ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) av användar databaser. Du kan också använda [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) för att skapa en spårnings logg som också kan användas för a/B-testning.

- Med hjälp av Datamigreringshanteraren för [data åtkomst (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), som tillhandahåller identifiering och utvärdering av SQL-frågor i koden och används för att migrera program käll kod från en databas plattform till en annan. Det här verktyget stöder många populära filtyper, inklusive C# och Java, XML och oformaterad text. En guide om hur du utför en DAMT-utvärdering finns i bloggen [Använd DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Använd DMA för att [Importera](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) fångade SPÅRNINGSFILER eller DAMT-filer under utvärderingen av användar databaser. 


#### <a name="scale-assessments"></a>Skalnings bedömningar

Om du har flera servrar som kräver en DMA-utvärdering kan du automatisera processen via [kommando rads gränssnittet](/sql/dma/dma-commandline). Med hjälp av gränssnittet kan du förbereda bedömnings kommandon i förväg för varje SQL Server instans i omfånget för migrering. 

För sammanfattande rapporter över stora fastigheter kan Data Migration Assistant (DMA) nu [samlas in i Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Återfaktas databaser med DMA

Utifrån resultaten av DMA-utvärderingen kan du ha en serie rekommendationer för att se till att dina användar databaser fungerar och fungerar som de ska efter migreringen. DMA innehåller information om de påverkade objekten samt resurser för att lösa de olika problemen. Vi rekommenderar att alla avbrytande ändringar och beteende ändringar löses innan du migrerar en produktion.

För inaktuella funktioner kan du välja att köra dina användar databaser i sitt ursprungliga [kompatibilitetsläge](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) om du vill undvika att göra dessa ändringar och påskynda migreringen. Detta förhindrar dock att du [uppgraderar din Databass kompatibilitet](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) tills de inaktuella objekten har lösts.

Vi rekommenderar starkt att alla DMA-korrigeringsfiler har skript och applicerats på mål SQL Server databasen under [efter migreringen](#post-migration).

> [!CAUTION]
> Alla SQL Server-versioner har inte stöd för alla kompatibilitetsläge. Kontrol lera att [mål SQL Server-versionen](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) har stöd för den valda databasens kompatibilitet. SQL Server 2019 stöder till exempel inte databaser med nivå 90-kompatibilitet (som är SQL Server 2005). Dessa databaser kräver minst en uppgradering till kompatibilitetsnivån 100.
>

## <a name="migrate"></a>Migrera

När du har slutfört stegen före migreringen är du redo att migrera användar databaser och-komponenter. Migrera dina databaser med hjälp av önskad [metod för migrering](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).  

Följande steg beskriver hur du utför en migrering med hjälp av säkerhets kopiering och återställning, eller en minimal nedtid-migrering med hjälp av säkerhets kopiering och återställning tillsammans med logg överföring. 

### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Följ dessa steg om du vill utföra en standardmigrering med säkerhets kopiering och återställning: 

1. Konfigurera anslutningen till mål SQL Server på den virtuella Azure-datorn, baserat på dina krav. Se [ansluta till en SQL Server virtuell dator på Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Pausa/stoppa alla program som använder databaser som är avsedda för migrering. 
1. Se till att användar databaserna är inaktiva med [ett enda användarläge](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Utför en fullständig säkerhets kopiering av databasen till en lokal plats.
1. Kopiera dina lokala säkerhets kopierings filer till den virtuella datorn med hjälp av fjärr skrivbord, [Azure datautforskaren](/azure/data-explorer/data-explorer-overview)eller [kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (> 2 TB säkerhets kopiering rekommenderas).
1. Återställ fullständiga säkerhets kopieringar av databasen till SQL Server på den virtuella Azure-datorn.

### <a name="log-shipping--minimize-downtime"></a>Logg överföring (minimera stillestånds tid)

Följ dessa steg för att utföra en minimal nedtid-migrering med säkerhets kopiering, återställning och logg överföring: 

1. Konfigurera anslutningen till att rikta SQL Server på virtuella Azure-datorer, baserat på dina krav. Se [ansluta till en SQL Server virtuell dator på Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Se till att lokala användar databaser som ska migreras finns i fullständig eller Mass återställnings modell.
1. Utför en fullständig säkerhets kopiering av databasen till en lokal plats och ändra eventuella befintliga säkerhets kopierings jobb som ska användas [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) nyckelord för att bevara logg kedjan.
1. Kopiera dina lokala säkerhets kopierings filer till den virtuella datorn med hjälp av fjärr skrivbord, [Azure datautforskaren](/azure/data-explorer/data-explorer-overview)eller [kommando rads verktyget AzCopy](../../../storage/common/storage-use-azcopy-v10.md) (>1 TB säkerhets kopiering rekommenderas).
1. Återställ fullständiga säkerhets kopieringar av databaser på SQL Server på den virtuella Azure-datorn.
1. Konfigurera [logg överföring](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) mellan lokala databaser och mål SQL Server på den virtuella Azure-datorn. Se till att du inte initierar om databaserna eftersom det redan har slutförts i föregående steg.
1. **Klipp ut över** till mål servern. 
   1. Pausa/stoppa program som använder databaser som ska migreras. 
   1. Se till att användar databaserna är inaktiva med [ett enda användarläge](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. När du är klar kan du utföra en kontrollerad logg överföring med [kontrollerad redundans](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) av lokala databaser till mål SQL Server på den virtuella Azure-datorn.



### <a name="migrating-objects-outside-user-databases"></a>Migrera objekt utanför användar databas (er)

Det kan finnas ytterligare SQL Server objekt som krävs för den sömlösa driften av användar databaserna efter migreringen. 

Följande tabell innehåller en lista över komponenter och rekommenderade metoder för migrering som kan slutföras före eller efter migreringen av användar databaserna: 


| **Funktion** | **Komponent** | **Migrations metod (er)** |
| --- | --- | --- |
| **Databaser** | Modell  | Skript med SQL Server Management Studio |
|| TempDB | Planera för att flytta TempDB till [Azure VM Temporary disk (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) för bästa prestanda. Se till att välja en VM-storlek som har en tillräckligt lokal SSD för att kunna hantera din TempDB. |
|| Användar databaser med FILESTREAM |  Använd [säkerhets kopierings-och återställnings](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) metoder för migrering. DMA stöder inte databaser med FILESTREAM. |
| **Säkerhet** | SQL Server-och Windows-inloggningar | Använd DMA för att [migrera användar inloggningar](/sql/dma/dma-migrateserverlogins). |
|| SQL Server roller | Skript med SQL Server Management Studio |
|| Kryptografiproviders | Rekommendera [att konvertera för att använda Azure Key Vault-tjänsten](../../virtual-machines/windows/azure-key-vault-integration-configure.md). I den här proceduren används [resurs leverantören för SQL-VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Server objekt** | Säkerhetskopiera enheter | Ersätt med säkerhets kopia av databasen med hjälp av [Azure Backup tjänst](../../../backup/backup-sql-server-database-azure-vms.md) eller Skriv säkerhets kopior till [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). I den här proceduren används [resurs leverantören för SQL-VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Länkade servrar | Skript med SQL Server Management Studio. |
|| Server utlösare | Skript med SQL Server Management Studio. |
| **Replikering** | Lokala publikationer | Skript med SQL Server Management Studio. |
|| Lokala prenumeranter | Skript med SQL Server Management Studio. |
| **PolyBase** | PolyBase | Skript med SQL Server Management Studio. |
| **Hantering** | Database Mail | Skript med SQL Server Management Studio. |
| **SQL Server Agent** | Jobb | Skript med SQL Server Management Studio. |
|| Aviseringar | Skript med SQL Server Management Studio. |
|| Operatorer | Skript med SQL Server Management Studio. |
|| Proxy | Skript med SQL Server Management Studio. |
| **Operativsystem** | Filer, fil resurser | Anteckna eventuella ytterligare filer eller fil resurser som används av dina SQL-servrar och replikeras på det virtuella Azure-målet. |


## <a name="post-migration"></a>Efter migreringen

När du har slutfört migreringen går du igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. I vissa fall kan det krävas ändringar i programmen.

Använd eventuella databas Migration Assistant rekommenderade korrigeringar till användar databaserna. Vi rekommenderar att du använder skript för att säkerställa konsekvens och för att möjliggöra automatisering.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av att utföra följande aktiviteter:

1. **Utveckla verifieringstester.**  Använd SQL-frågor för att testa migrering av databasen. Skapa validerings frågor som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
2. **Konfigurera test miljö.**  Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
3. **Kör verifierings test.**  Kör verifierings testen mot källan och målet och analysera sedan resultaten.
4. **Kör prestandatester.**  Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

> [!TIP]
> Använd [Database experimentation Assistant (dietanolamin)](/sql/dea/database-experimentation-assistant-overview) för att hjälpa till med att utvärdera mål SQL Server prestanda.
>

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att stämma av eventuella problem med data precision och fullständighet, samt för att lösa potentiella prestanda problem med arbets belastningen.

Mer information om de här problemen och specifika steg för att minimera dem finns i följande resurser:

- [Guide för validering och optimering efter migreringen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Justera prestanda i Azure SQL Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Azure kostnads optimerings Center](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Nästa steg

- För att kontrol lera tillgängligheten för tjänster som är tillämpliga på SQL Server, se [Azures globala infrastruktur Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om Azure SQL finns i:
   - [Distributionsalternativ](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server på virtuella Azure-datorer](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om licensiering finns i
   - [Bring your own license med Azure Hybrid-förmån](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Få kostnads fri utökad support för SQL Server 2008 och SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).