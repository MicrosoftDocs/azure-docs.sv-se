---
title: 'Åtkomst till Azure SQL Database: migrations guide'
description: I den här guiden får du lära dig hur du migrerar dina Microsoft Access-databaser till en Azure SQL-databas med hjälp av SQL Server Migration Assistant för åtkomst (SSMA för åtkomst).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f9fa2426e371ab9fd99e88979cbcbbb34adb00d6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643584"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guide för migrering: åtkomst till Azure SQL Database

I den här guiden får du lära dig hur du migrerar din Microsoft Access-databas till en Azure SQL Database med hjälp av SQL Server Migration Assistant för åtkomst (SSMA för åtkomst).

Andra guider för migrering finns i [Guide för Azure Database migration](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera din Access-databas till en SQL-databas gör du följande:

- Kontrol lera att käll miljön stöds. 
- Hämta och installera [SQL Server Migration Assistant för åtkomst](https://www.microsoft.com/download/details.aspx?id=54255).
- Se till att du har anslutning och tillräcklig behörighet för att få åtkomst till både källa och mål.

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för migreringen.


### <a name="assess"></a>Utvärdera 

Använd SSMA för åtkomst för att granska databas objekt och data och utvärdera databaser för migrering. 

Gör så här för att skapa en utvärdering: 

1. Öppna [SSMA för åtkomst](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn och en plats för ditt projekt och välj sedan **Azure SQL Database** som migreringsmålet i den nedrullningsbara listan. 
1. Välj **OK**. 

   ![Skärm bild av fönstret "nytt projekt" där du anger namn och plats för ditt migreringsjobb.](./media/access-to-sql-database-guide/new-project.png)

1. Välj **Lägg till databaser** och välj sedan de databaser som ska läggas till i det nya projektet. 

   ![Skärm bild av fliken "Lägg till databaser" i SSMA för åtkomst.](./media/access-to-sql-database-guide/add-databases.png)

1. I fönstret **åtkomst till metadata-Utforskaren** högerklickar du på en databas och väljer sedan **Skapa rapport**. Alternativt kan du välja fliken **Skapa rapport** längst upp till höger.

   ![Skärm bild av kommandot "Skapa rapport" i åtkomst till metadata Explorer.](./media/access-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistiken och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Access-objekt och förstå vilken ansträngning som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects. Exempel:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Skärm bild av en exempel databas rapport utvärdering i SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Verifiera data typerna

Validera standard mappningarna för data typ och ändra dem efter behov. Så här gör du:

1. I SSMA för åtkomst väljer du **verktyg** och väljer sedan **projekt inställningar**. 
1. Välj fliken **typ mappning** . 

   ![Skärm bild av fönstret "typ mappning" i SSMA för åtkomst.](./media/access-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabell namnet i fönstret **åtkomst till metadata Explorer** .


### <a name="convert-the-schema"></a>Konvertera schemat

Gör så här om du vill konvertera databas objekt: 

1. Välj fliken **Anslut till Azure SQL Database** och gör sedan följande:

   a. Ange information för att ansluta till din SQL-databas.  
   b. Välj mål-SQL-databas i list rutan. Eller så kan du ange ett nytt namn, vilket innebär att en databas skapas på mål servern.  
   c. Ange information om autentisering.   
   d. Välj **Anslut**.

   ![Skärm bild av fönstret "Anslut till Azure SQL Database" för att ange anslutnings information.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. I fönstret **åtkomst till metadata-Utforskaren** högerklickar du på databasen och väljer sedan **konvertera schema**. Alternativt kan du välja din databas och sedan välja fliken **konvertera schema** .

   ![Skärm bild av kommandot "konvertera schema" i fönstret "åtkomst till metadata Explorer".](./media/access-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar kan du jämföra de konverterade objekten med de ursprungliga objekten för att identifiera potentiella problem och åtgärda problemen utifrån rekommendationerna.

   ![Skärm bild som visar en jämförelse av de konverterade objekten till käll objekt.](./media/access-to-sql-database-guide/table-comparison.png)

    Jämför den konverterade Transact-SQL-texten med den ursprungliga koden och granska rekommendationerna.

   ![Skärm bild som visar en jämförelse av konverterade frågor till käll koden.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Valfritt Om du vill konvertera ett enskilt objekt högerklickar du på objektet och väljer sedan **konvertera schema**. Konverterade objekt visas i fet stil i **Access metadata Explorer**: 

   ![Skärm bild som visar att objekten i Access metadata Explorer har konverterats.](./media/access-to-sql-database-guide/converted-items.png)
 
1. I fönstret **utdata** väljer du ikonen **Granska resultat** och granskar felen i fönstret med **fel listan** . 
1. Spara projektet lokalt för en arbets schema reparation. Det gör du genom att välja **Arkiv**  >  **Spara projekt**. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar dem i SQL-databasen.

## <a name="migrate-the-databases"></a>Migrera databaserna

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser kan du köra migreringsprocessen. Migrering av data är en Mass inläsnings åtgärd som flyttar data rader till en Azure SQL-databas i transaktioner. Antalet rader som ska läsas in i din SQL-databas i varje transaktion konfigureras i projekt inställningarna.

Om du vill publicera schemat och migrera data med hjälp av SSMA för åtkomst gör du följande: 

1. Om du inte redan har gjort det väljer du **Anslut till Azure SQL Database** och anger anslutnings information. 

1. Publicera schemat. I fönstret **Azure SQL Database metadata Explorer** högerklickar du på den databas som du arbetar med och väljer sedan **Synkronisera med databas**. Den här åtgärden publicerar MySQL-schemat till SQL-databasen.

1. I fönstret **Synkronisera med databas** granskar du mappningen mellan käll projektet och målet:

   ![Skärm bild av fönstret "synkronisera med databas" för att granska synkroniseringen med databasen.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. I fönstret **Öppna metadata i Utforskaren** markerar du kryss rutorna bredvid de objekt som du vill migrera. Om du vill migrera hela databasen markerar du kryss rutan bredvid databasen. 

1. Migrera data. Högerklicka på databasen eller objektet som du vill migrera och välj sedan **migrera data**. Alternativt kan du välja fliken **migrera data** längst upp till höger.  

   Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller.

    ![Skärm bild av kommandot "migrera data" i fönstret "åtkomst till metadata Explorer".](./media/access-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du Visa **data flyttnings rapporten**.  

    ![Skärm bild av fönstret "migrera data rapport" som visar en exempel rapport för granskning.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Anslut till din Azure SQL-databas med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och verifiera migreringen genom att granska data och schema.

   ![Skärm bild av SQL Server Management Studio Object Explorer för att verifiera migreringen i SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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

- En matris med tjänster och verktyg från Microsoft och tjänster från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Information om hur du utför data åtkomst Layer A/B-testning finns i [Översikt över Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
