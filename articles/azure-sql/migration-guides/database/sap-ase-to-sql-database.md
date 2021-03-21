---
title: 'SAP-ASE till Azure SQL Database: migration guide'
description: I den här guiden får du lära dig att migrera dina SAP ASE-databaser till Azure SQL Database att använda SQL Server Migration Assistant för SAP adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565336"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migration guide: SAP ASE till Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig att migrera dina SAP ASE-databaser till Azure SQL Database att använda SQL Server Migration Assistant för SAP adapter Server Enterprise.

Mer information om andra biflyttnings guider finns i [databas migrering](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Förutsättningar 

Om du vill migrera din SAP SE-databas till Azure SQL Database behöver du:

- för att verifiera att din käll miljö stöds. 
- [SQL Server Migration Assistant for SAP adaptiv Server Enterprise (tidigare SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Före migrering

När du har uppfyllt kraven, är du redo att upptäcka miljön och bedöma möjligheten för migreringen.

### <a name="assess"></a>Utvärdera

Använd [SQL Server Migration Assistant (SSMA) för SAP adaptiv Server Enterprise (formellt SAP Sybase-ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) för att granska databas objekt och data, utvärdera databaser för migrering, migrera Sybase-databas-objekt till Azure SQL Database och sedan migrera data till Azure SQL Database. Läs mer i [SQL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Följ dessa steg om du vill skapa en utvärdering: 

1. Öppna **SSMA för Sybase**. 
1. Välj **fil** och välj sedan **nytt projekt**. 
1. Ange ett projekt namn, en plats där du vill spara projektet och välj sedan Azure SQL Database som mål för migreringen från List rutan. Välj **OK**.
1. Ange värden för SAP-anslutnings information i dialog rutan **Anslut till Sybase** . 
1. Högerklicka på SAP-databasen som du vill migrera och välj sedan **Skapa rapport**. Detta genererar en HTML-rapport.
1. Granska HTML-rapporten för att förstå konverterings statistik och eventuella fel eller varningar. Du kan också öppna rapporten i Excel för att få en inventering av DB2-objekt och den insats som krävs för att utföra schema konverteringar. Standard platsen för rapporten finns i rapportmappen i SSMAProjects.

   Exempel: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Validera typ mappningar

Innan du utför schema konverteringen verifierar du standard typ mappningar eller ändrar dem baserat på kraven. Du kan göra detta genom att navigera till **verktyg** -menyn och välja **projekt inställningar** eller ändra typ mappningen för varje tabell genom att välja tabellen i **SAP ASE metadata Explorer**.


### <a name="convert-schema"></a>Konvertera schema

Följ dessa steg om du vill konvertera schemat:

1. Valfritt Om du vill konvertera dynamiska eller ad hoc-frågor högerklickar du på noden och väljer **Lägg till instruktion**. 
1. Välj **Anslut till Azure SQL Database** i det övre navigerings fältet och ange Azure SQL Database information. Du kan välja att ansluta till en befintlig databas eller ange ett nytt namn, vilket innebär att en databas skapas på mål servern.
1. Högerklicka på SAP ASE-schemat i listan för **Sybase metadata** och välj **konvertera schema**. Alternativt kan du välja **konvertera schema** från det övre navigerings fältet. 
1. Jämför och granska schemats struktur för att identifiera eventuella problem. 

   Efter schema konverteringen kan du spara projektet lokalt för en arbets schema reparation. Välj **Spara projekt** på **Arkiv** -menyn. Det ger dig möjlighet att utvärdera käll-och mål scheman offline och utföra reparation innan du kan publicera schemat till Azure SQL Database.

Läs mer i [konvertera schema](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrera 

När du har de nödvändiga förutsättningarna och har slutfört de uppgifter som är kopplade till fasen **innan migreringen** , är du redo att utföra schemat och datamigreringen.

Följ dessa steg om du vill publicera schemat och migrera data: 

1. Högerklicka på databasen i **Azure SQL Database metadata Explorer** och välj **Synkronisera med databas**.  Den här åtgärden publicerar SAP ASE-schemat till Azure SQL Database-instansen.
1. Högerklicka på SAP ASE-schemat i **SAP ASE metadata Explorer** och välj **migrera data**.  Alternativt kan du välja **migrera data** från det övre navigerings fältet.  
1. När migreringen är klar kan du Visa **data flyttnings rapporten**: 
1. Verifiera migreringen genom att granska data och schemat på Azure SQL Database-instansen med Azure SQL Database Management Studio (SSMS).


## <a name="post-migration"></a>Efter migreringen 

När du har slutfört **migreringen** måste du gå igenom en serie uppgifter efter migreringen för att se till att allt fungerar så smidigt och effektivt som möjligt.

### <a name="remediate-applications"></a>Åtgärda program

När data har migrerats till mål miljön måste alla program som tidigare förbrukade källan börja använda målet. Om du gör detta måste du i vissa fall göra ändringar i programmen.

### <a name="perform-tests"></a>Utför tester

Test metoden för migrering av databasen består av att utföra följande aktiviteter:

1. **Utveckla verifieringstester**. Om du vill testa migreringen av databasen måste du använda SQL-frågor. Du måste skapa verifierings frågorna som ska köras mot både käll-och mål databaserna. Dina verifierings frågor ska omfatta det definitions område som du har definierat.

2. **Konfigurera test miljö**. Test miljön bör innehålla en kopia av käll databasen och mål databasen. Se till att isolera test miljön.

3. **Kör verifierings test**. Kör verifierings testen mot källan och målet och analysera sedan resultaten.

4. **Kör prestandatester**. Kör prestandatest mot källan och målet och analysera och jämför sedan resultaten.

### <a name="optimize"></a>Optimera

Fasen efter migreringen är avgörande för att kunna stämma av data precisions problem och kontrol lera att de är klara, samt att lösa prestanda problem med arbets belastningen.

> [!NOTE]
> Mer information om de här problemen och specifika steg för att minimera dem finns i [guiden för validering och optimering efter migrering](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="next-steps"></a>Nästa steg

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL Database finns i:
   - [En översikt över SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
