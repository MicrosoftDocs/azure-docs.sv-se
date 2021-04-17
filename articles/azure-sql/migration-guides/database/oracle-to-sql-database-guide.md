---
title: 'Oracle to Azure SQL Database: Migreringsguide'
description: I den här guiden får du lära dig hur du migrerar ditt Oracle-schema till Azure SQL Database med hjälp av SQL Server Migration Assistant för Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 45fbc1f85c5d7f66716fbf69deb430ce74575435
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388507"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Migreringsguide: Oracle till Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får [du](https://azure.microsoft.com/migration/migration-journey) lära dig hur du migrerar dina Oracle-scheman till Azure SQL Database med hjälp av [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant för Oracle (SSMA för Oracle).

Andra migreringsguider finns i [Azure Database Migration Guides](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera Oracle-schemat till SQL Database:

- Kontrollera att din källmiljö stöds.
- Ladda [ned SSMA för Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Ha en [SQL Database](../../database/single-database-create-quickstart.md) instans.
- Skaffa nödvändiga [behörigheter för SSMA för Oracle och](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) [providern](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att identifiera topologin för din miljö och utvärdera möjligheten att migrera [Azure-molnet.](https://azure.microsoft.com/migration) Den här delen av processen omfattar att utföra en inventering av de databaser som du behöver migrera, utvärdera databaserna för potentiella migreringsproblem eller blockerare och sedan lösa eventuella objekt som du har upptäckt.

### <a name="assess"></a>Utvärdera

Med hjälp av SSMA för Oracle kan du granska databasobjekt och data, utvärdera databaser för migrering, migrera databasobjekt till SQL Database och slutligen migrera data till databasen.

Så här skapar du en utvärdering:

1. Öppna [SSMA för Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Välj **Arkiv** och välj sedan **Nytt projekt.**
1. Ange ett projektnamn och en plats för att spara projektet. Välj **Azure SQL Database** som migreringsmål i listrutan och välj **OK.**

   ![Skärmbild som visar Anslut till Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Välj **Anslut till Oracle.** Ange värden för Oracle-anslutningsinformation i dialogrutan Anslut till **Oracle.**

1. Välj de Oracle-scheman som du vill migrera.

   ![Skärmbild som visar val av Oracle-schema.](./media/oracle-to-sql-database-guide/select-schema.png)

1. I **Oracle Metadata Explorer högerklickar** du på det Oracle-schema som du vill migrera och väljer sedan Skapa rapport **för** att generera en HTML-rapport. Du kan också välja en databas och sedan välja **fliken Skapa** rapport.

   ![Skärmbild som visar Skapa rapport.](./media/oracle-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverteringsstatistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Oracle-objekt och det arbete som krävs för att utföra schemakonverteringar. Standardplatsen för rapporten är i rapportmappen i SSMAProjects.

   Se till exempel `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Skärmbild som visar en utvärderingsrapport.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Verifiera datatyperna

Validera standardmappningarna av datatyper och ändra dem baserat på kraven om det behövs. Det gör du på följande sätt:

1. I SSMA för Oracle väljer **du Verktyg** och sedan **Projektinställningar.**
1. Välj fliken **Typmappning.**

   ![Skärmbild som visar Typmappning.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typmappningen för varje tabell genom att välja tabellen i **Oracle Metadata Explorer**.

### <a name="convert-the-schema"></a>Konvertera schemat

Så här konverterar du schemat:

1. (Valfritt) Lägg till dynamiska eller ad hoc-frågor i -instruktioner. Högerklicka på noden och välj sedan Lägg **till instruktioner**.
1. Välj fliken **Connect to Azure SQL Database** (Anslut till Azure SQL Database).
    1. I **SQL Database** anger du anslutningsinformation för att ansluta databasen.
    1. Välj målinstansen SQL Database i listrutan eller ange ett nytt namn. I så fall skapas en databas på målservern.
    1. Ange autentiseringsinformation och välj **Anslut.**

    ![Skärmbild som visar Anslut till Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. Högerklicka **på Oracle-schemat** i Oracle Metadata Explorer och välj sedan **Konvertera schema.** Du kan också välja ditt schema och sedan välja **fliken Konvertera** schema.

   ![Skärmbild som visar Konvertera schema.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar kan du jämföra och granska de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem baserat på rekommendationerna.

   ![Skärmbild som visar schemat granska rekommendationer.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Jämför den konverterade Transact-SQL-texten med de ursprungliga lagrade procedurerna och granska rekommendationerna.

   ![Skärmbild som visar granskningsrekommendationerna.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. I utdatafönstret väljer **du Granska** resultat och granskar felen i **fönstret Fellista.**
1. Spara projektet lokalt för en åtgärdsövning av offlineschemat. Välj **Spara projekt** på **Arkiv-menyn.** Det här steget ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du publicerar schemat till SQL Database.

## <a name="migrate"></a>Migrera

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migreringen omfattar två steg: att publicera schemat och migrera data.

Så här publicerar du schemat och migrerar dina data:

1. Publicera schemat genom att högerklicka på databasen från noden **Databaser** i **Azure SQL Database Metadata Explorer och** välja Synkronisera med **databas**.

   ![Skärmbild som visar Synkronisera med databas.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Granska mappningen mellan källprojektet och målet.

   ![Skärmbild som visar Synkronisera med databasgranskningen.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrera data genom att högerklicka på databasen eller objektet som du vill migrera i **Oracle Metadata Explorer** och välja Migrera **data.** Du kan också välja fliken **Migrera** data. Om du vill migrera data för en hel databas markerar du kryssrutan bredvid databasnamnet. Om du vill migrera data från enskilda tabeller expanderar du **databasen,** expanderar Tabeller och markerar sedan kryssrutorna bredvid tabellerna. Avmarkera kryssrutorna om du vill utelämna data från enskilda tabeller.

   ![Skärmbild som visar Migrera data.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Ange anslutningsinformation för både Oracle och SQL Database.
1. När migreringen är klar kan du visa **datamigreringsrapporten**.

   ![Skärmbild som visar datamigreringsrapporten.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Anslut till din SQL Database instans med [hjälp SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och verifiera migreringen genom att granska data och schema.

   ![Skärmbild som visar validering i SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Du kan också använda SQL Server Integration Services för att utföra migreringen. Mer information finns i:

- [Komma igång med SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services för Azure och Hybrid Data Movement](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Efter migreringen

När du har slutfört  migreringsfasen måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till målmiljön måste alla program som tidigare förbrukade källan börja använda målet. Om du utför den här uppgiften krävs i vissa fall ändringar i programmen.

Den [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code som gör att du kan analysera java-källkoden och identifiera API-anrop och frågor för dataåtkomst. Verktygslådan ger dig en enda fönstervy över vad som behöver åtgärdas för att stödja den nya databasens server. Mer information finns i blogginlägget Migrate your Java applications from Oracle (Migrera [dina Java-program](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) från Oracle).

### <a name="perform-tests"></a>Utföra tester

Test metoden för databasmigrering består av följande aktiviteter:

1. **Utveckla valideringstester:** Om du vill testa databasmigreringen måste du använda SQL-frågor. Du måste skapa valideringsfrågor som ska köras mot både käll- och måldatabaserna. Dina valideringsfrågor bör omfatta det omfång som du har definierat.
1. **Konfigurera en testmiljö:** Testmiljön ska innehålla en kopia av källdatabasen och måldatabasen. Se till att isolera testmiljön.
1. **Kör valideringstester:** Kör valideringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester:** Kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är viktig för att stämma av eventuella problem med datanoggrannhet, verifiera fullständighet och åtgärda prestandaproblem med arbetsbelastningen.

> [!NOTE]
> Mer information om dessa problem och hur du åtgärdar dem finns i [validerings- och optimeringsguiden efter migreringen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migreringstillgångar

Mer hjälp med att slutföra det här migreringsscenariot finns i följande resurser. De utvecklades som stöd för ett verkligt migreringsprojekt.

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Utvärderingsmodell och verktyg för dataarbetsbelastning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget innehåller förslag på "bästa passning"-målplattformar, molnberedskap och program- eller databasreparationsnivå för en viss arbetsbelastning. Den erbjuder enkel beräkning med ett klick och rapportgenerering som hjälper till att påskynda stora egendomsutvärderingar genom att tillhandahålla en automatiserad och enhetlig beslutsprocess för målplattformen.                                                          |
| [Skriptartefakter för Oracle-inventering](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Den här tillgången innehåller en PL/SQL-fråga som träffar Oracle-systemtabeller och ger ett antal objekt efter schematyp, objekttyp och status. Det ger också en grov uppskattning av rådata i varje schema och storleksändringen av tabeller i varje schema, med resultat som lagras i ett CSV-format.                                                                                                               |
| [Automatisera SSMA Oracle Assessment Collection & konsolidering](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Den här uppsättningen resurser använder en .csv-fil som post (sources.csv i projektmapparna) för att skapa de XML-filer som behövs för att köra en SSMA-utvärdering i konsolläge. Den source.csv tillhandahålls av kunden baserat på en inventering av befintliga Oracle-instanser. Utdatafilerna är AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml och VariableValueFile.xml.|
| [SSMA för vanliga Oracle-fel och så här åtgärdar du dem](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Med Oracle kan du tilldela ett icke-skalfritt villkor i WHERE-satsen. Men SQL Server stöder inte den här typen av villkor. Därför konverterar SSMA för Oracle inte frågor med ett icke-skalfritt villkor i WHERE-satsen. I stället genererar den felet O2SS0001. Den white paper innehåller mer information om problemet och olika sätt att lösa det.          |
| [Handbok för Oracle SQL Server migration](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Det här dokumentet fokuserar på de uppgifter som är associerade med att migrera ett Oracle-schema till den senaste versionen av SQL Server Database. Om migreringen kräver ändringar av funktioner måste den möjliga effekten av varje ändring på de program som använder databasen övervägas noggrant.                                                     |

Data SQL-teknikteamet utvecklade dessa resurser. Teamets huvudstadga är att avblockera och påskynda komplex modernisering för dataplattformsmigreringsprojekt till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

- En matris med Tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika databas- och datamigreringsscenarier och specialuppgifter finns i Tjänster och verktyg [för datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om SQL Database finns i:
  - [En översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Kalkylator för total ägandekostnad (TCO) i Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Mer information om ramverket och implementeringscykeln för molnmigrering finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metodtips för kostnad och storleksändring av arbetsbelastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)

- För videoinnehåll, se:
    - [Översikt över migreringsresan och de verktyg och tjänster som rekommenderas för utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
