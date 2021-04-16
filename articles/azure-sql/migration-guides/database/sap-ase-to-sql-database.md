---
title: 'SAP ASE till Azure SQL Database: Migreringsguide'
description: I den här guiden får du lära dig hur du migrerar dina SAP ASE-databaser till en Azure SQL-databas med hjälp av SQL Server Migration Assistant för SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388541"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migreringsguide: SAP ASE till Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här [](https://azure.microsoft.com/migration/migration-journey) guiden får du lära dig hur du migrerar dina SAP Adapter Server Enterprise-databaser (ASE) till en Azure SQL-databas med [hjälp av SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant för SAP Adapter Server Enterprise.

Andra migreringsguider finns i [Azure Database Migration Guide](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Förutsättningar 

Innan du börjar migrera DIN SAP SE-databas till din SQL-databas gör du följande:

- Kontrollera att din källmiljö stöds. 
- Ladda ned och [SQL Server Migration Assistant för SAP Adaptive Server Enterprise (tidigare SAP Sybase ASE).](https://www.microsoft.com/en-us/download/details.aspx?id=54256)
- Kontrollera att du har anslutning och tillräcklig behörighet för att komma åt både källa och mål.

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven är du redo att identifiera topologin för din miljö och utvärdera möjligheten att migrera [Azure-molnet.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Utvärdera

Med [hjälp av SQL Server Migration Assistant (SSMA) för SAP Adaptive Server Enterprise (formellt SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)kan du granska databasobjekt och data, utvärdera databaser för migrering, migrera Sybase-databasobjekt till din SQL-databas och sedan migrera data till SQL-databasen. Mer information finns i [SQL Server Migration Assistant för Sybase (SybaseToSQL).](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)

Skapa en utvärdering genom att göra följande: 

1. Öppna SSMA för Sybase. 
1. Välj **Arkiv** och sedan **Nytt projekt.** 
1. I fönstret **Nytt projekt** anger du ett namn och en  plats för projektet och går sedan till listrutan Migrera till och väljer **Azure SQL Database**. 
1. Välj **OK**.
1. I fönstret **Anslut till Sybase** anger du SAP-anslutningsinformationen. 
1. Högerklicka på den SAP-databas som du vill migrera och välj sedan **Skapa rapport.** Detta genererar en HTML-rapport. Du kan också välja fliken **Skapa rapport** längst upp till höger.
1. Granska HTML-rapporten för att förstå konverteringsstatistiken och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av SAP ASE-objekt och det arbete som krävs för att utföra schemakonverteringar. Standardplatsen för rapporten är i rapportmappen i SSMAProjects. Exempel:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Verifiera typmappningarna

Innan du utför schemakonverteringen verifierar du standardmappningarna av datatyp eller ändrar dem baserat på kraven. Du kan göra det genom att välja **Verktyg** Projektinställningar, eller så kan du ändra typmappningen för varje tabell genom att välja tabellen i  >   **SAP ASE Metadata Explorer.**

### <a name="convert-the-schema"></a>Konvertera schemat

Om du vill konvertera schemat gör du följande:

1. (Valfritt) Om du vill konvertera dynamiska eller specialiserade frågor högerklickar du på noden och väljer sedan Lägg **till instruktion**. 
1. Välj fliken **Anslut Azure SQL Database** och ange sedan information för din SQL-databas. Du kan välja att ansluta till en befintlig databas eller ange ett nytt namn. I så fall skapas en databas på målservern.
1. I **fönstret Sybase Metadata Explorer** högerklickar du på det SAP ASE-schema som du arbetar med och väljer sedan **Konvertera schema.** 
1. När schemat har konverterats kan du jämföra och granska den konverterade strukturen till den ursprungliga strukturen för att identifiera potentiella problem. 

   Efter schemakonverteringen kan du spara projektet lokalt för en åtgärdsövning av offlineschemat. Det gör du genom att **välja Arkiv**  >  **Spara projekt.** Detta ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du publicerar schemat till din SQL-databas.

1. I fönstret **Utdata** väljer **du Granska** resultat och granskar eventuella fel i **fönstret Fellista.** 
1. Spara projektet lokalt för en åtgärdsövning av offlineschemat. Det gör du genom att **välja Arkiv**  >  **Spara projekt.** Detta ger dig möjlighet att utvärdera käll- och målscheman offline och utföra reparation innan du publicerar schemat till din SQL-databas.

## <a name="migrate-the-databases"></a>Migrera databaserna 

När du har de nödvändiga förutsättningarna på plats  och har slutfört uppgifterna som är associerade med fasen före migreringen är du redo att köra schemat och datamigrering.

Gör följande för att publicera schemat och migrera data: 

1. Publicera schemat. I fönstret **Azure SQL Database Metadata Explorer** högerklickar du på databasen och väljer sedan Synkronisera med **databas.** Den här åtgärden publicerar SAP ASE-schemat till din SQL-databas.

1. Migrera data. I fönstret **SAP ASE Metadata Explorer** högerklickar du på den SAP ASE-databas eller det OBJEKT som du vill migrera och väljer sedan Migrera **data.** Du kan också välja fliken **Migrera data** längst upp till höger. 

   Om du vill migrera data för en hel databas markerar du kryssrutan bredvid databasnamnet. Om du vill migrera data från enskilda tabeller expanderar du **databasen,** expanderar Tabeller och markerar sedan kryssrutan bredvid tabellen. Avmarkera kryssrutan om du vill utelämna data från enskilda tabeller. 
1. När migreringen är klar kan du visa **datamigreringsrapporten.** 
1. Validera migreringen genom att granska data och schema. Det gör du genom att ansluta till DIN SQL-databas med [hjälp av SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="post-migration"></a>Efter migreringen 

När du har slutfört  migreringssteget måste du slutföra en serie uppgifter efter migreringen för att säkerställa att allt fungerar så smidigt och effektivt som möjligt.

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


## <a name="next-steps"></a>Nästa steg

- En matris med Tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika databas- och datamigreringsscenarier och specialuppgifter finns i Tjänst och verktyg för [datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkylator för total ägandekostnad i Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Mer information om ramverket och implementeringscykeln för molnmigrering finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metodtips för kostnad och storleksändring av arbetsbelastningar för migrering till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Molnmigrering resurser](https://azure.microsoft.com/migration/resources)

- Information om hur du utvärderar programåtkomstlagret finns [i Data Access Migration Toolkit (förhandsversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Mer information om hur du utför A/B-testning för dataåtkomstlager finns [i Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
