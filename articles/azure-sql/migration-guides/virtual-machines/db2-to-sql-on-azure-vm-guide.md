---
title: DB2 till SQL Server på virtuella Azure-datorer (migration guide)
description: Följ den här guiden för att migrera DB2-servern till SQL Server på virtuella Azure-datorer.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: c3f3af6a1c114d6587992f79e95dcc0c1ccb41e6
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797478"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Migreringsguiden: DB2 till SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Den här migreringsguiden lär dig att migrera dina användar databaser från DB2 till SQL Server på virtuella Azure-datorer med SQL Server Migration Assistant för DB2. 

Mer information om andra biflyttnings guider finns i [databas migrering](https://datamigration.microsoft.com/). 


## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna migrera DB2-databasen till SQL Server:

- för att verifiera att din käll miljö stöds.
- [SQL Server Migration Assistant (SSMA) för DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Anslutning](../../virtual-machines/windows/ways-to-connect-to-sql.md) mellan din käll miljö och din SQL Server VM i Azure. 



## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven, är du redo att upptäcka miljön och bedöma möjligheten för migreringen. 

### <a name="assess"></a>Utvärdera 

Skapa en utvärdering med SQL Server Migration Assistant (SSMA). 

Följ dessa steg om du vill skapa en utvärdering:

1. Öppna SQL Server Migration Assistant (SSMA) för DB2. 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn, en plats där du vill spara projektet och välj sedan ett SQL Server migrerings mål från List rutan. Välj **OK**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Ange projekt information och välj OK för att spara.":::


1. Ange värden för informationen om DB2-anslutningen i dialog rutan **Anslut till DB2** . 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-db2.png" alt-text="Anslut till DB2-instansen":::


1. Högerklicka på det DB2-schema som du vill migrera och välj sedan **Skapa rapport**. Då skapas en HTML-rapport. Alternativt kan du välja **Skapa rapport** i navigerings fältet när du har valt schemat. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Högerklicka på schemat och välj Skapa rapport":::

1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av DB2-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.

   Exempel: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Granska rapporten för att identifiera eventuella fel eller varningar":::


### <a name="validate-data-types"></a>Verifiera data typer

Validera standard mappningar för data typer och ändra dem baserat på krav vid behov. Det gör du på följande sätt: 

1. Välj **verktyg** på menyn. 
1. Välj **projekt inställningar**. 
1. Välj fliken **typ mappningar** . 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Välj schemat och skriv sedan mappning":::

1. Du kan ändra typ mappningen för varje tabell genom att välja tabellen i den **DB2 metadata Explorer**. 

### <a name="convert-schema"></a>Konvertera schema 

Följ dessa steg om du vill konvertera schemat:

1. Valfritt Lägg till dynamiska eller ad-hoc-frågor till instruktioner. Högerklicka på noden och välj sedan **Lägg till instruktioner**. 
1. Välj **Anslut till SQL Server**. 
    1. Ange anslutnings information för att ansluta till din SQL Server-instans på din virtuella Azure-dator. 
    1. Välj att ansluta till en befintlig databas på mål servern eller ange ett nytt namn för att skapa en ny databas på mål servern. 
    1. Välj **Anslut**. 

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Anslut till din SQL Server på den virtuella Azure-datorn":::


1. Högerklicka på schemat och välj sedan **konvertera schema**. Alternativt kan du välja **konvertera schema** från det övre navigerings fältet när du har valt ditt schema. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Högerklicka på schemat och välj Konvertera schema":::

1. När konverteringen är klar kan du jämföra och granska schemats struktur för att identifiera potentiella problem och åtgärda dem utifrån rekommendationerna. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Jämför och granska schemats struktur för att identifiera potentiella problem och åtgärda dem utifrån rekommendationer.":::

1. Spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. 


## <a name="migrate"></a>Migrera

När du har slutfört utvärderingen av dina databaser och åtgärdat eventuella avvikelser är nästa steg att köra migreringsprocessen.

Följ dessa steg om du vill publicera schemat och migrera dina data:

1. Publicera schemat: Högerklicka på databasen från noden **databaser** i **SQL Server metadata Explorer** och välj **Synkronisera med databas**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Högerklicka på databasen och välj synkronisera med databas":::

1. Migrera data: Högerklicka på schemat från den **DB2 metadata Explorer** och välj **migrera data**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Högerklicka på schemat och välj migrera data":::

1. Ange anslutnings information för både DB2-och SQL Server-instanserna. 
1. Visa **data migrations rapporten**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Granska data migrations rapporten":::

1. Anslut till din SQL Server-instans genom att använda SQL Server Management Studio och verifiera migreringen genom att granska data och schema. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Jämför schemat i SSMS":::

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört migreringen måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program 

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av följande aktiviteter:

1. **Utveckla verifieringstester**: om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.
1. **Konfigurera test miljö**: test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.
1. **Kör verifieringstester**: kör verifieringstester mot källan och målet och analysera sedan resultaten.
1. **Kör prestandatester**: kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

   > [!NOTE]
   > Om du behöver hjälp med att utveckla och köra verifierings test efter migrering bör du tänka på vilken data kvalitets lösning som är tillgänglig från partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 

## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats som stöd för ett verkligt migreringsjobb-projekt engagemang:

|Tillgång  |Description  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process.|
|[Paket för identifiering och utvärdering av DB2 zOS data till gångar](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|När du har kört SQL-skriptet på en databas kan du exportera resultatet till en fil i fil systemet. Flera fil format stöds, inklusive *. csv, så att du kan samla in resultaten i externa verktyg som kalkyl blad. Den här metoden kan vara användbar om du enkelt vill dela resultat med team som inte har Workbench installerat.|
|[IBM DB2 LUW-inventerings skript och artefakter](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Den här till gången innehåller en SQL-fråga som träffar system tabeller i IBM DB2 LUW version 11,1 och innehåller ett antal objekt efter schema och objekt typ, en grov uppskattning av rå data i varje schema och storleken på tabeller i varje schema med resultat som lagras i CSV-format.|
|[DB2 LUW ren skalning på Azure – installations guide](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Den här guiden fungerar som en utgångs punkt för en plan för DB2-implementering. Samma grundläggande mönster gäller även om affärs kraven varierar. Detta arkitektur mönster kan också användas för OLAP-program på Azure.|

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.

## <a name="next-steps"></a>Nästa steg

Efter migreringen granskar du [guiden för validering och optimering efter migreringen](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering, samt särskilda uppgifter, se [tjänster och verktyg för data migration](../../../dms/dms-tools-matrix.md).

Mer information om andra biflyttnings guider finns i [databas migrering](https://datamigration.microsoft.com/). 

För video innehåll, se:
- [Använda Database migration guide](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)
- [Översikt över migrerings resan](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)