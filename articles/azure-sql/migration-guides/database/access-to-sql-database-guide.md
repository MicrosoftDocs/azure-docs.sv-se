---
title: 'Åtkomst till Azure SQL Database: migrations guide'
description: I den här guiden får du lära dig att migrera dina Microsoft Access-databaser till Azure SQL Database att använda SQL Server Migration Assistant för åtkomst (SSMA för åtkomst).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 99e0fd3665a0269710a9b0994a1340745f77236f
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027320"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guide för migrering: åtkomst till Azure SQL Database

Den här migreringsguiden lär dig att migrera dina Microsoft Access-databaser till Azure SQL Database att använda SQL Server Migration Assistant för åtkomst.

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Om du vill migrera din Access-databas till Azure SQL Database behöver du:

- För att verifiera att din käll miljö stöds. 
- [SQL Server Migration Assistant för åtkomst](https://www.microsoft.com/download/details.aspx?id=54255). 
- Anslutning och tillräcklig behörighet för att få åtkomst till både källa och mål. 


## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven, är du redo att upptäcka miljön och bedöma möjligheten för migreringen.


### <a name="assess"></a>Utvärdera 

Använd SQL Server Migration Assistant (SSMA) för att få åtkomst till granskning av databas objekt och data och utvärdera databaser för migrering. 

Följ dessa steg om du vill skapa en utvärdering: 

1. Öppna [SQL Server Migration Assistant för åtkomst](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn, en plats där du vill spara projektet och välj sedan Azure SQL Database som mål för migreringen från List rutan. Välj **OK**:

   ![Välj nytt projekt](./media/access-to-sql-database-guide/new-project.png)

1. Välj **Lägg till databaser** och välj databaser som ska läggas till i det nya projektet:

   ![Välj Lägg till databaser](./media/access-to-sql-database-guide/add-databases.png)

1. I **Access metadata Explorer** högerklickar du på databasen och väljer sedan **Skapa rapport**. Alternativt kan du välja **Skapa rapport** i navigerings fältet när du har valt schemat:

   ![Högerklicka på databasen och välj Skapa rapport](./media/access-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Access-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects

   Exempelvis: `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Granska utvärderings exempel rapporten](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt:

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** :

   ![Typ mappningar](./media/access-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Access metadata Explorer**.


### <a name="convert-schema"></a>Konvertera schema

Följ dessa steg om du vill konvertera databas objekt: 

1. Välj **Anslut till Azure SQL Database**. 
    1. Ange anslutnings information för att ansluta databasen i Azure SQL Database.
    1. Välj mål SQL Database i list rutan eller ange ett nytt namn, i vilket fall en databas ska skapas på mål servern. 
    1. Ange information om autentisering. 
    1. Välj **Anslut**:

   ![Ansluta till Azure SQL Database](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Högerklicka på databasen i **Access metadata Explorer** och välj **konvertera schema**. Alternativt kan du välja **konvertera schema** från det övre navigerings fältet när du har valt databasen:

   ![Högerklicka på databasen och välj Konvertera schema](./media/access-to-sql-database-guide/convert-schema.png)
   

1. När konverteringen är klar kan du jämföra och granska de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna:

   ![Konverterade objekt kan jämföras med källa](./media/access-to-sql-database-guide/table-comparison.png)

   Jämför konverterad Transact-SQL-text till den ursprungliga koden och granska rekommendationerna:

   ![Konverterade frågor kan jämföras med käll koden](./media/access-to-sql-database-guide/query-comparison.png)

1. Valfritt Om du vill konvertera ett enskilt objekt högerklickar du på objektet och väljer **konvertera schema**. Konverterade objekt visas i fetstil i **Access metadata Explorer**: 

   ![Fetstilta objekt i metadata Explorer har konverterats](./media/access-to-sql-database-guide/converted-items.png)
 
1. Välj **gransknings resultat** i fönstret utdata och granska fel i **fel listans** fönster. 
1. Spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du kan publicera schemat till SQL Database.


## <a name="migrate"></a>Migrera

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migrering av data är en Mass inläsnings åtgärd som flyttar rader med data till Azure SQL Database i transaktioner. Antalet rader som ska läsas in i Azure SQL Database i varje transaktion konfigureras i projekt inställningarna.

Följ dessa steg om du vill publicera schemat och migrera data med hjälp av SSMA för åtkomst: 

1. Om du inte redan har gjort det väljer du **Anslut till Azure SQL Database** och anger anslutnings information. 
1. Publicera schemat: Högerklicka på databasen i **Azure SQL Database metadata Explorer** och välj **Synkronisera med databas**. Den här åtgärden publicerar MySQL-schemat till Azure SQL Database:

   ![Synkronisera med databas](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Granska mappningen mellan käll projektet och målet:

   ![Granska synkroniseringen med databasen](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrera data: Högerklicka på databasen eller objektet som du vill migrera i **Access metadata Explorer** och välj **migrera data**. Alternativt kan du välja **migrera data** från det övre navigerings fältet. Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar tabeller och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller:

    ![Migrera data](./media/access-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du Visa **data flyttnings rapporten**:  

    ![Migrera data granskning](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Anslut till din Azure SQL Database genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och verifiera migreringen genom att granska data och schema:

   ![Validera i SSMA](./media/access-to-sql-database-guide/validate-data.png)



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

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process. |


Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.

## <a name="next-steps"></a>Nästa steg

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).

