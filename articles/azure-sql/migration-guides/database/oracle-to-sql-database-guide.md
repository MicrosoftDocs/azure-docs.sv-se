---
title: 'Oracle till Azure SQL Database: migrations guide'
description: I den här guiden får du lära dig hur du migrerar Oracle-schemat till Azure SQL Database med SQL Server Migration Assistant för Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 9ae7e8c4544d2e8bd9dc4ff846aabb0c7f7f9358
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284348"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guide för migrering: Oracle till Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig [hur du migrerar](https://azure.microsoft.com/migration/migration-journey) dina Oracle-scheman till Azure SQL Database med hjälp av [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant för Oracle (SSMA för Oracle).

Andra guider för migrering finns i hand boken för [Azure Database migration](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera Oracle-schemat till SQL Database:

- Kontrol lera att käll miljön stöds.
- Ladda ned [SSMA för Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Ha en mål [SQL Database](../../database/single-database-create-quickstart.md) instans.
- Hämta de [behörigheter som krävs för SSMA för Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) och [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för din [Azure Cloud-migrering](https://azure.microsoft.com/migration). I den här delen av processen ingår att utföra en inventering av de databaser som du behöver migrera, utvärdera dessa databaser för potentiella problem med migrering eller blockerare, och sedan matcha objekt som du kanske har återställt.

### <a name="assess"></a>Utvärdera

Genom att använda SSMA för Oracle kan du granska databas objekt och data, utvärdera databaser för migrering, migrera databas objekt till SQL Database och slutligen migrera data till databasen.

Så här skapar du en utvärdering:

1. Öppna [SSMA för Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Välj **fil** och välj sedan **nytt projekt**.
1. Ange ett projekt namn och en plats där du vill spara projektet. Välj **Azure SQL Database** som migreringsmålet från List rutan och välj **OK**.

   ![Skärm bild som visar Anslut till Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Välj **Anslut till Oracle**. Ange värden för anslutnings information för Oracle i dialog rutan **Anslut till Oracle** .

1. Välj de Oracle-scheman som du vill migrera.

   ![Skärm bild som visar val av Oracle-schema.](./media/oracle-to-sql-database-guide/select-schema.png)

1. I **Oracle metadata Explorer** högerklickar du på det Oracle-schema som du vill migrera och väljer sedan **Skapa rapport** för att generera en HTML-rapport. Alternativt kan du välja en databas och sedan välja fliken **Skapa rapport** .

   ![Skärm bild som visar Skapa rapport.](./media/oracle-to-sql-database-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Oracle-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.

   Se till exempel `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Skärm bild som visar en utvärderings rapport.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Verifiera data typerna

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt:

1. I SSMA för Oracle väljer du **verktyg** och väljer sedan **projekt inställningar**.
1. Välj fliken **typ mappning** .

   ![Skärm bild som visar typ mappning.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Oracle metadata Explorer**.

### <a name="convert-the-schema"></a>Konvertera schemat

Så här konverterar du schemat:

1. Valfritt Lägg till dynamiska eller ad-hoc-frågor till instruktioner. Högerklicka på noden och välj sedan **Lägg till instruktioner**.
1. Välj fliken **Anslut till Azure SQL Database** .
    1. I **SQL Database** anger du anslutnings information för att ansluta databasen.
    1. Välj mål SQL Database instansen i list rutan eller ange ett nytt namn. i så fall skapas en databas på mål servern.
    1. Ange information om autentisering och välj **Anslut**.

    ![Skärm bild som visar Anslut till Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. I **Oracle metadata Explorer** högerklickar du på Oracle-schemat och väljer sedan **konvertera schema**. Alternativt kan du välja schemat och sedan välja fliken **konvertera schema** .

   ![Skärm bild som visar konverterings schema.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. När konverteringen är klar jämför och granskar du de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna.

   ![Skärm bild som visar schemat granska rekommendationer.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Jämför den konverterade Transact-SQL-texten med de ursprungliga lagrade procedurerna och granska rekommendationerna.

   ![Skärm bild som visar gransknings rekommendationerna.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. I fönstret utdata väljer du **gransknings resultat** och granskar felen i **fellistas** fönstret.
1. Spara projektet lokalt för en arbets schema reparation. På **Arkiv** -menyn väljer du **Spara projekt**. Det här steget ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat till SQL Database.

## <a name="migrate"></a>Migrera

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migreringen består av två steg: publicera schemat och migrera data.

Publicera ditt schema och migrera dina data:

1. Publicera schemat genom att högerklicka på databasen från noden **databaser** i **Azure SQL Database metadata Explorer** och välja **Synkronisera med databas**.

   ![Skärm bild som visar synkronisering med databas.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Granska mappningen mellan käll projektet och målet.

   ![Skärm bild som visar synkronisering med databas granskning.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrera data genom att högerklicka på databasen eller objektet som du vill migrera i **Oracle metadata Explorer** och välja **migrera data**. Alternativt kan du välja fliken **migrera data** . Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutorna bredvid tabellerna. Avmarkera kryss rutorna om du vill utelämna data från enskilda tabeller.

   ![Skärm bild som visar migrera data.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Ange anslutnings information för både Oracle och SQL Database.
1. När migreringen är klar kan du Visa **data flyttnings rapporten**.

   ![Skärm bild som visar data flyttnings rapporten.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Anslut till din SQL Database-instans genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och verifiera migreringen genom att granska data och schema.

   ![Skärm bild som visar verifiering i SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Du kan också använda SQL Server Integration Services för att utföra migreringen. Mer information finns i:

- [Komma igång med SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services för Azure och hybrid data förflyttning](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Efter migreringen

När du har slutfört *migreringen* måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. För att kunna utföra den här uppgiften krävs ändringar av programmen i vissa fall.

[Verktyget för migrering av data åtkomst](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code som gör att du kan analysera Java-källkoden och identifiera API-anrop och-frågor för data åtkomst. Verktyget ger dig en vy över vad som behöver åtgärdas för att stödja den nya databasens Server del. Mer information finns i [migrera dina Java-program från Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) -blogg inlägg.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
1. **Konfigurera en test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester**: kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att stämma av data precisions problem, kontrol lera att de är klara och åtgärda prestanda problem med arbets belastningen.

> [!NOTE]
> Mer information om de här problemen och stegen för att minimera dem finns i guiden för [validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migrera till gångar

Mer hjälp om hur du slutför det här scenariot för migrering finns i följande resurser. De har utvecklats för att ge stöd till ett verkligt projekt engagemang för migrering.

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program-eller databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad och enhetlig mål plattforms besluts process.                                                          |
| [Artefakter för Oracle-inventerings skript](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Den här till gången innehåller en PL/SQL-fråga som visar system tabeller i Oracle och innehåller ett antal objekt efter schema typ, objekt typ och status. Det ger också en grov uppskattning av rå data i varje schema och storleken på tabeller i varje schema, med resultat som lagras i CSV-format.                                                                                                               |
| [Automatisera SSMA för Oracle-utvärdering & konsolidering](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Den här uppsättningen resurser använder en. csv-fil som post (sources.csv i projektfilerna) för att skapa XML-filer som behövs för att köra en SSMA-utvärdering i konsol läge. source.csv tillhandahålls av kunden baserat på en inventering av befintliga Oracle-instanser. Utdatafilerna är AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml och VariableValueFile.xml.|
| [SSMA för vanliga Oracle-fel och hur du åtgärdar dem](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Med Oracle kan du tilldela ett nonscalar-villkor i WHERE-satsen. SQL Server stöder dock inte den här typen av villkor. Därför konverteras inte SSMA för Oracle frågor med ett nonscalar-villkor i WHERE-satsen. I stället genererar det fel O2SS0001. Den här white paper innehåller mer information om problemet och hur du kan lösa det.          |
| [Hand bok för Oracle to SQL Server-migrering](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Det här dokumentet fokuserar på de uppgifter som är kopplade till migrering av ett Oracle-schema till den senaste versionen av SQL Server Database. Om migreringen kräver ändringar av funktioner eller funktioner, måste den eventuella effekten av varje ändring av de program som använder-databasen beaktas noggrant.                                                     |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

- En matris med tjänster och verktyg från Microsoft och tjänster från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om SQL Database finns i:
  - [En översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Räknare för total ägande kostnad (TCO) för Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)

- För video innehåll, se:
    - [Översikt över migrerings resan och de verktyg och tjänster som rekommenderas för att utföra utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
