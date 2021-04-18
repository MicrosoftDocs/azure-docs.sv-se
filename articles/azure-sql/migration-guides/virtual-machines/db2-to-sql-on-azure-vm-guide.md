---
title: 'Db2 för SQL Server virtuell Azure-dator: Migreringsguide'
description: I den här guiden lär du dig att migrera dina IBM Db2-databaser till SQL Server virtuella Azure-datorer med hjälp av SQL Server Migration Assistant för Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600173"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Migreringsguide: IBM Db2 för att SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

I den här guiden lär du dig att migrera dina användardatabaser från IBM Db2 till SQL Server på virtuella Azure-datorer med hjälp av SQL Server Migration Assistant för Db2. 

Andra migreringsguider finns i [Azure Database Migration Guides](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Om du vill migrera din Db2-databas SQL Server behöver du:

- Så här kontrollerar du att [källmiljön stöds.](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)
- [SQL Server Migration Assistant (SSMA) för Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Anslutning](../../virtual-machines/windows/ways-to-connect-to-sql.md) mellan din källmiljö och din SQL Server VM i Azure. 
- En [mål-SQL Server på en virtuell Azure-dator](../../virtual-machines/windows/create-sql-vm-portal.md). 

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att identifiera topologin för din miljö och utvärdera migreringens genomförbarhet. 

### <a name="assess"></a>Utvärdera 

Använd SSMA för DB2 för att granska databasobjekt och data och utvärdera databaser för migrering. 

Följ dessa steg om du vill skapa en utvärdering:

1. Öppna [SSMA för Db2.](https://www.microsoft.com/download/details.aspx?id=54254) 
1. Välj **File**  >  **New Project (Arkiv nytt projekt).** 
1. Ange ett projektnamn och en plats där du kan spara projektet. Välj sedan SQL Server mål för migreringen i listrutan och välj **OK.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Skärmbild som visar projektinformation att ange.":::


1. På **Anslut till Db2** anger du värden för Db2-anslutningsinformationen.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Skärmbild som visar alternativ för att ansluta till din Db2-instans.":::


1. Högerklicka på det Db2-schema som du vill migrera och välj **sedan Skapa rapport.** Då genereras en HTML-rapport. Du kan också välja Skapa **rapport i** navigeringsfältet när du har valt schemat.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Skärmbild som visar hur du skapar en rapport.":::

1. Granska HTML-rapporten för att förstå konverteringsstatistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av Db2-objekt och det arbete som krävs för att utföra schemakonverteringar. Standardplatsen för rapporten är i rapportmappen i *SSMAProjects*.

   Exempel: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Skärmbild av rapporten som du granskar för att identifiera eventuella fel eller varningar.":::


### <a name="validate-data-types"></a>Verifiera datatyper

Verifiera standardmappningarna av datatyper och ändra dem baserat på kraven om det behövs. Det gör du på följande sätt: 

1. Välj **Verktyg** på menyn. 
1. Välj **Projektinställningar.** 
1. Välj **fliken Typmappningar.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Skärmbild som visar hur du väljer schema- och typmappning.":::

1. Du kan ändra typmappningen för varje tabell genom att välja tabellen i **Db2 Metadata Explorer**. 

### <a name="convert-schema"></a>Konvertera schema 

Följ dessa steg om du vill konvertera schemat:

1. (Valfritt) Lägg till dynamiska eller ad hoc-frågor i -instruktioner. Högerklicka på noden och välj sedan Lägg **till instruktioner**. 
1. Välj **Anslut för att SQL Server**. 
    1. Ange anslutningsinformation för att ansluta till din instans av SQL Server på din virtuella Azure-dator. 
    1. Välj att ansluta till en befintlig databas på målservern eller ange ett nytt namn för att skapa en ny databas på målservern. 
    1. Ange autentiseringsinformation. 
    1. Välj **Anslut**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Skärmbild som visar den information som behövs för att ansluta till din SQL Server virtuella Azure-dator.":::

1. Högerklicka på schemat och välj sedan **Konvertera schema.** Du kan också välja Konvertera schema **i det** övre navigeringsfältet när du har valt ditt schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Skärmbild som visar hur du väljer schemat och konverterar det.":::

1. När konverteringen är klar kan du jämföra och granska schemats struktur för att identifiera potentiella problem. Åtgärda problemen baserat på rekommendationerna. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Skärmbild som visar en jämförelse och granskning av schemats struktur för att identifiera potentiella problem.":::

1. I fönstret **Utdata** väljer du **Granska resultat.** Granska **felen** i fönstret Fellista. 
1. Spara projektet lokalt för en åtgärdsövning av offlineschemat. Välj **Spara projekt** på **Arkiv-menyn.** Detta ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du kan publicera schemat till SQL Server på virtuella Azure-datorer.

## <a name="migrate"></a>Migrera

När du har utvärderat dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen.

Följ dessa steg om du vill publicera ditt schema och migrera dina data:

1. Publicera schemat. I **SQL Server Metadata Explorer** från **noden Databaser** högerklickar du på databasen. Välj sedan **Synkronisera med databas**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Skärmbild som visar alternativet att synkronisera med databasen.":::

1. Migrera data. Högerklicka på databasen eller objektet som du vill migrera i **Db2 Metadata Explorer** och välj **Migrera data.** Du kan också välja **Migrera data i** navigeringsfältet. Om du vill migrera data för en hel databas markerar du kryssrutan bredvid databasnamnet. Om du vill migrera data från enskilda tabeller expanderar du **databasen,** expanderar Tabeller och markerar sedan kryssrutan bredvid tabellen. Avmarkera kryssrutan om du vill utelämna data från enskilda tabeller.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Skärmbild som visar hur du väljer schemat och väljer att migrera data.":::

1. Ange anslutningsinformation för både Db2- och SQL Server instanserna. 
1. När migreringen är klar kan du visa **datamigreringsrapporten:**  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Skärmbild som visar var du granskar datamigreringsrapporten.":::

1.  Anslut till din instans av SQL Server på en virtuell Azure-dator med [hjälp av SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Validera migreringen genom att granska data och schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Skärmbild som visar en jämförelse av schemat i SQL Server Management Studio.":::

## <a name="post-migration"></a>Efter migreringen 

När migreringen är klar måste du gå igenom en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program 

När data har migrerats till målmiljön måste alla program som tidigare förbrukade källan börja använda målet. Detta kräver i vissa fall ändringar i programmen.

### <a name="perform-tests"></a>Utföra tester

Testningen består av följande aktiviteter:

1. **Utveckla valideringstester:** Om du vill testa databasmigreringen måste du använda SQL-frågor. Du måste skapa valideringsfrågor som ska köras mot både käll- och måldatabaserna. Dina valideringsfrågor bör omfatta det omfång som du har definierat.
1. **Konfigurera testmiljön:** Testmiljön ska innehålla en kopia av källdatabasen och måldatabasen. Se till att isolera testmiljön.
1. **Kör valideringstester:** Kör valideringstesterna mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester:** Kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

## <a name="migration-assets"></a>Migreringstillgångar 

Mer hjälp finns i följande resurser, som har utvecklats för att stödja ett verkligt projekt för migrering:

|Tillgång  |Description  |
|---------|---------|
|[Utvärderingsmodell och verktyg för dataarbetsbelastning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget innehåller föreslagna målplattformar med bästa passning, molnberedskap och program-/databasreparationsnivå för en viss arbetsbelastning. Den erbjuder enkel beräkning med ett klick och rapportgenerering som hjälper till att påskynda stora egendomsutvärderingar genom att tillhandahålla en automatiserad och enhetlig beslutsprocess för målplattformen.|
|[Db2 zOS-datatillgångar – identifierings- och utvärderingspaket](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|När du har kört SQL-skriptet på en databas kan du exportera resultatet till en fil i filsystemet. Flera filformat stöds, inklusive *.csv, så att du kan samla in resultaten i externa verktyg, till exempel kalkylblad. Den här metoden kan vara användbar om du enkelt vill dela resultat med team som inte har Workbench installerat.|
|[IBM Db2 LUW-inventeringsskript och artefakter](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Den här tillgången innehåller en SQL-fråga som träffar IBM Db2 LUW version 11.1-systemtabeller och som tillhandahåller ett antal objekt efter schema och objekttyp, en ungefärlig uppskattning av "rådata" i varje schema och storleksändring av tabeller i varje schema, med resultat som lagras i ett CSV-format.|
|[Db2 LUW pure scale on Azure – installationsguide](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Den här guiden fungerar som en startpunkt för en Db2-implementeringsplan. Även om affärskraven skiljer sig åt gäller samma grundläggande mönster. Det här arkitekturmönstret kan också användas för OLAP-program i Azure.|

Data SQL-teknikteamet utvecklade dessa resurser. Det här teamets grundstadga är att avblockera och påskynda komplex modernisering för migreringsprojekt för dataplattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

Efter migreringen kan du gå igenom [validerings- och optimeringsguiden efter migreringen.](/sql/relational-databases/post-migration-validation-and-optimization-guide) 

Microsoft och tjänster och verktyg från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas- och datamigrering finns i [Datamigreringstjänster och verktyg.](../../../dms/dms-tools-matrix.md)

Videoinnehåll finns i [Översikt över migreringsresan.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
