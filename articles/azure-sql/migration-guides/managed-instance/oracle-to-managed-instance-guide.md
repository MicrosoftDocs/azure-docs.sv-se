---
title: 'Oracle till Azure SQL-hanterad instans: guide för migrering'
description: I den här guiden får du lära dig att migrera dina Oracle-scheman till Azure SQL-hanterad instans med SQL Server Migration Assistant för Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 4343359e17203fcae538558ebeaa967cfde1540d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640519"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Migration guide: Oracle till Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

I den här guiden får du lära dig att migrera dina Oracle-scheman till Azure SQL-hanterad instans med SQL Server Migration Assistant för Oracle. 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

För att migrera Oracle-schemat till SQL-hanterad instans behöver du: 

- För att verifiera att din käll miljö stöds. 
- För att ladda ned [SQL Server Migration Assistant (SSMA) för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- En mål [Azure SQL-hanterad instans](../../managed-instance/instance-create-quickstart.md). 
- De [behörigheter som krävs för SSMA för Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) och [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven, är du redo att upptäcka miljön och bedöma möjligheten för migreringen. I den här delen av processen ingår att utföra en inventering av de databaser som du behöver migrera, utvärdera dessa databaser för potentiella problem med migrering eller blockerare, och sedan matcha objekt som du kanske har återställt.



### <a name="assess"></a>Utvärdera 

Använd SQL Server Migration Assistant (SSMA) för Oracle om du vill granska databas objekt och data, utvärdera databaser för migrering, migrera databas objekt till Azure SQL-hanterad instans och slutligen migrera data till databasen. 

Följ dessa steg om du vill skapa en utvärdering: 


1. Öppna [SQL Server Migration Assistant för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn, en plats där du vill spara projektet och välj sedan Azure SQL Managed instance som mål för migreringen från List rutan. Välj **OK**:

   ![Nytt projekt](./media/oracle-to-managed-instance-guide/new-project.png)

1. Välj **Anslut till Oracle**. Ange värden för anslutnings information för Oracle i dialog rutan **Anslut till Oracle** :

   ![Anslut till Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

   Välj de Oracle-scheman som du vill migrera: 

   ![Välj Oracle-schema](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Högerklicka på det Oracle-schema som du vill migrera i **Oracle metadata Explorer** och välj sedan **Skapa rapport**. Då skapas en HTML-rapport. Alternativt kan du välja **Skapa rapport** i navigerings fältet när du har valt databasen:

   ![Skapa rapport](./media/oracle-to-managed-instance-guide/create-report.png)

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Oracle-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.

   Exempelvis: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Utvärderings rapport](./media/oracle-to-managed-instance-guide/assessment-report.png)


### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt:

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** :

   ![Typ mappningar](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Oracle metadata Explorer**.

### <a name="convert-schema"></a>Konvertera schema

Följ dessa steg om du vill konvertera schemat: 

1. Valfritt Lägg till dynamiska eller ad-hoc-frågor till instruktioner. Högerklicka på noden och välj sedan **Lägg till instruktioner**.
1. Välj **Anslut till Azure SQL-hanterad instans**. 
    1. Ange anslutnings information för att ansluta din databas i Azure SQL-hanterad instans.
    1. Välj mål databas i list rutan eller ange ett nytt namn, i vilket fall en databas ska skapas på mål servern. 
    1. Ange information om autentisering. 
    1. Välj **Anslut**:

    ![Ansluta till hanterad SQL-instans](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Högerklicka på Oracle-schemat i **Oracle metadata Explorer** och välj sedan **konvertera schema**. Alternativt kan du välja **konvertera schema** från det övre navigerings fältet när du har valt ditt schema:

   ![Konvertera schema](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. När konverteringen är klar kan du jämföra och granska de konverterade objekten till de ursprungliga objekten för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna:

   ![Jämför tabell rekommendationer](./media/oracle-to-managed-instance-guide/table-comparison.png)

   Jämför konverterad Transact-SQL-text till den ursprungliga koden och granska rekommendationerna: 

   ![Jämför procedur rekommendationer](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Välj **gransknings resultat** i fönstret utdata och granska fel i **fel listans** fönster. 
1. Spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du kan publicera schemat till SQL-hanterad instans.

## <a name="migrate"></a>Migrera

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen. Migreringen består av två steg – att publicera schemat och migrera data. 

Följ dessa steg om du vill publicera schemat och migrera dina data:

1. Publicera schemat: Högerklicka på det schema eller objekt som du vill migrera i **Oracle metadata Explorer** och välj **migrera data**. Alternativt kan du välja **migrera data** från det övre navigerings fältet. Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar tabeller och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller:

   ![Synkronisera med databas](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)

   Granska mappningen mellan käll projektet och målet:

   ![Synkronisera med databas granskning](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migrera data: Högerklicka på schemat från **Oracle metadata Explorer** och välj **migrera data**. 

   ![Migrera data](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Ange anslutnings information för både Oracle-och Azure SQL-hanterade instanser.
1. När migreringen är klar kan du Visa **data flyttnings rapporten**:  

   ![Data flyttnings rapport](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Anslut till din Azure SQL-hanterade instans genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och verifiera migreringen genom att granska data och schema:

   ![Validera i SSMA](./media/oracle-to-managed-instance-guide/validate-data.png)


Du kan också använda SQL Server Integration Services (SSIS) för att utföra migreringen. Mer information finns i: 

- [Komma igång med SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS för Azure och hybrid data förflyttning](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört **migreringen** måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

[Verktyget för migrering av data åtkomst](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code som gör att du kan analysera Java-källkoden och identifiera API-anrop och frågor för data åtkomst, vilket ger dig en vy över vad som behöver åtgärdas för att stödja den nya databasens Server del. Mer information finns i [migrera vår java-app från Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) -bloggen. 

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av att utföra följande aktiviteter:

1.  **Utveckla verifieringstester**. Om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.

2.  **Konfigurera test miljö**. Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

3.  **Kör verifierings test**. Kör verifierings testen mot källan och målet och analysera sedan resultaten.

4.  **Kör prestandatester**. Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen.

> [!NOTE]
> Mer information om de här problemen och specifika steg för att minimera dem finns i [guiden för validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process.                                                          |
| [Artefakter för Oracle-inventerings skript](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Den här till gången innehåller en PL/SQL-fråga som visar system tabeller i Oracle och innehåller ett antal objekt efter schema typ, objekt typ och status. Det ger också en grov uppskattning av rå data i varje schema och storleken på tabeller i varje schema, med resultat som lagras i CSV-format.                                                                                                               |
| [Automatisera SSMA för Oracle-utvärdering & konsolidering](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Den här uppsättningen av resurser använder en. csv-fil som post (sources.csv i projektfilerna) för att skapa de XML-filer som behövs för att köra SSMA-utvärdering i konsol läge. source.csv tillhandahålls av kunden baserat på en inventering av befintliga Oracle-instanser. Utdatafilerna är AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml och VariableValueFile.xml.|
| [SSMA för vanliga Oracle-fel och hur du åtgärdar dem](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Med Oracle kan du tilldela ett icke-skalärt villkor i WHERE-satsen. SQL Server stöder dock inte den här typen av villkor. Därför konverteras SQL Server Migration Assistant (SSMA) för Oracle inte frågor med ett icke-skalärt villkor i WHERE-satsen, i stället för att generera ett fel O2SS0001. Den här white paper innehåller mer information om problemet och hur du kan lösa det.          |
| [Hand bok för Oracle to SQL Server-migrering](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Det här dokumentet fokuserar på de uppgifter som är kopplade till migrering av ett Oracle-schema till den senaste versionen av SQL Server Base. Om migreringen kräver ändringar av funktioner eller funktioner, måste den eventuella effekten av varje ändring av de program som använder-databasen betraktas noggrant.                                                     |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL-hanterad instans finns i: 
  - [En översikt över Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Räknare för total ägande kostnad (TCO) för Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) s)

- För video innehåll, se: 
    - [Översikt över migrerings resan och de verktyg/tjänster som rekommenderas för utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)