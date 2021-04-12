---
title: 'SQL Server till Azure SQL Database: migration guide'
description: Följ den här guiden för att migrera SQL Server-databaser till Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: 6ce7de649876ed8ccff1254f25a7213b407dacae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284093"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>Guide för migrering: SQL Server till Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig [hur du migrerar](https://azure.microsoft.com/migration/migration-journey) din SQL Server-instans till Azure SQL Database. 

Du kan migrera SQL Server som körs lokalt eller på: 

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)  
- Cloud SQL för SQL Server (Google Cloud Platform – GCP) 

Mer information om migrering finns i [Översikt över migrering](sql-server-to-sql-database-overview.md). Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Process flöde för migrering":::

## <a name="prerequisites"></a>Förutsättningar 

Kontrol lera att du har följande förutsättningar för att [SQL Server migreringen](https://azure.microsoft.com/en-us/migration/sql-server/) till Azure SQL Database: 

- En vald [metod för migrering](sql-server-to-sql-database-overview.md#compare-migration-options) och motsvarande verktyg.
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installerat på en dator som kan ansluta till din käll SQL Server.
- Ett mål [Azure SQL Database](../../database/single-database-create-quickstart.md). 
- Anslutning och rätt behörigheter för åtkomst till både källa och mål. 



## <a name="pre-migration"></a>Före migrering

När du har kontrollerat att käll miljön stöds börjar du med steget före migreringen. Identifiera alla befintliga data källor, utvärdera möjligheterna för migrering och identifiera eventuella spärrnings problem som kan förhindra din [Azure Cloud-migrering](https://azure.microsoft.com/migration).

### <a name="discover"></a>Identifiera

I identifierings fasen genomsöker du nätverket för att identifiera alla SQL Server instanser och funktioner som används av din organisation. 

Använd [Azure Migrate](../../../migrate/migrate-services-overview.md) för att bedöma lämplighet för migrering av lokala servrar, utföra prestandabaserade storleks ändringar och tillhandahålla kostnads uppskattningar för att köra dem i Azure. 

Du kan också använda [Microsoft Assessment and Planning Toolkit ("kart verktyg")](https://www.microsoft.com/download/details.aspx?id=7826) för att utvärdera din aktuella IT-infrastruktur. Verktyget är ett kraftfullt verktyg för inventering, utvärdering och rapportering för att förenkla planerings processen för migrering. 

Mer information om vilka verktyg som är tillgängliga för identifierings fasen finns i [tjänster och verktyg som är tillgängliga för data migrations scenarier](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Utvärdera 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

När data källorna har identifierats kan du utvärdera eventuella lokala SQL Server-databaser som kan migreras till Azure SQL Database för att identifiera migrations-eller kompatibilitetsproblem. 

Du kan använda Data Migration Assistant (version 4,1 och senare) för att utvärdera databaser för att få: 

- [Rekommendationer för Azure-mål](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Rekommendationer för Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Följ dessa steg om du vill utvärdera din miljö med hjälp av Database migration-utvärderingen: 

1. Öppna [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Välj **fil** och välj sedan **ny utvärdering**. 
1. Ange ett projekt namn, välj SQL Server som käll Server typ och välj sedan Azure SQL Database som mål server typ. 
1. Välj typ (er) av utvärderings rapporter som du vill generera. Till exempel databasens kompatibilitet och funktions paritet. Beroende på typen av utvärdering kan de behörigheter som krävs på käll SQL Server vara olika.  DMA kommer att markera de behörigheter som krävs för den valda Advisor-tjänsten innan du kör utvärderingen.
    - Kategorin **funktion paritet** innehåller en omfattande uppsättning rekommendationer, alternativ som är tillgängliga i Azure och åtgärder som hjälper dig att planera ditt migreringsjobb. (sysadmin-behörigheter krävs)
    - Kategorin **kompatibilitetsproblem** identifierar delvis stödda kompatibilitetsproblem som kan blockera migrering samt rekommendationer för att åtgärda dem ( `CONNECT SQL` , `VIEW SERVER STATE` och de behörigheter som `VIEW ANY DEFINITION` krävs).
1. Ange käll anslutnings information för din SQL Server och Anslut till käll databasen.
1. Välj **Starta utvärdering**. 
1. När processen har slutförts väljer du och granskar utvärderings rapporterna för att blockera och funktions paritets problem. Utvärderings rapporten kan också exporteras till en fil som kan delas med andra team eller personal i din organisation. 
1. Bestäm vilken kompatibilitetsnivå för databaser som minimerar efter migreringen.  
1. Identifiera den bästa Azure SQL Database SKU: n för din lokala arbets belastning. 

Mer information finns i [utföra en utvärdering av SQL Server migrering med data migration assistant](/sql/dma/dma-assesssqlonprem).

Om utvärderingen påträffar flera blockerare för att bekräfta att databasen inte är klar för en Azure SQL Database migrering, kan du överväga att:

- [Azure SQL-hanterad instans](../managed-instance/sql-server-to-managed-instance-overview.md) om det finns flera instanser av instans omfång
- [SQL Server på Azure Virtual Machines](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) om både SQL Database-och SQL-hanterade instanser inte kan vara lämpliga mål. 



#### <a name="scaled-assessments-and-analysis"></a>Skalade utvärderingar och analyser
Data Migration Assistant har stöd för att utföra skalnings bedömning och konsolidering av utvärderings rapporterna för analys. 

Om du har flera servrar och databaser som måste utvärderas och analyseras i stor skala för att tillhandahålla en bredare vy av datafastighetsen kan du läsa mer i följande länkar:

- [Utföra skalnings bedömning med PowerShell](/sql/dma/dma-consolidatereports)
- [Analysera utvärderings rapporter med hjälp av Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Att köra utvärderingar i skala för flera databaser, särskilt stora, kan också automatiseras med hjälp av [kommando rads verktyget DMA](/sql/dma/dma-commandline) och laddas upp till [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) för ytterligare analys och mål beredskap.

## <a name="migrate"></a>Migrera

När du har slutfört uppgifter som är kopplade till fasen före migrering är du redo att utföra schemat och datamigreringen. 

Migrera dina data med din valda [metod för migrering](sql-server-to-sql-database-overview.md#compare-migration-options). 

Den här guiden beskriver de två mest populära alternativen-Data Migration Assistant och Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Följ dessa steg om du vill migrera en databas från SQL Server till Azure SQL Database med hjälp av DMA: 

1. Hämta och installera [databasen Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
1. Skapa ett nytt projekt och välj **migrering** som projekt typ.
1. Ange käll Server typen till **SQL Server** och den mål server typ som ska **Azure SQL Database**, Välj migrerings omfånget som **schema och data** och välj **skapa**.
1. I migreringsjobbet anger du käll Server information, till exempel Server namn, autentiseringsuppgifter för att ansluta till servern och käll databasen som ska migreras.
1. I informationen om mål servern anger du Azure SQL Database Server namnet och autentiseringsuppgifterna för att ansluta till servern och mål databasen som ska migreras till.
1. Välj schema objekt och distribuera dem till mål Azure SQL Database.
1. Välj slutligen **Starta datamigrering** och övervaka förloppet för migreringen.

En detaljerad själv studie kurs finns i [migrera lokala SQL Server eller SQL Server på virtuella Azure-datorer för att Azure SQL Database använda data migration assistant](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Skala databasen till en högre tjänst nivå och beräknings storlek under import processen för att maximera import hastigheten genom att tillhandahålla fler resurser. Du kan sedan skala ned när importen har slutförts.</br>
> - Kompatibilitetsnivån för den importerade databasen baseras på din käll Databass kompatibilitetsnivå. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Följ stegen nedan om du vill migrera databaser från SQL Server till Azure SQL Database med DMS:

1. Registrera **Microsoft. data migration** -resurs leverantören i din prenumeration om du inte redan gjort det. 
1. Skapa en Azure Database Migration Service-instans på önskad plats (helst i samma region som mål Azure SQL Database). Välj ett befintligt virtuellt nätverk eller skapa ett nytt som värd för DMS-instansen.
1. När DMS-instansen har skapats skapar du ett nytt migreringsjobb och anger käll Server typen **SQL Server** och mål server typen som **Azure SQL Database**. Välj **migrering av data offline** som aktivitets typ i bladet för att skapa migreringsjobb.
1. Ange käll SQL Server information på informations sidan för **migreringen** och mål Azure SQL Database information på informations sidan för **migreringen** .
1. Mappa käll-och mål databaserna för migrering och välj sedan de tabeller som du vill migrera.
1. Granska migrerings översikten och välj **Kör migrering**. Du kan sedan övervaka migrerings aktiviteten och kontrol lera förloppet för migreringen av databasen.

En detaljerad själv studie kurs finns i [migrera SQL Server till en Azure SQL Database med DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Data synkronisering och start punkt

När du använder migreringsåtgärder som kontinuerligt replikerar/synkroniserar data ändringar från källan till målet, kan källdata och schemat ändras och riktas mot målet. Under datasynkroniseringen ska du se till att alla ändringar på källan fångas och tillämpas på målet under migreringsprocessen. 

När du har kontrollerat att data är samma på både källan och målet kan du Start punkt från källan till mål miljön. Det är viktigt att planera start punkt-processen med affärs-/program grupper för att säkerställa minimala avbrott under start punkt inte påverkar affärs kontinuiteten. 

> [!IMPORTANT]
> Mer information om de steg som är associerade med att utföra en start punkt som en del av migreringar med hjälp av DMS finns i [utföra migrering start punkt](../../../dms/tutorial-sql-server-to-azure-sql.md).

## <a name="migration-recommendations"></a>Rekommendationer för migrering

För att påskynda migreringen till Azure SQL Database bör du tänka på följande rekommendationer:

|  | Resurs konkurrens | Rekommendation |
|--|--|--|
| **Källa (vanligt vis lokalt)** |Primär Flask hals under migreringen i källan är DATA I/O och svars tid på DATA filen som måste övervakas noggrant.  |Baserat på DATA-i/o-och DATA fils svars tid och beroende på om det är en virtuell dator eller fysisk server måste du engagera lagrings administratör och utforska alternativ för att minska Flask halsen. |
|**Mål (Azure SQL Database)**|Största begränsnings faktor är loggens skapande frekvens och svars tid för logg filen. Med Azure SQL Database kan du få maximalt 96 MB/s logg skapande hastighet. | För att påskynda migreringen kan du skala upp målets SQL-databas till Affärskritisk Gen5 8-vCore för att få den maximala logg skapande frekvensen på 96 MB/s och även uppnå en låg latens för logg filen. Den [storskaliga](../../database/service-tier-hyperscale.md) tjänst nivån ger 100 MB/s logg taxa oavsett vilken service nivå som valts |
|**Nätverk** |Den nödvändiga nätverks bandbredden är lika med max antalet logg inmatningar 96 MB/s (768 MB/s) |Beroende på nätverks anslutning från ditt lokala data Center till Azure, kontrol lera nätverks bandbredden (vanligt vis [Azure-ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)) för att passa den maximala logg inmatnings hastigheten. |
|**Virtuell dator som används för Data Migration Assistant (DMA)** |CPU är den primära Flask halsen för den virtuella dator som kör DMA |Saker att tänka på för att påskynda migreringen av data med hjälp av </br>– Azure Compute-intensiva virtuella datorer </br>-Använd minst F8s_v2 (8 vCore) virtuell dator för att köra DMA </br>– Kontrol lera att den virtuella datorn körs i samma Azure-region som mål |
|**Azure Database Migration Service (DMS)** |Överväganden för beräkning av resurs konkurrens och databas objekt för DMS |Använd Premium 4-vCore. DMS tar automatiskt hand om databas objekt som externa nycklar, utlösare, begränsningar och icke-grupperade index och behöver inte ha någon manuell åtgärd.  |


## <a name="post-migration"></a>Efter migreringen

När du har slutfört migreringen går du igenom en serie uppgifter efter migreringen för att se till att allt fungerar smidigt och effektivt. 

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen. 

### <a name="remediate-applications"></a>Åtgärda program 

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. I vissa fall behöver du inte göra några ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
1. **Konfigurera test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester**: kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.


## <a name="leverage-advanced-features"></a>Utnyttja avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktioner som erbjuds av SQL Database, till exempel [inbyggd hög tillgänglighet](../../database/high-availability-sla.md), [hot identifiering](../../database/azure-defender-for-sql.md)och [övervakning och justering av arbets belastningen](../../database/monitor-tune-overview.md). 

Vissa SQL Server funktioner är bara tillgängliga när kompatibilitetsnivån för [databas](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) har ändrats till den senaste kompatibilitetsnivån (150). 

Läs mer i [hantera Azure SQL Database efter migreringen](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Nästa steg

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).


- Mer information om [Azure Migrate](https://azure.microsoft.com/services/azure-migrate) finns i
   - [Azure Migrate](../../../migrate/migrate-services-overview.md)

- Mer information om SQL Database finns i:
    - [En översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
