---
title: 'Azure Synapse Analytics: migration guide'
description: Följ den här guiden för att migrera dina databaser till en dedikerad Azure Synapse Analytics SQL-pool.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8304064e62ea3996e2ee6be6e12885cb853c9375
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278787"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrera ett informations lager till en dedikerad SQL-pool i Azure Synapse Analytics

I följande avsnitt får du en översikt över vad som ingår i migreringen av en befintlig data lager lösning till en dedikerad SQL-pool i Azure Synapse Analytics.

## <a name="overview"></a>Översikt

Innan du påbörjar migreringen bör du kontrol lera att Azure Synapse Analytics är den bästa lösningen för din arbets belastning. Azure Synapse Analytics är ett distribuerat system utformat för att utföra analyser av stora data. För att migrera till Azure Synapse Analytics krävs vissa design ändringar som inte är svåra att förstå, men som kan ta lite tid att implementera. Om ditt företag kräver ett informations lager i företags klass är fördelarna värdefull. Men om du inte behöver kraften i Azure Synapse Analytics är det mer kostnads effektivt att använda [SQL Server](https://docs.microsoft.com/sql/sql-server/) eller [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

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
- Inkompatibla format (till exempel JSON och XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

En av de kritiska Blocker som kunderna möter översätter sina databas objekt när de migrerar från ett system till ett annat. [Azure Synapse-väg](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) hjälper dig att uppgradera till en modern informations lager plattform genom att automatisera objekt översättningen av ditt befintliga data lager. Det är ett kostnads fritt, intuitivt och lättanvänt verktyg som automatiserar kod översättningen för att möjliggöra en snabb migrering till Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

# <a name="migrate-from-sql-server"></a>[Migrera från SQL Server](#tab/migratefromSQLServer)

Om du vill migrera SQL Server data lagret till Azure Synapse Analytics kontrollerar du att du uppfyller följande krav:

- Ha ett informations lager eller en analys arbets belastning.
- Hämta den senaste versionen av [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera SQL Server objekt till Azure Synapse-objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse-arbetsyta.

# <a name="migrate-from-netezza"></a>[Migrera från Netezza](#tab/migratefromNetezza)

Om du vill migrera ditt Netezza data lager till Azure Synapse Analytics kontrollerar du att du uppfyller följande krav:

- Hämta den senaste versionen av [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera SQL Server objekt till Azure Synapse-objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse-arbetsyta.

Mer information finns i [Azure Synapse Analytics-lösningar och migrering för Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrera från snö](#tab/migratefromSnowflake)

Om du vill migrera ditt snö data lager till Azure Synapse Analytics kontrollerar du att du uppfyller följande krav:

- Ladda ned den senaste versionen av [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) för att migrera snö flingor-objekt till Azure Synapse-objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse-arbetsyta.

# <a name="migrate-from-oracle"></a>[Migrera från Oracle](#tab/migratefromOracle)

Om du vill migrera ditt Oracle Data Warehouse till Azure Synapse Analytics kontrollerar du att du uppfyller följande krav:

- Ha ett informations lager eller en analys arbets belastning.
- Ladda ned SQL Server Migration Assistant för Oracle för att konvertera Oracle-objekt till SQL Server. Mer information finns i [migrera Oracle-databaser till SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Hämta den senaste versionen av [Azure Synapse-utbildningsavsnitt](https://www.microsoft.com/download/details.aspx?id=102787) för att migrera SQL Server objekt till Azure Synapse-objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse-arbetsyta.

Mer information finns i [Azure Synapse Analytics-lösningar och migrering för ett Oracle Data Warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Före migrering

När du har bestämt dig för att migrera en befintlig lösning till Azure Synapse Analytics måste du planera migreringen innan du börjar. Ett primärt mål för planering är att se till att dina data, tabell scheman och kod är kompatibla med Azure Synapse Analytics. Det finns vissa skillnader i kompatibilitet mellan ditt aktuella system och Azure Synapse Analytics som du behöver för att lösa problemet. Dessutom tar det tid att migrera stora mängder data till Azure. Noggrann planering påskyndar processen med att hämta dina data till Azure.

Ett annat viktiga mål för planeringen är att justera din design för att säkerställa att din lösning utnyttjar den höga prestanda som Azure Synapse Analytics har utformats för att tillhandahålla. Att utforma data lager för skalning introducerar unika design mönster, så traditionella metoder är inte alltid det bästa. Vissa design justeringar kan göras efter migreringen, men om du gör ändringar tidigare i processen kommer du att spara tid senare.

## <a name="migrate"></a>Migrera

Om du utför en lyckad migrering måste du migrera tabell scheman, kod och data. Mer detaljerad information om de här ämnena finns i följande artiklar:

- [Beakta tabell design](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Beakta kod ändring](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrera dina data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Överväg arbets belastnings hantering](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Fler resurser

Customer Advisory-teamet har en bra vägledning för Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) som publiceras som blogg inlägg. Mer information om migrering finns i [migrera data till Azure SQL Data Warehouse i praktiken](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migrera till gångar från verkliga ärenden

Mer hjälp om hur du slutför det här scenariot för migrering finns i följande resurser. De har utvecklats för att ge stöd till ett verkligt projekt engagemang för migrering.

| Rubrik/länk                              | Beskrivning                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program-eller databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad och enhetlig mål plattforms besluts process. |
| [Hantera data kodnings problem vid inläsning av data till Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Det här blogg inlägget ger insikt om några av de data kodnings problem som du kan stöta på när du använder PolyBase för att läsa in data till SQL Data Warehouse. Den här artikeln innehåller också några alternativ som du kan använda för att lösa dessa problem och läsa in data. |
| [Hämta tabell storlekar i Azure Synapse Analytics-dedikerad SQL-pool](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | En av de viktigaste uppgifterna som en arkitekt måste utföra är att hämta mått för en ny miljö efter migreringen. Exempel innefattar insamling av inläsnings tider från lokal plats till molnet och insamling av PolyBase-tider. En av de viktigaste uppgifterna är att fastställa lagrings storleken i SQL Data Warehouse jämfört med kundens aktuella plattform. |
| [Verktyg för att flytta lokala SQL Server inloggningar till Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Ett PowerShell-skript skapar ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från en lokal instans av SQL Server till en Azure SQL-plattform som tjänst (PaaS). Verktyget tillåter automatisk mappning av Windows Server Active Directory-konton till Azure Active Directory-konton eller kan göra UPN-sökningar för varje inloggning mot lokala Windows Server-Active Directory. Verktyget kan också flytta SQL Server interna inloggningar. Anpassade Server-och databas roller är skriptade, tillsammans med roll medlemskap, databas roll och användar behörigheter. Inneslutna databaser stöds inte och endast en delmängd av möjliga SQL Server behörigheter har skript. Mer information finns i support dokumentet, och skriptet innehåller kommentarer för att under lätta förståelsen. |

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.

## <a name="videos"></a>Video

Se hur [Walgreens migrerade sin detalj handels inventerings system](https://www.youtube.com/watch?v=86dhd8N1lH4) med cirka 100 TB data från Netezza till Azure Synapse Analytics i Record Time.
