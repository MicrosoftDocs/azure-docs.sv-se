---
title: 'MySQL till Azure SQL Database: migration guide'
description: I den här guiden får du lära dig hur du migrerar dina MySQL-databaser till en Azure SQL Database med hjälp av SQL Server Migration Assistant för MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 8952e6e1bda8763d403c02dcd5815f1e1c0941e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641870"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guide för migrering: MySQL till Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig hur du migrerar MySQL-databasen till en Azure SQL Database med hjälp av SQL Server Migration Assistant för MySQL (SSMA for MySQL). 

Andra guider för migrering finns i [Guide för Azure Database migration](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera MySQL-databasen till en SQL-databas gör du följande:

- Kontrol lera att käll miljön stöds. För närvarande stöds MySQL 5,6 och 5,7. 
- Hämta och installera [SQL Server Migration Assistant för MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Se till att du har anslutning och tillräcklig behörighet för att komma åt både källan och målet.

## <a name="pre-migration"></a>Före migrering 

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för migreringen.

### <a name="assess"></a>Utvärdera 

Använd SQL Server Migration Assistant (SSMA) för MySQL för att granska databas objekt och data och utvärdera databaser för migrering. 

Gör så här för att skapa en utvärdering:

1. Öppna [SSMA för MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. I fönstret **nytt projekt** anger du ett namn och en plats för projektet. i list rutan **migrera till** väljer du **Azure SQL Database**. 
1. Välj **OK**.

   ![Skärm bild av fönstret "nytt projekt" där du kan ange namn, plats och mål för ditt migreringsjobb.](./media/mysql-to-sql-database-guide/new-project.png)

1. Välj fliken **Anslut till MySQL** och ange sedan information om hur du ansluter MySQL-servern. 

   ![Skärm bild av fönstret Anslut till MySQL för att ange anslutningar till källan.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. I fönstret **MySQL metadata Explorer** högerklickar du på MySQL-schemat och väljer sedan **Skapa rapport**. Alternativt kan du välja fliken **Skapa rapport** längst upp till höger.

   ![Skärm bild av länkarna "Skapa rapport" i SSMA för MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik, fel och varningar. Analysera den för att förstå konverterings problemen och lösningarna. 
   Du kan också öppna rapporten i Excel för att få en inventering av MySQL-objekt och förstå den ansträngning som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects. Exempel: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Skärm bild av en exempel konverterings rapport i SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Verifiera data typerna

Validera standard mappningarna för data typ och ändra dem baserat på krav, om det behövs. Så här gör du: 

1. Välj **verktyg** och välj sedan **projekt inställningar**.  
1. Välj fliken **typ mappningar** . 

   ![Skärm bild av fönstret "typ mappning" i SSMA för MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabell namnet i fönstret **MySQL metadata Explorer** . 

### <a name="convert-the-schema"></a>Konvertera schemat 

Om du vill konvertera schemat gör du följande: 

1. Valfritt Om du vill konvertera dynamiska eller specialiserade frågor högerklickar du på noden och väljer sedan **Lägg till instruktion**. 

1. Välj fliken **Anslut till Azure SQL Database** och gör sedan följande:

   a. Ange information för att ansluta till din SQL-databas.  
   b. Välj mål-SQL-databas i list rutan. Eller så kan du ange ett nytt namn, vilket innebär att en databas skapas på mål servern.  
   c. Ange information om autentisering.  
   d. Välj **Anslut**.

   ![Skärm bild av fönstret "Anslut till Azure SQL Database" i SSMA för MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Högerklicka på det schema som du arbetar med och välj sedan **konvertera schema**. Alternativt kan du välja fliken **konvertera schema** längst upp till höger.

   ![Skärm bild av kommandot "Convert schema" i fönstret "MySQL metadata Explorer".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar granskar och jämför du de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna. 

   ![Skärm bild som visar en jämförelse av de konverterade objekten till de ursprungliga objekten.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Jämför den konverterade Transact-SQL-texten med den ursprungliga koden och granska rekommendationerna.

   ![Skärm bild som visar en jämförelse av konverterade frågor till käll koden.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. I fönstret **utdata** väljer du **gransknings resultat** och granskar eventuella fel i fönstret med **fel listan** . 
1. Spara projektet lokalt för en arbets schema reparation. Det gör du genom att välja **Arkiv**  >  **Spara projekt**. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat i SQL-databasen.

   Jämför de konverterade procedurerna med de ursprungliga procedurerna, som du ser här: 

   ![Skärm bild som visar en jämförelse av de konverterade procedurerna till de ursprungliga procedurerna.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrera databaserna 

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser kan du köra migreringsprocessen. Migreringen består av två steg: publicera schemat och migrera data. 

Så här publicerar du schemat och migrerar data: 

1. Publicera schemat. I fönstret **Azure SQL Database metadata Explorer** högerklickar du på databasen och väljer sedan **Synkronisera med databas**. Den här åtgärden publicerar MySQL-schemat till din SQL-databas.

   ![Skärm bild av fönstret "synkronisera med databas" för att granska databas mappning.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrera data. I fönstret **MySQL metadata Explorer** högerklickar du på det MySQL-schema som du vill migrera och väljer sedan **migrera data**. Alternativt kan du välja fliken **migrera data** längst upp till höger.

   Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller.

   ![Skärm bild av kommandot "migrera data" i fönstret "MySQL metadata Explorer".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du Visa **data flyttnings rapporten**.
   
   ![Skärm bild av data migrations rapporten.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Anslut till din SQL-databas med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och verifiera migreringen genom att granska data och schema.

   ![Skärm bild av SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört *migreringen* måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.

1. **Konfigurera en test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.

1. **Kör prestandatester**: kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att stämma av data precisions problem, kontrol lera att de är klara och åtgärda prestanda problem med arbets belastningen.

Mer information om de här problemen och stegen för att minimera dem finns i guiden för [validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migrera till gångar

Mer hjälp om hur du slutför det här scenariot för migrering finns i följande resurs. Den har utvecklats för att ge stöd till ett verkligt projekt engagemang för migrering.

| Rubrik | Beskrivning |
| --- | --- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tillhandahåller rekommenderade "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för angivna arbets belastningar. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad, enhetlig besluts process för mål plattform. |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg 

- För att beräkna de kostnads besparingar du kan inse genom att migrera dina arbets belastningar till Azure kan du läsa mer i [Azures totala kostnad för ägande kostnad](https://aka.ms/azure-tco).

- En matris med tjänster och verktyg från Microsoft och tjänster från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Andra guider för migrering finns i [Guide för Azure Database migration](https://datamigration.microsoft.com/). 

- För migrerings-videor, se [Översikt över migrerings resan och rekommenderade verktyg och tjänster för migrering och bedömning](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
