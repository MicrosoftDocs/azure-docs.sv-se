---
title: 'Oracle till Azure SQL-hanterad instans: guide för migrering'
description: I den här guiden får du lära dig hur du migrerar dina Oracle-scheman till en Azure SQL-hanterad instans med hjälp av SQL Server Migration Assistant för Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553936"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Migration guide: Oracle till Azure SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

I den här guiden får du lära dig hur du migrerar dina Oracle-scheman till en Azure SQL-hanterad instans med hjälp av SQL Server Migration Assistant för Oracle (SSMA för Oracle).

Andra guider för migrering finns i hand boken för [Azure Database migration](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar migrera Oracle-schemat till SQL-hanterad instans:

- Kontrol lera att din käll miljö stöds.
- Ladda ned [SSMA för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Ha ett mål för [SQL-hanterad instans](../../managed-instance/instance-create-quickstart.md) .
- Hämta de [behörigheter som krävs för SSMA för Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) och [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för migreringen. I den här delen av processen ingår att utföra en inventering av de databaser som du behöver migrera, utvärdera dessa databaser för potentiella problem med migrering eller blockerare, och sedan matcha objekt som du kanske har återställt.

### <a name="assess"></a>Utvärdera

Genom att använda SSMA för Oracle kan du granska databas objekt och data, utvärdera databaser för migrering, migrera databas objekt till SQL-hanterad instans och slutligen migrera data till databasen.

Så här skapar du en utvärdering:

1. Öppna [SSMA för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
1. Välj **fil** och välj sedan **nytt projekt**.
1. Ange ett projekt namn och en plats där du vill spara projektet. Välj sedan **Azure SQL-hanterad instans** som mål för migreringen från List rutan och välj **OK**.

   ![Skärm bild som visar nytt projekt.](./media/oracle-to-managed-instance-guide/new-project.png)

1. Välj **Anslut till Oracle**. Ange värden för anslutnings information för Oracle i dialog rutan **Anslut till Oracle** .

   ![Skärm bild som visar Anslut till Oracle.](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Välj de Oracle-scheman som du vill migrera.

   ![Skärm bild som visar val av Oracle-schema.](./media/oracle-to-managed-instance-guide/select-schema.png)

1. I **Oracle metadata Explorer** högerklickar du på det Oracle-schema som du vill migrera och väljer sedan **Skapa rapport** för att generera en HTML-rapport. Alternativt kan du välja en databas och sedan välja fliken **Skapa rapport** .

   ![Skärm bild som visar Skapa rapport.](./media/oracle-to-managed-instance-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Oracle-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.

   Se till exempel `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Skärm bild som visar en utvärderings rapport.](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Verifiera data typerna

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt:

1. I SSMA för Oracle väljer du **verktyg** och väljer sedan **projekt inställningar**.
1. Välj fliken **typ mappning** .

   ![Skärm bild som visar typ mappning.](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Oracle metadata Explorer**.

### <a name="convert-the-schema"></a>Konvertera schemat

Så här konverterar du schemat:

1. Valfritt Lägg till dynamiska eller ad-hoc-frågor till instruktioner. Högerklicka på noden och välj sedan **Lägg till instruktioner**.
1. Välj fliken **Anslut till Azure SQL-hanterad instans** .
    1. Ange anslutnings information för att ansluta databasen i **SQL Database Hanterad instans**.
    1. Välj mål databas i list rutan eller ange ett nytt namn, i vilket fall en databas ska skapas på mål servern.
    1. Ange information om autentisering och välj **Anslut**.

    ![Skärm bild som visar Anslut till Azure SQL-hanterad instans.](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. I **Oracle metadata Explorer** högerklickar du på Oracle-schemat och väljer sedan **konvertera schema**. Alternativt kan du välja schemat och sedan välja fliken **konvertera schema** .

   ![Skärm bild som visar konverterings schema.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. När konverteringen är klar jämför och granskar du de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna.

   ![Skärm bild som visar jämförelse av tabell rekommendationer.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Jämför den konverterade Transact-SQL-texten med den ursprungliga koden och granska rekommendationerna.

   ![Skärm bild som visar jämförelser av procedur rekommendationer.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. I fönstret utdata väljer du **gransknings resultat** och granskar felen i **fellistas** fönstret.
1. Spara projektet lokalt för en arbets schema reparation. På **Arkiv** -menyn väljer du **Spara projekt**. Det här steget ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat till SQL-hanterad instans.

## <a name="migrate"></a>Migrera

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migreringen består av två steg: publicera schemat och migrera data.

Publicera ditt schema och migrera dina data:
1. Publicera schemat genom att högerklicka på databasen från noden **databaser** i **Azure SQL Managed instance metadata Explorer** och välja **Synkronisera med databas**.

   ![Skärm bild som visar synkronisering med databas.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Granska mappningen mellan käll projektet och målet.

   ![Skärm bild som visar synkronisering med databas granskning.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migrera data genom att högerklicka på det schema eller objekt som du vill migrera i **Oracle metadata Explorer** och välja **migrera data**. Alternativt kan du välja fliken **migrera data** . Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutorna bredvid tabellerna. Avmarkera kryss rutorna om du vill utelämna data från enskilda tabeller.

   ![Skärm bild som visar migrera data.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Ange anslutnings information för både Oracle-och SQL-hanterad instans.
1. När migreringen är klar kan du Visa **data flyttnings rapporten**.

   ![Skärm bild som visar data flyttnings rapport.](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Anslut till din instans av SQL-hanterad instans med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)och verifiera migreringen genom att granska data och schema.

   ![Skärm bild som visar verifiering i SSMA för Oracle.](./media/oracle-to-managed-instance-guide/validate-data.png)

Du kan också använda SQL Server Integration Services för att utföra migreringen. Mer information finns i:

- [Komma igång med SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services för Azure och hybrid data förflyttning](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Efter migreringen

När du har slutfört *migreringen* måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Att utföra det här steget kräver ändringar i programmen i vissa fall.

[Verktyget för migrering av data åtkomst](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code som gör att du kan analysera Java-källkoden och identifiera API-anrop och-frågor för data åtkomst. Verktyget ger dig en vy över vad som behöver åtgärdas för att stödja den nya databasens Server del. Mer information finns i [migrera vår java-app från Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blogg inlägg.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
2. **Konfigurera en test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
3. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
4. **Kör prestandatester**: kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

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

- Mer information om SQL-hanterad instans finns i:
  - [En översikt över Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Räknare för total ägande kostnad (TCO) för Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- För video innehåll, se:
    - [Översikt över migrerings resan och de verktyg och tjänster som rekommenderas för att utföra utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
