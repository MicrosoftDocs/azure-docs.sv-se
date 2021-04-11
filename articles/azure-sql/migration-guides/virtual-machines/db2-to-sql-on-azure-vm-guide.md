---
title: 'DB2 till SQL Server på Azure VM: migration guide'
description: I den här guiden får du lära dig att migrera dina IBM DB2-databaser till SQL Server på virtuella Azure-datorer genom att använda SQL Server Migration Assistant för DB2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: ff241f468db2e56b73ba10b5621e8a8ab40a19b6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554152"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Guide för migrering: IBM DB2 till SQL Server på virtuell Azure-dator
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

I den här guiden får du lära dig att migrera dina användar databaser från IBM DB2 till SQL Server på virtuella Azure-datorer med hjälp av SQL Server Migration Assistant för DB2. 

Andra guider för migrering finns i hand boken för [Azure Database migration](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna migrera DB2-databasen till SQL Server:

- För att kontrol lera att [käll miljön stöds](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [SQL Server Migration Assistant (SSMA) för DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Anslutning](../../virtual-machines/windows/ways-to-connect-to-sql.md) mellan din käll miljö och din SQL Server VM i Azure. 
- Ett mål [SQL Server på den virtuella Azure-datorn](../../virtual-machines/windows/create-sql-vm-portal.md). 

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för migreringen. 

### <a name="assess"></a>Utvärdera 

Använd SSMA för DB2 för att granska databas objekt och data och utvärdera databaser för migrering. 

Följ dessa steg om du vill skapa en utvärdering:

1. Öppna [SSMA för DB2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Välj **Arkiv**  >  **nytt projekt**. 
1. Ange ett projekt namn och en plats där du vill spara projektet. Välj sedan ett SQL Server migrerings mål i list rutan och välj **OK**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Skärm bild som visar projekt information att ange.":::


1. Ange värden för information om DB2-anslutning vid **Anslut till DB2**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Skärm bild som visar alternativ för att ansluta till DB2-instansen.":::


1. Högerklicka på det DB2-schema som du vill migrera och välj sedan **Skapa rapport**. Då skapas en HTML-rapport. Alternativt kan du välja **Skapa rapport** i navigerings fältet när du har valt schemat.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Skärm bild som visar hur du skapar en rapport.":::

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av DB2-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i *SSMAProjects*.

   Exempel: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Skärm bild av rapporten som du kan granska för att identifiera eventuella fel eller varningar.":::


### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningarna för data typ och ändra dem baserat på krav vid behov. Det gör du på följande sätt: 

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** .

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Skärm bild som visar val av schema och typ mappning.":::

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i den **DB2 metadata Explorer**. 

### <a name="convert-schema"></a>Konvertera schema 

Följ dessa steg om du vill konvertera schemat:

1. Valfritt Lägg till dynamiska eller ad hoc-frågor till instruktioner. Högerklicka på noden och välj sedan **Lägg till instruktioner**. 
1. Välj **Anslut till SQL Server**. 
    1. Ange anslutnings information för att ansluta till din instans av SQL Server på din virtuella Azure-dator. 
    1. Välj att ansluta till en befintlig databas på mål servern eller ange ett nytt namn för att skapa en ny databas på mål servern. 
    1. Ange information om autentisering. 
    1. Välj **Anslut**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Skärm bild som visar den information som behövs för att ansluta till din SQL Server på den virtuella Azure-datorn.":::

1. Högerklicka på schemat och välj sedan **konvertera schema**. Alternativt kan du välja **konvertera schema** från det övre navigerings fältet när du har valt ditt schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Skärm bild som visar hur du väljer schemat och konverterar det.":::

1. När konverteringen är klar kan du jämföra och granska schemats struktur för att identifiera eventuella problem. Åtgärda problemen utifrån rekommendationerna. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Skärm bild som visar jämför och granska schemats struktur för att identifiera eventuella problem.":::

1. I fönstret **utdata** väljer du **gransknings resultat**. Granska fel i fönstret **fel lista** . 
1. Spara projektet lokalt för en arbets schema reparation. Från menyn **Arkiv** väljer du **Spara projekt**. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra åtgärder innan du kan publicera schemat till SQL Server på den virtuella Azure-datorn.

## <a name="migrate"></a>Migrera

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen.

Följ dessa steg om du vill publicera schemat och migrera dina data:

1. Publicera schemat. I **SQL Server metadata Explorer**, i noden **databaser** , högerklickar du på databasen. Välj sedan **Synkronisera med databas**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Skärm bild som visar alternativet för att synkronisera med-databasen.":::

1. Migrera data. Högerklicka på databasen eller objektet som du vill migrera i **DB2 metadata Explorer** och välj **migrera data**. Alternativt kan du välja **migrera data** från navigerings fältet. Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Skärm bild som visar hur du väljer schemat och hur du väljer att migrera data.":::

1. Ange anslutnings information för både DB2-och SQL Server-instanserna. 
1. När migreringen är klar kan du Visa **data migrations rapporten**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Skärm bild som visar var du kan granska data flyttnings rapporten.":::

1.  Anslut till din instans av SQL Server på Azure VM genom att använda [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Verifiera migreringen genom att granska data och schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Skärm bild som visar jämförelse av schemat i SQL Server Management Studio.":::

## <a name="post-migration"></a>Efter migreringen 

När migreringen är klar måste du gå igenom en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program 

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Testningen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
1. **Konfigurera test miljön**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester**: kör prestandatester mot källan och målet och analysera och jämför sedan resultaten.

## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats som stöd för ett verkligt migreringsjobb-projekt engagemang:

|Tillgång  |Description  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process.|
|[Paket för identifiering och utvärdering av DB2 zOS data till gångar](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|När du har kört SQL-skriptet på en databas kan du exportera resultatet till en fil i fil systemet. Flera fil format stöds, inklusive *. csv, så att du kan samla in resultaten i externa verktyg som kalkyl blad. Den här metoden kan vara användbar om du enkelt vill dela resultat med team som inte har Workbench installerat.|
|[IBM DB2 LUW-inventerings skript och artefakter](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Den här till gången innehåller en SQL-fråga som träffar IBM DB2 LUW version 11,1 system tabeller och innehåller ett antal objekt efter schema och objekt typ, en grov uppskattning av "rå data" i varje schema och storleken på tabeller i varje schema med resultat som lagras i CSV-format.|
|[DB2 LUW ren skalning på Azure – installations guide](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Den här guiden fungerar som en utgångs punkt för en plan för DB2-implementering. Även om affärs kraven skiljer sig åt, gäller samma grundläggande mönster. Detta arkitektur mönster kan också användas för OLAP-program på Azure.|

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="next-steps"></a>Nästa steg

Efter migreringen granskar du [guiden för validering och optimering efter migreringen](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

För tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering, se [tjänster och verktyg för data migration](../../../dms/dms-tools-matrix.md).

För video innehåll, se [Översikt över migrerings resan](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
