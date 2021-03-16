---
title: 'Oracle till SQL Server på Azure VM: migration guide'
description: Den här guiden lär dig att migrera dina Oracle-scheman till SQL Server på virtuella Azure-datorer med SQL Server Migration Assistant för Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f9b6dea216e05bb645daf5fdd041cec692821af8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565285"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Migration guide: Oracle till SQL Server på Azure VM
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Den här guiden lär dig att migrera dina Oracle-scheman till SQL Server på virtuella Azure-datorer med SQL Server Migration Assistant för Oracle. 

För andra scenarier, se [Guide för databas migrering](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Förutsättningar 

Om du vill migrera Oracle-schemat till SQL Server på den virtuella Azure-datorn behöver du:

- För att verifiera att din käll miljö stöds.
- För att ladda ned [SQL Server Migration Assistant (SSMA) för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Ett mål [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- De [behörigheter som krävs för SSMA för Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) och [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Före migrering

När du förbereder för migrering till molnet kontrollerar du att din käll miljö stöds och att du har åtgärdat eventuella krav. På så sätt kan du säkerställa en effektiv och lyckad migrering.

I den här delen av processen ingår att utföra en inventering av de databaser som du behöver migrera, utvärdera dessa databaser för potentiella problem med migrering eller blockerare, och sedan matcha objekt som du kanske har återställt. 

### <a name="discover"></a>Identifiera

Använd [kart verktyget](https://go.microsoft.com/fwlink/?LinkID=316883) för att identifiera befintliga data källor och information om de funktioner som används av din verksamhet för att få en bättre förståelse för och planera för migreringen. Den här processen omfattar genomsökning av nätverket för att identifiera alla organisationens Oracle-instanser tillsammans med den version och de funktioner som används.

Följ dessa steg om du vill använda kart verktyget för att utföra en inventerings genomsökning: 

1. Öppna [kart verktyget](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Välj **skapa/Välj databas**.
1. Välj **skapa en inventerings databas**, ange ett namn för den nya inventerings databasen som du skapar, ange en kort beskrivning och välj sedan **OK**. 
1. Välj **samla in inventerings data** för att öppna **inventerings-och utvärderings guiden**. 
1. I **inventerings-och utvärderings guiden** väljer du **Oracle** och väljer sedan **Nästa**. 
1. Välj det Sök alternativ för datorn som passar dina affärs behov och din miljö och välj sedan **Nästa**: 
1. Ange antingen autentiseringsuppgifter eller skapa nya autentiseringsuppgifter för de system som du vill utforska och välj sedan **Nästa**.
1. Ange ordningen för autentiseringsuppgifterna och välj sedan **Nästa**. 
1. Ange autentiseringsuppgifterna för varje dator som du vill identifiera. Du kan använda unika autentiseringsuppgifter för varje dator/dator, eller så kan du välja att använda listan **alla autentiseringsuppgifter för datorn** .  
1. Verifiera din val Sammanfattning och välj sedan **Slutför**.
1. När sökningen är klar kan du Visa sammanfattnings rapporten för **data insamling** . Genomsökningen tar några minuter och beror på antalet databaser. Välj **Stäng** när du är färdig. 
1. Välj **alternativ** för att generera en rapport om Oracle-utvärderingen och databas information. Välj båda alternativen (ett i taget) för att generera rapporten.


### <a name="assess"></a>Utvärdera

När du har identifierat data källorna använder du [SQL Server Migration Assistant (SSMA) för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) för att utvärdera de Oracle-instanser som migreras till SQL Server VM så att du förstår luckorna mellan de två. Med hjälp av Migration Assistant kan du granska databas objekt och data, utvärdera databaser för migrering, migrera databas objekt till SQL Server och sedan migrera data till SQL Server.

Följ dessa steg om du vill skapa en utvärdering: 

1. Öppna  [SQL Server Migration Assistant (SSMA) för Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn, en plats där du vill spara projektet och välj sedan ett SQL Server migrerings mål från List rutan. Välj **OK**. 
1. Ange värden för information om Oracle-anslutning i dialog rutan **Anslut till Oracle** .
1. Högerklicka på det Oracle-schema som du vill migrera i **Oracle metadata Explorer** och välj sedan **Skapa rapport**. Då skapas en HTML-rapport. Alternativt kan du välja **Skapa rapport** i navigerings fältet när du har valt databasen.

1. I **Oracle metadata Explorer** väljer du Oracle-schemat och väljer sedan **Skapa rapport** för att generera en HTML-rapport med konverterings statistik och fel/varningar, om det finns några.
1. Granska HTML-rapporten för konverterings statistik, samt fel och varningar. Analysera den för att förstå konverterings problem och lösningar.

   Den här rapporten kan också nås från mappen SSMA-projekt som har marker ATS på den första skärmen. I exemplet ovan letar du upp report.xml-filen från: 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    och öppna den i Excel för att få en inventering av Oracle-objekt och den insats som krävs för att utföra schema konverteringar.


### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt: 

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** . 
1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i **Oracle metadata Explorer**. 



### <a name="convert-schema"></a>Konvertera schema

Följ dessa steg om du vill konvertera schemat: 

1. Valfritt Om du vill konvertera dynamiska eller ad hoc-frågor högerklickar du på noden och väljer **Lägg till instruktion**.
1. Välj **Anslut till SQL Server** från det övre navigerings fältet och ange anslutnings information för din SQL Server på den virtuella Azure-datorn. Du kan välja att ansluta till en befintlig databas eller ange ett nytt namn, vilket innebär att en databas skapas på mål servern.
1. Högerklicka på schemat och välj **konvertera schema**.
1. När schemat har konverterats kan du jämföra och granska schemats struktur för att identifiera eventuella problem.

   Du kan spara projektet lokalt för en arbets schema reparation. Du kan göra det genom att välja **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du kan publicera schemat till SQL Server.


## <a name="migrate"></a>Migrera

När du har de nödvändiga förutsättningarna och har slutfört de uppgifter som är kopplade till fasen **innan migreringen** , är du redo att utföra schemat och datamigreringen. Migreringen består av två steg – att publicera schemat och migrera data. 


Följ dessa steg om du vill publicera schemat och migrera data: 

1. Högerklicka på databasen i **SQL Server metadata Explorer**  och välj **Synkronisera med databas**. Den här åtgärden publicerar Oracle-schemat till SQL Server på den virtuella Azure-datorn. 
1. Högerklicka på Oracle-schemat från **Oracle metadata Explorer** och välj **migrera data**. Alternativt kan du välja Migrera data från den översta navigerings raden.
1. Ange anslutnings information för Oracle och SQL Server på den virtuella Azure-datorn i dialog rutan.
1. När migreringen är klar kan du visa data flyttnings rapporten:
1. Anslut till din SQL Server på den virtuella Azure-datorn med [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) för att granska data och schema på din SQL Server instans. 


Förutom att använda SSMA kan du också använda SQL Server Integration Services (SSIS) för att migrera data. Mer information finns i: 
- Artikeln [komma igång med SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- White paper [SQL Server Integration Services: SSIS för Azure och hybrid data förflyttning](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Efter migreringen 

När du har slutfört **migreringen** måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

[Verktyget för migrering av data åtkomst](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) är ett tillägg för Visual Studio Code som gör att du kan analysera Java-källkoden och identifiera API-anrop och frågor för data åtkomst, vilket ger dig en vy över vad som behöver åtgärdas för att stödja den nya databasens Server del. Mer information finns i [migrera vår java-app från Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) -bloggen. 

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av att utföra följande aktiviteter:

1. **Utveckla verifieringstester**. Om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.

2. **Konfigurera test miljö**. Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

3. **Kör verifierings test**. Kör verifierings testen mot källan och målet och analysera sedan resultaten.

4. **Kör prestandatester**. Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen.

> [!Note]
> Mer information om de här problemen och specifika steg för att minimera dem finns i [guiden för validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| **Rubrik/länk**                                                                                                                                          | **Beskrivning**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process.                                                          |
| [Artefakter för Oracle-inventerings skript](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Den här till gången innehåller en PL/SQL-fråga som visar system tabeller i Oracle och innehåller ett antal objekt efter schema typ, objekt typ och status. Det ger också en grov uppskattning av rå data i varje schema och storleken på tabeller i varje schema, med resultat som lagras i CSV-format.                                                                                                               |
| [Automatisera SSMA för Oracle-utvärdering & konsolidering](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Den här uppsättningen av resurser använder en. csv-fil som post (sources.csv i projektfilerna) för att skapa de XML-filer som behövs för att köra SSMA-utvärdering i konsol läge. source.csv tillhandahålls av kunden baserat på en inventering av befintliga Oracle-instanser. Utdatafilerna är AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml och VariableValueFile.xml.|
| [SSMA för vanliga Oracle-fel och hur du åtgärdar dem](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Med Oracle kan du tilldela ett icke-skalärt villkor i WHERE-satsen. SQL Server stöder dock inte den här typen av villkor. Därför konverteras SQL Server Migration Assistant (SSMA) för Oracle inte frågor med ett icke-skalärt villkor i WHERE-satsen, i stället för att generera ett fel O2SS0001. Den här white paper innehåller mer information om problemet och hur du kan lösa det.          |
| [Hand bok för Oracle to SQL Server-migrering](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Det här dokumentet fokuserar på de uppgifter som är kopplade till migrering av ett Oracle-schema till den senaste versionen av SQL-serverbase. Om migreringen kräver ändringar av funktioner eller funktioner, måste den eventuella effekten av varje ändring av de program som använder-databasen betraktas noggrant.                                                     |

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.

## <a name="next-steps"></a>Nästa steg

- För att kontrol lera tillgängligheten för tjänster som är tillämpliga på SQL Server, se [Azures globala infrastruktur Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om Azure SQL finns i:
   - [Distributionsalternativ](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server på virtuella Azure-datorer](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om licensiering finns i
   - [Bring your own license med Azure Hybrid-förmån](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Få kostnads fri utökad support för SQL Server 2008 och SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).

