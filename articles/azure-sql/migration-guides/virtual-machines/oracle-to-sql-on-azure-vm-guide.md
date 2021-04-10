---
title: 'Oracle till SQL Server på Azure Virtual Machines: migreringsguiden'
description: I den här guiden får du lära dig att migrera dina Oracle-scheman till SQL Server på Azure Virtual Machines genom att använda SQL Server Migration Assistant för Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961259"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Guide för migrering: Oracle till SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig att migrera dina Oracle-scheman till SQL Server på Azure Virtual Machines genom att använda SQL Server Migration Assistant för Oracle. 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar 

Om du vill migrera Oracle-schemat till SQL Server på Azure Virtual Machines behöver du:

- En käll miljö som stöds.
- [SQL Server Migration Assistant (SSMA) för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Ett mål [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- De [behörigheter som krävs för SSMA för Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) och [providern](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Anslutning och tillräcklig behörighet för att komma åt källan och målet. 


## <a name="pre-migration"></a>Före migrering

För att förbereda för migrering till molnet kontrollerar du att din käll miljö stöds och att du har åtgärdat alla krav. På så sätt kan du säkerställa en effektiv och lyckad migrering.

Den här delen av processen omfattar: 
- Utföra en inventering av de databaser som du behöver migrera.
- Att utvärdera dessa databaser för potentiella problem med migrering eller blockerare. 
- Lösa eventuella problem som du återställer. 

### <a name="discover"></a>Identifiera

Använd [kart verktyg](https://go.microsoft.com/fwlink/?LinkID=316883) för att identifiera befintliga data källor och information om de funktioner som din verksamhet använder. På så sätt får du en bättre förståelse för migreringen och hjälper dig att planera för den. Den här processen omfattar genomsökning av nätverket för att identifiera organisationens Oracle-instanser och de versioner och funktioner som du använder.

Följ dessa steg om du vill använda kart verktyg för att göra en inventerings genomsökning: 


1. Öppna [kart verktyg](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Välj **skapa/Välj databas**:

   ![Skärm bild som visar alternativet Skapa/Välj databas.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Välj **skapa en inventerings databas**. Ange ett namn för den nya inventerings databasen som du skapar, ange en kort beskrivning och välj sedan **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Skärm bild som visar gränssnittet för att skapa en inventerings databas.":::

1. Välj **samla in inventerings data** för att öppna **guiden inventering och utvärdering**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Skärm bild som visar länken samla in lager data.":::


1. Välj **Oracle** i **inventerings-och utvärderings guiden** och välj sedan **Nästa**:

   ![Skärm bild som visar sidan inventerings scenarier i guiden inventering och utvärdering.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Välj det Sök alternativ för datorn som passar dina affärs behov och din miljö och välj sedan **Nästa**: 

   ![Skärm bild som visar sidan identifierings metoder i inventerings-och utvärderings guiden.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Ange antingen autentiseringsuppgifter eller skapa nya autentiseringsuppgifter för de system som du vill utforska och välj sedan **Nästa**:

    ![Skärm bild som visar sidan alla autentiseringsuppgifter för datorer i inventerings-och utvärderings guiden.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Ange prioritetsordningen för autentiseringsuppgifterna och välj sedan **Nästa**: 

   ![Skärm bild som visar sidan för identifiering av autentiseringsuppgifter i guiden inventering och utvärdering.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Ange autentiseringsuppgifterna för varje dator som du vill identifiera. Du kan använda unika autentiseringsuppgifter för varje dator/dator, eller så kan du använda listan alla datorer som autentiseringsuppgifter.  

   ![Skärm bild som visar sidan Ange datorer och autentiseringsuppgifter i inventerings-och utvärderings guiden.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verifiera dina val och välj sedan **Slutför**:

   ![Skärm bild som visar sidan Sammanfattning i guiden inventering och utvärdering.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. När genomsökningen är klar visar du sammanfattningen av **data insamlingen** . Genomsökningen kan ta några minuter, beroende på antalet databaser. Välj **Stäng** när du är klar: 

   ![Skärm bild som visar sammanfattningen av data insamling.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Välj **alternativ** för att generera en rapport om Oracle-utvärderingen och databas information. Välj båda alternativen en i taget för att generera rapporten.


### <a name="assess"></a>Utvärdera

När du har identifierat data källorna använder [SQL Server Migration Assistant för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) för att utvärdera de Oracle-instanser som migreras till SQL Server VM. Assistenten hjälper dig att förstå luckorna mellan käll-och mål databaserna. Du kan granska databas objekt och data, utvärdera databaser för migrering, migrera databas objekt till SQL Server och sedan migrera data till SQL Server.

Följ dessa steg om du vill skapa en utvärdering: 


1. Öppna [SQL Server Migration Assistant för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. På **Arkiv** -menyn väljer du **nytt projekt**. 
1. Ange ett projekt namn och en plats för projektet och välj sedan ett SQL Server migrerings mål från listan. Välj **OK**: 

   ![Skärm bild som visar dialog rutan nytt projekt.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Välj **Anslut till Oracle**. Ange värden för Oracle-anslutningen i dialog rutan **Anslut till Oracle** :

   ![Skärm bild som visar dialog rutan Anslut till Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Välj de Oracle-scheman som du vill migrera: 

   ![Skärm bild som visar listan över Oracle-scheman som kan migreras.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. I **Oracle metadata Explorer** högerklickar du på det Oracle-schema som du vill migrera och väljer sedan **Skapa rapport**. Om du gör det skapas en HTML-rapport. Alternativt kan du välja databasen och sedan **Skapa rapport** på den översta menyn.

   ![Skärm bild som visar hur du skapar en rapport.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Granska HTML-rapporten för konverterings statistik, fel och varningar. Analysera den för att förstå konverterings problem och lösningar.

    Du kan också öppna rapporten i Excel för att få en inventering av Oracle-objekt och den insats som krävs för att slutföra schema konverteringar. Standard platsen för rapporten är rapportmappen i SSMAProjects. 

   Exempelvis: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Skärm bild som visar en konverterings rapport.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningarna för data typ och ändra dem baserat på krav, om det behövs. Det gör du på följande sätt: 


1. På menyn **verktyg** väljer du **projekt inställningar**. 
1. Välj fliken **typ mappningar** . 

   ![Skärm bild som visar fliken typ mappningar.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Oracle metadata Explorer**. 

### <a name="convert-the-schema"></a>Konvertera schemat

Följ dessa steg om du vill konvertera schemat: 

1. Valfritt Om du vill konvertera dynamiska eller ad hoc-frågor högerklickar du på noden och väljer **Lägg till instruktion**.

1. Välj **Anslut till SQL Server** på den översta menyn. 
     1. Ange anslutnings information för din SQL Server på den virtuella Azure-datorn. 
     1. Välj mål databasen i listan eller ange ett nytt namn. Om du anger ett nytt namn kommer en databas att skapas på mål servern. 
     1. Ange information om autentisering. 
     1. Välj **Anslut**. 


   ![Skärm bild som visar hur du ansluter till SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Högerklicka på Oracle-schemat i **Oracle metadata Explorer** och välj **konvertera schema**. Alternativt kan du välja **konvertera schema** på översta menyn:

   ![Skärm bild som visar hur du konverterar schemat.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. När schema konverteringen är klar granskar du de konverterade objekten och jämför dem med de ursprungliga objekten för att identifiera eventuella problem. Använd rekommendationerna för att lösa eventuella problem:

   ![Skärm bild som visar en jämförelse mellan två scheman.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Jämför konverterad Transact-SQL-text till de ursprungliga lagrade procedurerna och granska rekommendationerna: 

   ![Skärm bild som visar Transact-SQL, lagrade procedurer och en varning.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Du kan spara projektet lokalt för en arbets schema reparation. Det gör du genom att välja **Spara projekt** på **Arkiv** -menyn. Genom att spara projektet lokalt kan du utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat till SQL Server.

1. Välj **gransknings resultat** i fönstret **utdata** och granska sedan fel i **fel listans** fönster. 
1. Spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat till SQL Server på Azure Virtual Machines.


## <a name="migrate"></a>Migrera

När du har de nödvändiga förutsättningarna och har slutfört de uppgifter som är kopplade till fasen innan migreringen, är du redo att starta schemat och datamigreringen. Migreringen består av två steg: publicera schemat och migrera data. 


Följ dessa steg om du vill publicera schemat och migrera data: 

1. Publicera schemat: Högerklicka på databasen i **SQL Server metadata Explorer** och välj **Synkronisera med databas**. Om du gör det publiceras Oracle-schemat till SQL Server på Azure Virtual Machines. 

   ![Skärm bild som visar kommandot Synkronisera med databas.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Granska mappningen mellan käll projektet och målet:

   ![Skärm bild som visar synkroniseringsstatus.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrera data: Högerklicka på databasen eller objektet som du vill migrera i **Oracle metadata Explorer** och välj **migrera data**. Alternativt kan du välja **migrera data** på den översta menyn.

   Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutorna om du vill utelämna data från enskilda tabeller.

   ![Skärm bild som visar kommandot migrera data.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Ange anslutnings information för Oracle och SQL Server på Azure Virtual Machines i dialog rutan.
1. När migreringen är klar kan du Visa **data flyttnings rapporten**:

    ![Skärm bild som visar data flyttnings rapporten.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Anslut till din SQL Server på Azure Virtual Machines-instansen genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Verifiera migreringen genom att granska data och schema:


   ![Skärm bild som visar en SQL Server-instans i SSMA.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

I stället för att använda SSMA kan du använda SQL Server Integration Services (SSIS) för att migrera data. Mer information finns i: 
- Artikeln [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- White paper [SSIS för Azure och hybrid data förflyttning](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Efter migreringen 

När du har slutfört migreringen måste du slutföra en serie uppgifter efter migreringen för att se till att allt körs så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare har använt källan börja använda målet. Att göra dessa ändringar kan kräva ändringar i programmen.

[Verktyget för migrering av data åtkomst](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code. Det gör att du kan analysera Java-källkod och identifiera API-anrop och-frågor för data åtkomst. Verktyget ger en vy över vad som behöver åtgärdas för att stödja den nya databasens Server del. Mer information finns i [migrera ditt Java-program från Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Utför tester

För att testa migreringen av databasen slutför du följande aktiviteter:

1. **Utveckla verifieringstester**. Om du vill testa migreringen av databasen måste du använda SQL-frågor. Skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det omfång som du har definierat.

2. **Konfigurera en test miljö**. Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

3. **Kör verifierings test**. Kör verifierings testen mot källan och målet och analysera sedan resultaten.

4. **Kör prestandatester**. Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera om det är klart. Det är också viktigt att lösa prestanda problem med arbets belastningen.

> [!Note]
> Mer information om de här problemen och de åtgärder som krävs för att minimera dem finns i [guiden för validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Migreringsresurser 

Mer hjälp om hur du slutför det här migrerings scenariot finns i följande resurser som har utvecklats för att stödja ett verkligt migreringsjobb.

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger förslag på bästa anpassning av målspråk, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel beräkning av beräkningar och rapportgenerering som hjälper till att påskynda en stor fastighets bedömning genom att tillhandahålla en automatiserad och enhetlig besluts process för mål plattform.                                                          |
| [Artefakter för Oracle-inventerings skript](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Den här till gången innehåller en PL/SQL-fråga som är riktad mot Oracle system tables och innehåller ett antal objekt efter schema typ, objekt typ och status. Det ger också en grov uppskattning av rå data i varje schema och storleken på tabeller i varje schema, med resultat som lagras i CSV-format.                                                                                                               |
| [Automatisera SSMA för Oracle-utvärdering & konsolidering](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Den här uppsättningen resurser använder en. csv-fil som post (sources.csv i projektfilerna) för att skapa XML-filerna som du behöver för att köra en SSMA-utvärdering i konsol läge. Du anger source.csv-filen genom att göra en förteckning över befintliga Oracle-instanser. Utdatafilerna är AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml och VariableValueFile.xml.|
| [SSMA problem och möjliga lösningar vid migrering av Oracle-databaser](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Med Oracle kan du tilldela ett icke-skalärt villkor i en WHERE-sats. SQL Server stöder inte den här typen av villkor. Så SSMA för Oracle konverterar inte frågor som har ett icke-skalärt villkor i WHERE-satsen. I stället genererar det ett fel: O2SS0001. Den här white paper innehåller information om problemet och hur du kan lösa det.          |
| [Hand bok för Oracle to SQL Server-migrering](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Det här dokumentet fokuserar på de uppgifter som är kopplade till migrering av ett Oracle-schema till den senaste versionen av SQL Server. Om migreringen kräver ändringar av funktioner eller funktioner, måste du noga överväga vilken påverkan varje ändring påverkar för programmen som använder-databasen.                                                     |


Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för projekt med migrering av data plattformar till Microsoft Azure Data plattform.


## <a name="next-steps"></a>Nästa steg

- Om du vill kontrol lera tillgängligheten för de tjänster som är tillämpliga på SQL Server, se [Azures globala infrastruktur Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL finns i:
   - [Distributionsalternativ](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnader och storleks arbets belastningar som migrerats till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om licensiering finns i:
   - [Bring your own license med Azure Hybrid-förmån](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Få kostnads fri utökad support för SQL Server 2008 och SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Om du vill utvärdera program åtkomst lagret använder du för [hands versionen av data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Mer information om hur du gör data åtkomst Layer A/B-testning finns i [Översikt över Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).


