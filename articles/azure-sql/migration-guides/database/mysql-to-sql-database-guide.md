---
title: 'MySQL till Azure SQL Database: Migreringsguide'
description: I den här guiden får du lära dig hur du migrerar dina MySQL-databaser till en Azure SQL-databas med hjälp av SQL Server Migration Assistant for MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389459"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Migreringsguide: MySQL till Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här [](https://azure.microsoft.com/migration/migration-journey) guiden får du lära dig hur du migrerar din MySQL-databas till en Azure SQL-databas med [hjälp av SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for MySQL (SSMA for MySQL). 

Andra migreringsguider finns i [Azure Database Migration Guide](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera MySQL-databasen till en SQL-databas gör du följande:

- Kontrollera att din källmiljö stöds. För närvarande stöds MySQL 5.6 och 5.7. 
- Ladda ned och [installera SQL Server Migration Assistant för MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Kontrollera att du har anslutning och tillräcklig behörighet för att komma åt både källan och målet.

## <a name="pre-migration"></a>Före migrering 

När du har uppfyllt kraven är du redo att identifiera topologin för din miljö och utvärdera möjligheten att migrera [Azure-molnet.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Utvärdera 

Använd SQL Server Migration Assistant (SSMA) för MySQL för att granska databasobjekt och data och utvärdera databaser för migrering. 

Skapa en utvärdering genom att göra följande:

1. Öppna [SSMA for MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Välj **Arkiv** och sedan **Nytt projekt.** 
1. I fönstret **Nytt projekt** anger du ett namn och en  plats för projektet och går sedan till listrutan Migrera till och väljer **Azure SQL Database**. 
1. Välj **OK**.

   ![Skärmbild av fönstret Nytt projekt där du anger migreringsprojektets namn, plats och mål.](./media/mysql-to-sql-database-guide/new-project.png)

1. Välj fliken **Anslut till MySQL** och ange sedan information om hur du ansluter MySQL-servern. 

   ![Skärmbild av fönstret "Anslut till MySQL" för att ange anslutningar till källan.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. I fönstret **MySQL Metadata Explorer** högerklickar du på MySQL-schemat och väljer sedan **Skapa rapport.** Du kan också välja fliken **Skapa rapport** längst upp till höger.

   ![Skärmbild av länkarna "Skapa rapport" i SSMA for MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverteringsstatistik, fel och varningar. Analysera den för att förstå konverteringsproblem och lösningar. 
   Du kan också öppna rapporten i Excel för att få en inventering av MySQL-objekt och förstå det arbete som krävs för att utföra schemakonverteringar. Standardplatsen för rapporten är i rapportmappen i SSMAProjects. Exempel: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Skärmbild av en exempelkonverteringsrapport i SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Verifiera datatyperna

Verifiera standardmappningarna av datatyper och ändra dem baserat på kraven, om det behövs. Så här gör du: 

1. Välj **Verktyg** och sedan **Projektinställningar.**  
1. Välj **fliken Typmappningar.** 

   ![Skärmbild av fönstret "Typmappning" i SSMA för MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typmappningen för varje tabell genom att välja tabellnamnet i **fönstret MySQL Metadata Explorer.** 

### <a name="convert-the-schema"></a>Konvertera schemat 

Om du vill konvertera schemat gör du följande: 

1. (Valfritt) Om du vill konvertera dynamiska eller specialiserade frågor högerklickar du på noden och väljer sedan Lägg **till instruktion**. 

1. Välj **fliken Anslut Azure SQL Database** och gör sedan följande:

   a. Ange information för att ansluta till din SQL-databas.  
   b. I listrutan väljer du din SQL-måldatabas. Eller så kan du ange ett nytt namn, vilket innebär att en databas skapas på målservern.  
   c. Ange autentiseringsinformation.  
   d. Välj **Anslut**.

   ![Skärmbild av fönstret "Anslut till Azure SQL Database" i SSMA för MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Högerklicka på det schema som du arbetar med och välj sedan **Konvertera schema.** Du kan också välja fliken **Konvertera schema** längst upp till höger.

   ![Skärmbild av kommandot "Konvertera schema" i fönstret MySQL Metadata Explorer.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar granskar du och jämför de konverterade objekten med de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem baserat på rekommendationerna. 

   ![Skärmbild som visar en jämförelse av de konverterade objekten till de ursprungliga objekten.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Jämför den konverterade Transact-SQL-texten med den ursprungliga koden och granska rekommendationerna.

   ![Skärmbild som visar en jämförelse av konverterade frågor till källkoden.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. I fönstret **Utdata** väljer **du Granska** resultat och granskar sedan eventuella fel i **fönstret Fellista.** 
1. Spara projektet lokalt för en åtgärdsövning av offlineschemat. Det gör du genom att **välja Arkiv**  >  **Spara projekt.** Detta ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du publicerar schemat till din SQL-databas.

   Jämför de konverterade procedurerna med de ursprungliga procedurerna, som du ser här: 

   ![Skärmbild som visar en jämförelse av de konverterade procedurerna med de ursprungliga procedurerna.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrera databaserna 

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser kan du köra migreringsprocessen. Migreringen omfattar två steg: att publicera schemat och migrera data. 

Gör följande för att publicera schemat och migrera data: 

1. Publicera schemat. I fönstret **Azure SQL Database Metadata Explorer** högerklickar du på databasen och väljer sedan Synkronisera med **databas.** Den här åtgärden publicerar MySQL-schemat till din SQL-databas.

   ![Skärmbild av fönstret "Synkronisera med databasen" för granskning av databasmappning.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrera data. I fönstret **MySQL Metadata Explorer** högerklickar du på det MySQL-schema som du vill migrera och väljer sedan **Migrera data.** Du kan också välja fliken **Migrera data** längst upp till höger.

   Om du vill migrera data för en hel databas markerar du kryssrutan bredvid databasnamnet. Om du vill migrera data från enskilda tabeller expanderar du **databasen,** expanderar Tabeller och markerar sedan kryssrutan bredvid tabellen. Avmarkera kryssrutan om du vill utelämna data från enskilda tabeller.

   ![Skärmbild av kommandot "Migrera data" i fönstret "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du visa **datamigreringsrapporten.**
   
   ![Skärmbild av datamigreringsrapporten.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Anslut till DIN SQL-databas med [hjälp SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och validera migreringen genom att granska data och schema.

   ![Skärmbild av SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört  migreringssteget måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till målmiljön måste alla program som tidigare förbrukade källan börja använda målet. Detta kräver i vissa fall ändringar i programmen.

### <a name="perform-tests"></a>Utföra tester

Test metoden för databasmigrering består av följande aktiviteter:

1. **Utveckla valideringstester:** Om du vill testa databasmigreringen måste du använda SQL-frågor. Du måste skapa valideringsfrågor som ska köras mot både käll- och måldatabaserna. Dina valideringsfrågor bör omfatta det omfång som du har definierat.

1. **Konfigurera en testmiljö:** Testmiljön ska innehålla en kopia av källdatabasen och måldatabasen. Se till att isolera testmiljön.

1. **Kör valideringstester:** Kör valideringstester mot källan och målet och analysera sedan resultaten.

1. **Kör prestandatester:** Kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är viktig för att stämma av eventuella problem med datanoggrannhet, verifiera fullständighet och åtgärda prestandaproblem med arbetsbelastningen.

Mer information om dessa problem och hur du åtgärdar dem finns i [validerings- och optimeringsguiden efter migreringen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migreringstillgångar

Mer hjälp med att slutföra det här migreringsscenariot finns i följande resurs. Det utvecklades som stöd för ett verkligt migreringsprojekt.

| Rubrik | Beskrivning |
| --- | --- |
| [Utvärderingsmodell och verktyg för dataarbetsbelastning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Innehåller föreslagna målplattformar med "bästa passning", molnberedskap och åtgärdsnivåer för program/databaser för angivna arbetsbelastningar. Den erbjuder enkel beräkning med ett klick och rapportgenerering som hjälper till att påskynda stora egendomsutvärderingar genom att tillhandahålla en automatiserad, enhetlig beslutsprocess för målplattformen. |

Data SQL-teknikteamet utvecklade dessa resurser. Teamets huvudstadga är att avblockera och påskynda komplex modernisering för dataplattformsmigreringsprojekt till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg 

- Information om kostnadsbesparingar som du kan göra genom att migrera dina arbetsbelastningar till Azure finns i kalkylatorn för total [ägandekostnad i Azure.](https://aka.ms/azure-tco)

- En matris med Tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas- och datamigrering och specialuppgifter finns i Tjänst och verktyg för [datamigrering.](../../../dms/dms-tools-matrix.md)

- Andra migreringsguider finns i [Migreringsguide för Azure Database.](https://datamigration.microsoft.com/) 

- Migreringsvideor finns i [Översikt över migreringsresan och rekommenderade migrerings- och utvärderingsverktyg och -tjänster.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- Mer [molnmigreringsresurser](https://azure.microsoft.com/migration/resources/)finns i [molnmigreringslösningar.](https://azure.microsoft.com/migration)

