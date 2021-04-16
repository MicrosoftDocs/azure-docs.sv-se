---
title: 'Åtkomst till Azure SQL Database: Migreringsguide'
description: I den här guiden får du lära dig hur du migrerar dina Microsoft Access-databaser till en Azure SQL-databas med hjälp av SQL Server Migration Assistant for Access (SSMA för åtkomst).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388473"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Migreringsguide: Åtkomst till Azure SQL Database

I den här [](https://azure.microsoft.com/migration/migration-journey) guiden får du lära dig hur du migrerar din Microsoft Access-databas till en Azure SQL-databas med hjälp [av SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Access (SSMA för åtkomst).

Andra migreringsguider finns i [Azure Database Migration Guide](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera Access-databasen till en SQL-databas gör du följande:

- Kontrollera att din källmiljö stöds. 
- Hämta och installera [SQL Server Migration Assistant för Access](https://www.microsoft.com/download/details.aspx?id=54255).
- Kontrollera att du har anslutning och tillräcklig behörighet för att komma åt både källa och mål.

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att identifiera topologin för din miljö och utvärdera möjligheten att migrera [Azure-molnet.](https://azure.microsoft.com/migration)


### <a name="assess"></a>Utvärdera 

Använd SSMA för åtkomst för att granska databasobjekt och data och utvärdera databaser för migrering. 

Skapa en utvärdering genom att göra följande: 

1. Öppna [SSMA för Access](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Välj **Arkiv** och sedan **Nytt projekt.** 
1. Ange ett projektnamn och en plats för projektet. I listrutan väljer **du sedan Azure SQL Database** som migreringsmål. 
1. Välj **OK**. 

   ![Skärmbild av fönstret Nytt projekt där du anger namnet och platsen för migreringsprojektet.](./media/access-to-sql-database-guide/new-project.png)

1. Välj **Lägg till** databaser och välj sedan de databaser som ska läggas till i det nya projektet. 

   ![Skärmbild av fliken "Lägg till databaser" i SSMA för åtkomst.](./media/access-to-sql-database-guide/add-databases.png)

1. Högerklicka på **en databas** i fönstret Access Metadata Explorer och välj sedan Skapa **rapport.** Du kan också välja fliken **Skapa rapport** längst upp till höger.

   ![Skärmbild av kommandot "Skapa rapport" i Access Metadata Explorer.](./media/access-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverteringsstatistiken och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Åtkomstobjekt och förstå det arbete som krävs för att utföra schemakonverteringar. Standardplatsen för rapporten är i rapportmappen i SSMAProjects. Exempel:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Skärmbild av en exempelutvärdering av databasrapporten i SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Verifiera datatyperna

Verifiera standardmappningarna av datatyper och ändra dem baserat på dina krav, om det behövs. Så här gör du:

1. I SSMA för åtkomst väljer **du Verktyg** och sedan **Projektinställningar.** 
1. Välj fliken **Typmappning.** 

   ![Skärmbild av fönstret "Typmappning" i SSMA för åtkomst.](./media/access-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typmappningen för varje tabell genom att välja tabellnamnet i **fönstret Access Metadata Explorer.**


### <a name="convert-the-schema"></a>Konvertera schemat

Om du vill konvertera databasobjekt gör du följande: 

1. Välj **fliken Anslut Azure SQL Database** och gör sedan följande:

   a. Ange information om hur du ansluter till din SQL-databas.  
   b. I listrutan väljer du din SQL-måldatabas. Eller så kan du ange ett nytt namn, vilket innebär att en databas skapas på målservern.  
   c. Ange autentiseringsinformation.   
   d. Välj **Anslut**.

   ![Skärmbild av fönstret "Anslut till Azure SQL Database" där du kan ange anslutningsinformation.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. I fönstret Access Metadata Explorer (Access **Metadata Explorer)** högerklickar du på databasen och väljer **sedan Convert Schema (Konvertera schema).** Du kan också välja din databas och sedan välja **fliken Konvertera** schema.

   ![Skärmbild av kommandot "Konvertera schema" i fönstret "Access Metadata Explorer".](./media/access-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar jämför du de konverterade objekten med de ursprungliga objekten för att identifiera potentiella problem och åtgärda problemen baserat på rekommendationerna.

   ![Skärmbild som visar en jämförelse av de konverterade objekten till källobjekten.](./media/access-to-sql-database-guide/table-comparison.png)

    Jämför den konverterade Transact-SQL-texten med den ursprungliga koden och granska rekommendationerna.

   ![Skärmbild som visar en jämförelse av konverterade frågor till källkoden.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Valfritt) Om du vill konvertera ett enskilt objekt högerklickar du på objektet och väljer **sedan Konvertera schema.** Konverterade objekt visas i fetstil i **Access Metadata Explorer:** 

   ![Skärmbild som visar att objekten i Access Metadata Explorer konverteras.](./media/access-to-sql-database-guide/converted-items.png)
 
1. I **fönstret Utdata** väljer du **ikonen Granska** resultat och granskar felen i **listrutan** Fel. 
1. Spara projektet lokalt för en åtgärdsövning av offlineschemat. Det gör du genom att **välja Arkiv**  >  **Spara projekt.** Detta ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du publicerar dem till din SQL-databas.

## <a name="migrate-the-databases"></a>Migrera databaserna

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser kan du köra migreringsprocessen. Migrering av data är en massinläsningsåtgärd som flyttar rader med data till Azure SQL databas i transaktioner. Antalet rader som ska läsas in i DIN SQL-databas i varje transaktion konfigureras i projektinställningarna.

Om du vill publicera ditt schema och migrera data med hjälp av SSMA för åtkomst gör du följande: 

1. Om du inte redan gjort det väljer du **Anslut för Azure SQL Database** och anger anslutningsinformation. 

1. Publicera schemat. I **fönstret Azure SQL Database Metadata Explorer** högerklickar du på databasen som du arbetar med och väljer sedan **Synkronisera med databas.** Den här åtgärden publicerar MySQL-schemat till SQL-databasen.

1. I fönstret **Synkronisera med databas** granskar du mappningen mellan källprojektet och målet:

   ![Skärmbild av fönstret Synkronisera med databasen för att granska synkroniseringen med databasen.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. I fönstret Access Metadata Explorer (Access **Metadata Explorer)** markerar du kryssrutorna bredvid de objekt som du vill migrera. Om du vill migrera hela databasen markerar du kryssrutan bredvid databasen. 

1. Migrera data. Högerklicka på den databas eller det objekt som du vill migrera och välj sedan **Migrera data.** Du kan också välja fliken **Migrera data** längst upp till höger.  

   Om du vill migrera data för en hel databas markerar du kryssrutan bredvid databasnamnet. Om du vill migrera data från enskilda tabeller expanderar du **databasen,** expanderar Tabeller och markerar sedan kryssrutan bredvid tabellen. Avmarkera kryssrutan om du vill utelämna data från enskilda tabeller.

    ![Skärmbild av kommandot "Migrera data" i fönstret "Access Metadata Explorer".](./media/access-to-sql-database-guide/migrate-data.png)

1. När migreringen är klar kan du visa **datamigreringsrapporten**.  

    ![Skärmbild av fönstret "Migrera datarapport" som visar en exempelrapport för granskning.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Anslut till Azure SQL databas med hjälp [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och verifiera migreringen genom att granska data och schema.

   ![Skärmbild av SQL Server Management Studio Object Explorer för att verifiera migreringen i SSMA.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört  migreringsfasen måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

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
| [Utvärderingsmodell och verktyg för dataarbetsbelastning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Innehåller föreslagna målplattformar för "bästa passning", molnberedskap och program-/databasreparationsnivåer för angivna arbetsbelastningar. Den erbjuder enkel beräkning med ett klick och rapportgenerering som hjälper till att påskynda stora egendomsutvärderingar genom att tillhandahålla en automatiserad, enhetlig beslutsprocess för målplattformen. |

Data SQL-teknikteamet utvecklade dessa resurser. Det här teamets grundstadga är att avblockera och påskynda komplex modernisering för migreringsprojekt för dataplattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

- En matris med Tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas- och datamigrering och specialuppgifter finns i Tjänst och verktyg för [datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkylator för total ägandekostnad i Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementeringscykeln för molnmigrering finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metodtips för kostnad och storleksändring av arbetsbelastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)


- Information om hur du utvärderar programåtkomstlagret finns [i Data Access Migration Toolkit (förhandsversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Information om hur du utför A/B-testning för dataåtkomstlager finns [i Översikt över Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
