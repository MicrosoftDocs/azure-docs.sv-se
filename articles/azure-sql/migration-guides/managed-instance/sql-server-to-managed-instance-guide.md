---
title: 'SQL Server till SQL-hanterad instans: migreringsguiden'
description: Följ den här guiden för att migrera SQL Server-databaser till Azure SQL-hanterad instans.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: ac8b0e0c2cdbd46626677f4be0f78800d839ad28
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358902"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Migration guide: SQL Server till SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Den här guiden hjälper dig att migrera din SQL Server-instans till en Azure SQL-hanterad instans. 

Du kan migrera SQL Server som körs lokalt eller på: 

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)  
- Cloud SQL för SQL Server (Google Cloud Platform – GCP) 

Mer information om migrering finns i [Översikt över migrering](sql-server-to-managed-instance-overview.md). För andra scenarier, se [Guide för databas migrering](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Process flöde för migrering":::

## <a name="prerequisites"></a>Förutsättningar 

Om du vill migrera din SQL Server till en Azure SQL-hanterad instans, se till att gå igenom följande krav: 

- Välj en [metod för migrering](sql-server-to-managed-instance-overview.md#compare-migration-options) och motsvarande verktyg som krävs för den valda metoden
- Installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) på en dator som kan ansluta till din käll SQL Server


## <a name="pre-migration"></a>Före migrering

När du har kontrollerat att käll miljön stöds börjar du med steget före migreringen. Identifiera alla befintliga data källor, utvärdera möjligheterna för migrering och identifiera eventuella spärrnings problem som kan förhindra migreringen.  

### <a name="discover"></a>Identifiera

I identifierings fasen genomsöker du nätverket för att identifiera alla SQL Server instanser och funktioner som används av din organisation. 

Använd [Azure Migrate](../../../migrate/migrate-services-overview.md) för att utvärdera migreringens lämplighet för lokala servrar, utföra prestandabaserade storleks ändringar och tillhandahålla kostnads uppskattningar för att köra dem i Azure. 

Du kan också använda [Microsoft Assessment and Planning Toolkit ("kart verktyg")](https://www.microsoft.com/download/details.aspx?id=7826) för att utvärdera din aktuella IT-infrastruktur. Verktyget är ett kraftfullt verktyg för inventering, utvärdering och rapportering för att förenkla planerings processen för migrering. 

Mer information om vilka verktyg som är tillgängliga för identifierings fasen finns i [tjänster och verktyg som är tillgängliga för data migrations scenarier](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Utvärdera 

När data källorna har identifierats kan du utvärdera eventuella lokala SQL Server-instanser som kan migreras till den hanterade Azure SQL-instansen för att identifiera migreringsåtgärder eller kompatibilitetsproblem. 

Du kan använda Data Migration Assistant (version 4,1 och senare) för att utvärdera databaser för att få: 

- [Rekommendationer för Azure-mål](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Rekommendationer för Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Följ dessa steg om du vill utvärdera din miljö med hjälp av Database migration-utvärderingen: 

1. Öppna [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Välj **fil** och välj sedan **ny utvärdering**. 
1. Ange ett projekt namn, välj SQL Server som käll Server typ och välj sedan Azure SQL Managed instance som mål server typ. 
1. Välj typ (er) av utvärderings rapporter som du vill generera. Till exempel databasens kompatibilitet och funktions paritet. Beroende på typen av utvärdering kan de behörigheter som krävs på käll SQL Server vara olika.  DMA kommer att markera de behörigheter som krävs för den valda Advisor-tjänsten innan du kör utvärderingen.
    - Kategorin **funktion paritet** innehåller en omfattande uppsättning rekommendationer, alternativ som är tillgängliga i Azure och åtgärder som hjälper dig att planera ditt migreringsjobb. (sysadmin-behörigheter krävs)
    - Kategorin **kompatibilitetsproblem** identifierar delvis stödda kompatibilitetsproblem som kan blockera migrering samt rekommendationer för att åtgärda dem ( `CONNECT SQL` , `VIEW SERVER STATE` och de behörigheter som `VIEW ANY DEFINITION` krävs).
1. Ange käll anslutnings information för din SQL Server och Anslut till käll databasen.
1. Välj **Starta utvärdering**. 
1. När processen är klar väljer du och granskar utvärderings rapporterna för att blockera och funktions paritets problem. Utvärderings rapporten kan också exporteras till en fil som kan delas med andra team eller personal i din organisation. 
1. Bestäm vilken kompatibilitetsnivå för databaser som minimerar efter migreringen.  
1. Identifiera den bästa Azure SQL Managed instance-SKU: n för din lokala arbets belastning. 

Mer information finns i [utföra en utvärdering av SQL Server migrering med data migration assistant](/sql/dma/dma-assesssqlonprem).

Om SQL-hanterad instans inte är ett lämpligt mål för din arbets belastning kan SQL Server på virtuella Azure-datorer vara ett användbart alternativt mål för ditt företag. 

#### <a name="scaled-assessments-and-analysis"></a>Skalade utvärderingar och analyser

Data Migration Assistant har stöd för att utföra skalnings bedömning och konsolidering av utvärderings rapporterna för analys. Om du har flera servrar och databaser som måste utvärderas och analyseras i skala för att ge en bredare vy av datafastigheten klickar du på följande länkar om du vill veta mer.

- [Utföra skalnings bedömning med PowerShell](/sql/dma/dma-consolidatereports)
- [Analysera utvärderings rapporter med hjälp av Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Att köra utvärderingar i skala för flera databaser kan också automatiseras med hjälp av [kommando rads verktyget DMA](/sql/dma/dma-commandline) , vilket innebär att resultaten kan laddas upp till [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) för ytterligare analys och mål beredskap.

### <a name="create-a-performance-baseline"></a>Skapa en bas linje för prestanda

Om du behöver jämföra arbets Belastningens prestanda på en SQL-hanterad instans med din ursprungliga arbets belastning som körs på SQL Server, skapar du en bas linje för prestanda som används för jämförelse. Mer information finns i [bas linje för prestanda](sql-server-to-managed-instance-performance-baseline.md) . 

### <a name="create-sql-managed-instance"></a>Skapa hanterad SQL-instans 

Baserat på informationen i fasen identifiering och utvärdering skapar du en lämplig storleks mål SQL-hanterad instans. Du kan göra detta med hjälp av mallen [Azure Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)eller en [Azure Resource Manager (arm)](../../managed-instance/create-template-quickstart.md). 


## <a name="migrate"></a>Migrera

När du har slutfört uppgifter som är kopplade till fasen före migrering är du redo att utföra schemat och datamigreringen. 

Migrera dina data med din valda [metod för migrering](sql-server-to-managed-instance-overview.md#compare-migration-options). 

Den här guiden beskriver de två mest populära alternativen-Azure Database Migration Service (DMS) och inbyggd säkerhets kopiering och återställning. 

### <a name="database-migration-service"></a>Database Migration Service

Om du vill utföra migreringar med DMS följer du stegen nedan:

1. Registrera **Microsoft. data migration** -resurs leverantören i din prenumeration om du utför den första gången.
1. Skapa en Azure Database Migration Service-instans på önskad plats (helst i samma region som din mål Azure SQL-hanterade instans) och välj ett befintligt virtuellt nätverk eller skapa ett nytt som värd för DMS-instansen.
1. När du har skapat din DMS-instans skapar du ett nytt migreringsjobb och anger käll Server typen **SQL Server** och mål server typen som **Azure SQL Database Hanterad instans**. Välj typ av aktivitet i bladet skapa projekt – online eller offline datamigrering. 
1.  Ange käll SQL Server information på informations sidan för **migreringen** och Azure SQL-hanterad instans information på sidan information om mål för **migrering** . Välj **Nästa**.
1. Välj den databas som du vill migrera. 
1. Ange konfigurations inställningar för att ange den **SMB-nätverks resurs** som innehåller dina säkerhets kopierings filer för databasen. Använd Windows-användarautentiseringsuppgifter med DMS som har åtkomst till nätverks resursen. Ange **Azure Storage konto information**. 
1. Granska migrerings översikten och välj **Kör migrering**. Du kan sedan övervaka migrerings aktiviteten och kontrol lera förloppet för migreringen av databasen.
1. När databasen har återställts väljer du **Starta start punkt**. Migreringsprocessen kopierar säkerhets kopian av loggen när du gör den tillgänglig i SMB-nätverks resursen och återställer den på målet. 
1. Stoppa all inkommande trafik till käll databasen och uppdatera anslutnings strängen till den nya Azure SQL-hanterade instans databasen. 

En detaljerad steg-för-steg-guide om migreringen finns i [migrera SQL Server till en Azure SQL-hanterad instans online med DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning 

En av de viktigaste funktionerna i Azure SQL-hanterad instans för att möjliggöra snabb och enkel databas migrering är den interna återställningen av databas säkerhets kopierings- `.bak` filer som lagras på [Azure Storage](https://azure.microsoft.com/services/storage/). Säkerhets kopiering och återställning är en asynkron åtgärd baserat på databasens storlek. 

Följande diagram ger en övergripande översikt över processen:

![Diagrammet visar SQL Server med en pil med etiketten säkerhets kopiering/uppladdning till URL som flödar till Azure Storage och en andra pil som har etiketten Återställ från URL: en som flödar från Azure Storage till en hanterad instans av SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Tiden att ta säkerhets kopian, överföra den till Azure Storage och utföra en intern återställnings åtgärd till den Azure SQL-hanterade instansen baserat på databasens storlek. Sätt en tillräckligt stillestånds tid för att hantera driften för stora databaser. 


Följ dessa steg om du vill migrera med säkerhets kopiering och återställning: 

1. Säkerhetskopiera databasen till Azure Blob Storage. Använd till exempel [säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) i [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Använd [Microsoft Azure-verktyget](https://go.microsoft.com/fwlink/?LinkID=324399) för att stödja databaser som är äldre än SQL Server 2012 SP1-CU2. 
1. Anslut till din Azure SQL-hanterade instans med hjälp av SQL Server Management Studio. 
1. Skapa en autentiseringsuppgift med hjälp av en signatur för delad åtkomst för att komma åt ditt Azure Blob Storage-konto med dina databas säkerhets kopior. Exempel:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Återställ säkerhets kopian från BLOB-behållaren för Azure Storage. Exempel: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. När återställningen är klar kan du Visa databasen i **Object Explorer** i SQL Server Management Studio. 

Mer information om migreringen finns i [återställa en databas till en Azure SQL-hanterad instans med SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> En databas återställnings åtgärd är asynkron och går att försöka igen. Du kan få ett fel meddelande i SQL Server Management Studio om anslutningen bryts eller om timeout går ut. Azure SQL Database fortsätter att försöka återställa databasen i bakgrunden och du kan följa förloppet för återställningen med hjälp av vyerna [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .



## <a name="data-sync-and-cutover"></a>Data synkronisering och start punkt

När du använder migreringsåtgärder som kontinuerligt replikerar/synkroniserar data ändringar från källan till målet, kan källdata och schemat ändras och riktas mot målet. Under datasynkroniseringen ska du se till att alla ändringar på källan fångas och tillämpas på målet under migreringsprocessen. 

När du har kontrollerat att data är samma på både källa och mål kan du Start punkt från källan till mål miljön. Det är viktigt att planera start punkt-processen med affärs-/program grupper för att säkerställa minimala avbrott under start punkt inte påverkar affärs kontinuiteten. 

> [!IMPORTANT]
> Mer information om de steg som är associerade med att utföra en start punkt som en del av migreringar med hjälp av DMS finns i [utföra migrering start punkt](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


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

   > [!NOTE]
   > Om du behöver hjälp med att utveckla och köra verifierings test efter migrering bör du tänka på vilken data kvalitets lösning som är tillgänglig från partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 



## <a name="leverage-advanced-features"></a>Utnyttja avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktioner som erbjuds av SQL-hanterad instans, till exempel [inbyggd hög tillgänglighet](../../database/high-availability-sla.md), [hot identifiering](../../database/azure-defender-for-sql.md)och [övervakning och justering av arbets belastningen](../../database/monitor-tune-overview.md). 

Med [Azure SQL-analys](../../../azure-monitor/insights/azure-sql.md) kan du övervaka en stor uppsättning hanterade instanser på ett centraliserat sätt.

Vissa SQL Server funktioner är bara tillgängliga när kompatibilitetsnivån för [databas](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) har ändrats till den senaste kompatibilitetsnivån (150). 


## <a name="next-steps"></a>Nästa steg

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL-hanterad instans finns i:
   - [Tjänst nivåer i Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Skillnader mellan SQL Server och Azure SQL-hanterad instans](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).