---
title: 'Azure Synapse Analytics: migration guide'
description: Följ den här guiden för att migrera dina databaser till Azure Synapse Analytics-dedikerad SQL-pool.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: e27e7e0bb4616508c8f137260bde68de9420f9cf
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644967"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrera ett informations lager till en dedikerad SQL-pool i Azure Synapse Analytics 
I följande avsnitt får du en översikt över vad som ingår i migreringen av en befintlig data lager lösning till en dedikerad SQL-pool i Azure Synapse Analytics.

## <a name="overview"></a>Översikt
Innan du migrerar bör du kontrol lera att Azure Synapse Analytics är den bästa lösningen för din arbets belastning. Azure Synapse Analytics är ett distribuerat system utformat för att utföra analyser av stora data. För att migrera till Azure Synapse Analytics krävs vissa design ändringar som inte är svåra att förstå, men som kan ta lite tid att implementera. Om ditt företag kräver ett informations lager i företags klass är fördelarna värdefull. Men om du inte behöver kraften i Azure Synapse Analytics är det mer kostnads effektivt att använda [SQL Server](https://docs.microsoft.com/sql/sql-server/) eller [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Överväg att använda Azure Synapse Analytics när du:
- Ha en eller flera terabyte data.
- Planera att köra analyser på stora mängder data.
- Behöver möjlighet att skala beräkning och lagring.
- Vill spara pengar genom att pausa beräknings resurserna när du inte behöver dem.

I stället för Azure Synapse Analytics bör du överväga andra alternativ för drift (OLTP) arbets belastningar som har:
- Hög frekvens läsningar och skrivningar.
- Ett stort antal singleton-val väljs.
- Stora volymer för en enskild rad infogningar.
- Behov av rad-vid-rad-bearbetning.
- Inkompatibla format (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
En av de kritiska Blocker som kunderna möter översätter sina databas objekt vid migrering från ett system till ett annat. [Azure Synapse-väg](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) hjälper dig att uppgradera till en modern informations lager plattform genom att automatisera objekt översättningen av ditt befintliga data lager. Det är ett kostnads fritt, intuitivt och lättanvänt verktyg som automatiserar kod översättningen och möjliggör en snabb migrering till Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar
# <a name="migrate-from-sql-server"></a>[Migrera från SQL Server](#tab/migratefromSQLServer)
Om du vill migrera SQL Server data lagret till Azure Synapse Analytics kontrollerar du att du har följande krav: 

- Ett informations lager eller en analys arbets belastning 
- Hämta det senaste verktyget för [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera SQL Server objekt till Azure Synapse-objekt.
- En [dedikerad SQL-pool](../get-started-create-workspace.md) i Azure dataSynapses-arbetsytan. 

# <a name="migrate-from-netezza"></a>[Migrera från Netezza](#tab/migratefromNetezza)
Kontrol lera att du har följande förutsättningar för att migrera ditt Netezza data lager till Azure Synapse Analytics: 

- Hämta det senaste verktyget för [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera SQL Server objekt till Azure Synapse-objekt.
- En [dedikerad SQL-pool](../get-started-create-workspace.md) i Azure dataSynapses-arbetsytan.

Mer information finns i [Azure Synapse Analytics-lösningar och migrering för Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrera från snö](#tab/migratefromSnowflake)
Kontrol lera att du har följande förutsättningar för att migrera ditt snö data lager till Azure Synapse Analytics: 

- Hämta det senaste verktyget för [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera snö flingor-objekt till Azure Synapse-objekt.
- En [dedikerad SQL-pool](../get-started-create-workspace.md) i Azure dataSynapses-arbetsytan. 

# <a name="migrate-from-oracle"></a>[Migrera från Oracle](#tab/migratefromOracle)
Kontrol lera att du har följande förutsättningar för att migrera ditt Oracle Data Warehouse till Azure Synapse Analytics: 

- Ett informations lager eller en analys arbets belastning 
- Ladda ned SSMA för Oracle för att konvertera Oracle-objekt till SQL Server. Mer information finns i [migrera Oracle-databaser till SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) . 
- Hämta den senaste versionen av [Azure Synapse utbildningsavsnitt](https://www.microsoft.com/download/details.aspx?id=102787) -verktyget för att migrera SQL Server objekt till Azure Synapse-objekt.
- En [dedikerad SQL-pool](../get-started-create-workspace.md) i Azure dataSynapses-arbetsytan. 

Mer information finns i [Azure Synapse Analytics-lösningar och migrering för ett Oracle Data Warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Före migrering
När du har fattat beslutet om att migrera en befintlig lösning till Azure Synapse Analytics är det viktigt att planera migreringen innan du börjar. Ett primärt mål för planering är att se till att dina data, tabell scheman och kod är kompatibla med Azure Synapse Analytics. Det finns vissa skillnader i kompatibilitet mellan ditt aktuella system och Azure Synapse Analytics som du behöver för att lösa problemet. Dessutom tar det tid att migrera stora mängder data till Azure. Noggrann planering påskyndar processen med att hämta dina data till Azure. Ett annat viktiga mål för planeringen är att justera din design för att säkerställa att din lösning utnyttjar den höga prestanda som Azure Synapse Analytics har utformats för att tillhandahålla. Att utforma data lager för skalning introducerar unika design mönster, så traditionella metoder är inte alltid det bästa. Vissa design justeringar kan göras efter migreringen, men om du gör ändringar tidigare i processen kommer du att spara tid senare.

## <a name="migrate"></a>Migrera
Om du utför en lyckad migrering måste du migrera tabell scheman, kod och data. Mer detaljerad information om de här ämnena finns i:
- Den här artikeln är [tabell design](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- I artikeln [beaktas kod ändringar](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- Artikeln [migrerar dina data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- I artikeln [övervägs arbets belastnings hantering](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>Ytterligare resurser 
- KATTen (Customer Advisory team) har en viss bra Azure Synapse Analytics-vägledning (tidigare SQL DW) publicerad som blogg inlägg. Se till att ta en titt på artikeln, [migrera data till Azure SQL Data Warehouse i praktiken](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)för ytterligare vägledning om migrering.

## <a name="migration-assets-from-real-world-engagements"></a>Migrera till gångar från verkliga ärenden
Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| Rubrik/länk                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process. |
| [Hantera data kodnings problem vid inläsning av data till Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Den här bloggen är avsedd att ge insikt om några av de data kodnings problem som kan uppstå när PolyBase används för att läsa in data till SQL Data Warehouse. Den här artikeln innehåller också några alternativ som du kan använda för att lösa dessa problem och läsa in data. |
| [Hämta tabell storlekar i Azure Synapse Analytics-dedikerad SQL-pool](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | En av de viktigaste uppgifterna som en arkitekt måste utföra är att hämta mått om en ny miljö efter migreringen: insamling av inläsnings tider från lokal plats till molnet, insamling av PolyBase-tider osv. Av de här uppgifterna är en av de viktigaste att fastställa lagrings storleken i SQL Data Warehouse jämfört med kundens aktuella plattform. |
| [Verktyg för att flytta lokala SQL Server inloggningar till Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Ett PowerShell-skript som skapar ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från en lokal SQL Server till en Azure SQL PaaS-tjänst. Verktyget tillåter automatisk mappning av Windows AD-konton till Azure AD-konton eller kan göra UPN-sökningar för varje inloggning mot den lokala Windows-Active Directory. Verktyget kan också flytta SQL Server interna inloggningar. Anpassade Server-och databas roller är skriptade, samt roll medlemskap och databas roll och användar behörigheter. Inneslutna databaser stöds ännu inte och endast en delmängd av möjliga SQL Server behörigheter har skript ATS. t. ex. behörigheter som beviljats med Grant stöds inte (komplexa behörighets träd). Mer information finns i support dokumentet och skriptet innehåller kommentarer för att under lätta förståelsen. |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="videos"></a>Video
- Se hur [Walgreens migrerade sin återförsäljarversion](https://www.youtube.com/watch?v=86dhd8N1lH4) av 100 TB data från Netezza till Azure Synapse Analytics (tidigare SQL DW) i Record Time. 