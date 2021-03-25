---
title: 'MySQL till Azure SQL Database: migration guide'
description: Den här guiden lär dig att migrera dina MySQL-databaser till Azure SQL Database att använda SQL Server Migration Assistant för MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 14b2c1f98ae977548edb635b8a8a7a956b3f2dd7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023783"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guide för migrering: MySQL till Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig att migrera MySQL-databasen till Azure SQL Database att använda SQL Server Migration Assistant för MySQL (SSMA for MySQL). 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna migrera MySQL-databasen till Azure SQL Database:

- För att verifiera att din käll miljö stöds. För närvarande stöds MySQL 5,6 och 5,7. 
- [SQL Server Migration Assistant för MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Anslutning och tillräcklig behörighet för att få åtkomst till både källa och mål. 


## <a name="pre-migration"></a>Före migrering 

När du har uppfyllt kraven, är du redo att upptäcka miljön och bedöma möjligheten för migreringen.

### <a name="assess"></a>Utvärdera 

Använd SQL Server Migration Assistant (SSMA) för MySQL för att granska databas objekt och data och utvärdera databaser för migrering. 

Utför följande steg för att skapa en utvärdering: 

1. Öppna [SQL Server Migration Assistant för MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Välj **fil** på menyn och välj sedan **nytt projekt**. 
1. Ange projekt namnet, en plats där du vill spara projektet. Välj **Azure SQL Database** som mål för migrering. Välj **OK**:

   ![Nytt projekt](./media/mysql-to-sql-database-guide/new-project.png)

1. Välj **Anslut till MySQL** och ange anslutnings information för att ansluta MySQL-servern:

   ![Anslut till MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Högerklicka på MySQL-schemat i **MySQL metadata Explorer** och välj **Skapa rapport**. Alternativt kan du välja **Skapa rapport** från det övre navigerings fältet:

   ![Skapa rapport](./media/mysql-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av MySQL-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.
 
   Exempelvis: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Konverterings rapport](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt: 

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** :

   ![Typ mappningar](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i den **MySQL-metadata Explorer**. 

### <a name="convert-schema"></a>Konvertera schema 

Följ dessa steg om du vill konvertera schemat: 

1. Valfritt Om du vill konvertera dynamiska eller ad hoc-frågor högerklickar du på noden och väljer **Lägg till instruktion**. 
1. Välj **Anslut till Azure SQL Database**. 
    1. Ange anslutnings information för att ansluta databasen i Azure SQL Database.
    1. Välj mål SQL Database i list rutan eller ange ett nytt namn, i vilket fall en databas ska skapas på mål servern. 
    1. Ange information om autentisering. 
    1. Välj **Anslut**:

   ![Anslut till SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Högerklicka på schemat och välj **konvertera schema**. Alternativt kan du välja **konvertera schema** från övre linjens navigerings fält när du har valt databasen:

   ![Konvertera schema](./media/mysql-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar kan du jämföra och granska de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna:

   ![Konverterade objekt kan jämföras med källa](./media/mysql-to-sql-database-guide/table-comparison.png)

   Jämför konverterad Transact-SQL-text till den ursprungliga koden och granska rekommendationerna:

   ![Konverterade frågor kan jämföras med käll koden](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Välj **gransknings resultat** i fönstret utdata och granska fel i **fel listans** fönster. 
1. Spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du kan publicera schemat till SQL Database.



## <a name="migrate"></a>Migrera 

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migreringen består av två steg – att publicera schemat och migrera data. 

Följ dessa steg om du vill publicera schemat och migrera data: 

1. Publicera schemat: Högerklicka på databasen i **Azure SQL Database metadata Explorer** och välj **Synkronisera med databas**. Den här åtgärden publicerar MySQL-schemat till Azure SQL Database:

   ![Synkronisera med databas](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Granska mappningen mellan käll projektet och målet:

   ![Synkronisera med databas granskning](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrera data: Högerklicka på databasen eller objektet som du vill migrera i **MySQL metadata Explorer** och välj **migrera data**. Alternativt kan du välja **migrera data** från det övre navigerings fältet. Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar tabeller och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller:

   ![Migrera data](./media/mysql-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du Visa **data flyttnings** rapporten: 

   ![Data flyttnings rapport](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Anslut till din Azure SQL Database genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och verifiera migreringen genom att granska data och schema:

    ![Validera i SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Efter migreringen 

När du har slutfört **migreringen** måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av att utföra följande aktiviteter:

1. **Utveckla verifieringstester**. Om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.

2. **Konfigurera test miljö**. Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

3. **Kör verifierings test**. Kör verifierings testen mot källan och målet och analysera sedan resultaten.

4. **Kör prestandatester**. Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen.

Mer information om de här problemen och specifika steg för att minimera dem finns i [guiden för validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migrera till gångar

Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| Rubrik/länk     | Beskrivning    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process. |

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.

## <a name="next-steps"></a>Nästa steg 

- Se till att ta en titt på den [totala kostnaden för total ägande kostnad (TCO) i Azure](https://aka.ms/azure-tco) för att beräkna de kostnads besparingar du kan inse genom att migrera dina arbets belastningar till Azure.

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Mer information om andra biflyttnings guider finns i [databas migrering](https://datamigration.microsoft.com/). 

För videor, se: 
- [Översikt över migrerings resan och de verktyg/tjänster som rekommenderas för utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
