---
title: 'SAP-ASE till Azure SQL Database: migration guide'
description: I den här guiden får du lära dig hur du migrerar dina SAP ASE-databaser till en Azure SQL-databas med hjälp av SQL Server Migration Assistant för SAP adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: c60d6ba1f4d3628f57b8149779318c3e049a9e24
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284246"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migration guide: SAP ASE till Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig [hur du migrerar](https://azure.microsoft.com/migration/migration-journey) dina SAP adapter Server Enterprise-databaser (ASE) till en Azure SQL-databas med hjälp av [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant för SAP adapter Server Enterprise.

Andra guider för migrering finns i [Guide för Azure Database migration](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar 

Innan du börjar migrera SAP SE-databasen till SQL-databasen gör du följande:

- Kontrol lera att käll miljön stöds. 
- Hämta och installera [SQL Server Migration Assistant för SAP adaptiv Server Enterprise (tidigare SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Se till att du har anslutning och tillräcklig behörighet för att få åtkomst till både källa och mål.

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att upptäcka miljön och bedöma möjligheten för din [Azure Cloud-migrering](https://azure.microsoft.com/migration).

### <a name="assess"></a>Utvärdera

Genom att använda [SQL Server Migration Assistant (SSMA) för SAP adaptiv Server Enterprise (formellt SAP Sybase-ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)kan du granska databas objekt och data, utvärdera databaser för migrering, migrera Sybase-databas-objekt till din SQL-databas och sedan migrera data till SQL-databasen. Läs mer i [SQL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Gör så här för att skapa en utvärdering: 

1. Öppna SSMA för Sybase. 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. I fönstret **nytt projekt** anger du ett namn och en plats för projektet. i list rutan **migrera till** väljer du **Azure SQL Database**. 
1. Välj **OK**.
1. I fönstret **Anslut till Sybase** anger du information om SAP-anslutningen. 
1. Högerklicka på SAP-databasen som du vill migrera och välj sedan **Skapa rapport**. Detta genererar en HTML-rapport. Alternativt kan du välja fliken **Skapa rapport** längst upp till höger.
1. Granska HTML-rapporten för att förstå konverterings statistiken och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av SAP ASE-objekt och den ansträngning som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects. Exempel:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Verifiera typ mappningarna

Innan du utför schema konvertering ska du validera standard mappningarna för data typ eller ändra dem baserat på kraven. Du kan göra detta genom att välja **verktyg**  >  **projekt inställningar**, eller så kan du ändra typ mappningen för varje tabell genom att välja tabellen i **SAP ASE metadata Explorer**.

### <a name="convert-the-schema"></a>Konvertera schemat

Om du vill konvertera schemat gör du följande:

1. Valfritt Om du vill konvertera dynamiska eller specialiserade frågor högerklickar du på noden och väljer sedan **Lägg till instruktion**. 
1. Välj fliken **Anslut till Azure SQL Database** och ange sedan informationen för din SQL-databas. Du kan välja att ansluta till en befintlig databas eller ange ett nytt namn, vilket innebär att en databas skapas på mål servern.
1. I fönstret **Sybase metadata Explorer** högerklickar du på det SAP ASE-schema som du arbetar med och väljer sedan **konvertera schema**. 
1. När schemat har konverterats kan du jämföra och granska den omvandlade strukturen till den ursprungliga strukturen identifiera potentiella problem. 

   Efter schema konverteringen kan du spara projektet lokalt för en arbets schema reparation. Det gör du genom att välja **Arkiv**  >  **Spara projekt**. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat i SQL-databasen.

1. I fönstret **utdata** väljer du **gransknings resultat** och granskar eventuella fel i **fel List** fönstret. 
1. Spara projektet lokalt för en arbets schema reparation. Det gör du genom att välja **Arkiv**  >  **Spara projekt**. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du publicerar schemat i SQL-databasen.

## <a name="migrate-the-databases"></a>Migrera databaserna 

När du har de nödvändiga förutsättningarna och har slutfört de uppgifter som är kopplade till fasen *innan migreringen* , är du redo att köra schemat och datamigreringen.

Så här publicerar du schemat och migrerar data: 

1. Publicera schemat. I fönstret **Azure SQL Database metadata Explorer** högerklickar du på databasen och väljer sedan **Synkronisera med databas**. Den här åtgärden publicerar SAP ASE-schemat till din SQL-databas.

1. Migrera data. I fönstret **SAP ASE metadata Explorer** högerklickar du på den SAP ASE-databas eller det objekt som du vill migrera och väljer sedan **migrera data**. Alternativt kan du välja fliken **migrera data** längst upp till höger. 

   Om du vill migrera data för en hel databas markerar du kryss rutan bredvid databas namnet. Om du vill migrera data från enskilda tabeller expanderar du databasen, expanderar **tabeller** och markerar sedan kryss rutan bredvid tabellen. Avmarkera kryss rutan om du vill utelämna data från enskilda tabeller. 
1. När migreringen är klar kan du Visa **data flyttnings rapporten**. 
1. Verifiera migreringen genom att granska data och schema. Det gör du genom att ansluta till din SQL-databas med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Nästa steg

- En matris med tjänster och verktyg från Microsoft och tjänster från tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering och särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för att kostnads fritt och ändra storlek på arbets belastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
