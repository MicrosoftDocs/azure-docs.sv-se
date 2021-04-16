---
title: 'Azure Synapse Analytics: Migreringsguide'
description: Följ den här guiden för att migrera dina databaser till en Azure Synapse Analytics dedikerad SQL-pool.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565652"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrera ett informationslager till en dedikerad SQL-pool i Azure Synapse Analytics

Följande avsnitt innehåller en översikt över vad som ingår i migreringen av en befintlig informationslagerlösning till en Azure Synapse Analytics dedikerad SQL-pool.

## <a name="overview"></a>Översikt

Innan du påbörjar migreringen bör du kontrollera att Azure Synapse Analytics är den bästa lösningen för din arbetsbelastning. Azure Synapse Analytics är ett distribuerat system som utformats för att utföra analyser på stora data. Att migrera till Azure Synapse Analytics kräver vissa designändringar som inte är svåra att förstå, men som kan ta lite tid att implementera. Om ditt företag kräver ett informationslager i företagsklass är det värt ansträngningen. Men om du inte behöver kraften hos Azure Synapse Analytics är det mer kostnadseffektivt att [använda SQL Server](https://docs.microsoft.com/sql/sql-server/) eller [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Överväg att Azure Synapse Analytics när du:

- Ha en eller flera terabyte data.
- Planera att köra analyser på stora mängder data.
- Behöver kunna skala beräkning och lagring.
- Vill spara pengar genom att pausa beräkningsresurser när du inte behöver dem.

I stället Azure Synapse Analytics överväga andra alternativ för driftarbetsbelastningar (OLTP) som har:

- Läsningar och skrivningar med hög frekvens.
- Stora antal singleton-val.
- Stora volymer med enskilda radinfogningar.
- Bearbetningsbehov rad för rad.
- Inkompatibla format (till exempel JSON och XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

En av de viktigaste blockerarna som kunder står inför är att översätta sina databasobjekt när de migrerar från ett system till ett annat. [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) hjälper dig att uppgradera till en modern informationslagerplattform genom att automatisera objektöversättning av ditt befintliga informationslager. Det är ett kostnadsfritt, intuitivt och användarvänligt verktyg som automatiserar kodöversättning för att möjliggöra en snabbare migrering till Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

# <a name="migrate-from-sql-server"></a>[Migrera från SQL Server](#tab/migratefromSQLServer)

Om du vill SQL Server datalagret till Azure Synapse Analytics måste du kontrollera att du har uppfyllt följande krav:

- Ha ett informationslager eller en analysarbetsbelastning.
- Ladda ned den senaste versionen [av Azure Synapse För att](https://www.microsoft.com/en-us/download/details.aspx?id=102787) migrera SQL Server objekt till Azure Synapse objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse arbetsyta.

# <a name="migrate-from-netezza"></a>[Migrera från Netezza](#tab/migratefromNetezza)

Om du vill migrera netezza-informationslagret till Azure Synapse Analytics måste du kontrollera att du har uppfyllt följande krav:

- Ladda ned den senaste versionen [Azure Synapse Väg för](https://www.microsoft.com/en-us/download/details.aspx?id=102787) att migrera SQL Server objekt till Azure Synapse objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse arbetsyta.

Mer information finns i Azure Synapse Analytics [och migrering för Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrera från Snowflake](#tab/migratefromSnowflake)

Om du vill migrera ditt Snowflake-informationslager till Azure Synapse Analytics kontrollerar du att du har uppfyllt följande krav:

- Ladda ned den senaste versionen [av Azure Synapse För att](https://www.microsoft.com/en-us/download/details.aspx?id=102787) migrera Snowflake-objekt till Azure Synapse objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse arbetsyta.

# <a name="migrate-from-oracle"></a>[Migrera från Oracle](#tab/migratefromOracle)

Om du vill migrera Ditt Oracle-Azure Synapse Analytics måste du kontrollera att du har uppfyllt följande krav:

- Ha ett informationslager eller en analysarbetsbelastning.
- Ladda SQL Server Migration Assistant Oracle för att konvertera Oracle-objekt till SQL Server. Mer information finns i [Migrera Oracle-databaser till SQL Server (OracleToSQL).](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)
- Ladda ned den senaste versionen [Azure Synapse Väg för](https://www.microsoft.com/download/details.aspx?id=102787) att migrera SQL Server objekt till Azure Synapse objekt.
- Ha en [dedikerad SQL-pool](../get-started-create-workspace.md) i en Azure Synapse arbetsyta.

Mer information finns i Azure Synapse Analytics [och migrering för ett Oracle-informationslager.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)

---

## <a name="pre-migration"></a>Före migrering

När du har bestämt dig för att migrera en befintlig lösning Azure Synapse Analytics måste du planera migreringen innan du börjar. Ett viktigt mål med planeringen är att säkerställa att dina data, tabellscheman och kod är kompatibla med Azure Synapse Analytics. Det finns vissa kompatibilitetsskillnader mellan ditt aktuella system och Azure Synapse Analytics som du måste komma runt. Dessutom tar det tid att migrera stora mängder data till Azure. Noggrann planering påskyndar processen för att hämta dina data till Azure.

Ett annat viktigt mål med planeringen är att justera din design för att säkerställa att din lösning drar full nytta av den höga frågeprestanda som Azure Synapse Analytics har utformats för att tillhandahålla. Design av informationslager för skalning introducerar unika designmönster, så traditionella metoder är inte alltid de bästa. Vissa designjusteringar kan göras efter migreringen, men du kan spara tid senare om du gör ändringar tidigare i processen.

## <a name="migrate"></a>Migrera

När du utför en lyckad migrering måste du migrera dina tabellscheman, kod och data. Mer detaljerad information om dessa ämnen finns i följande artiklar:

- [Överväg tabelldesign](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Överväg kodändring](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrera dina data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Överväg arbetsbelastningshantering](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Fler resurser

Customer Advisory Team har en bra Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) som publicerats som blogginlägg. Mer information om migrering finns i [Migrera data till Azure SQL Data Warehouse i praktiken.](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)

## <a name="migration-assets-from-real-world-engagements"></a>Migreringstillgångar från verkliga engagemang

Mer hjälp med att slutföra det här migreringsscenariot finns i följande resurser. De utvecklades som stöd för ett verkligt migreringsprojekt.

| Rubrik/länk                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Utvärderingsmodell och verktyg för dataarbetsbelastning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget innehåller förslag på "bästa passning"-målplattformar, molnberedskap och program- eller databasreparationsnivå för en viss arbetsbelastning. Den erbjuder enkel beräkning med ett klick och rapportgenerering som hjälper till att påskynda stora egendomsutvärderingar genom att tillhandahålla en automatiserad och enhetlig målplattformsbeslutsprocess. |
| [Hantera problem med datakodning vid inläsning av data till Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Det här blogginlägget innehåller information om några av de problem med datakodning som du kan stöta på när du använder PolyBase för att läsa in data till SQL Data Warehouse. Den här artikeln innehåller också några alternativ som du kan använda för att lösa sådana problem och läsa in data. |
| [Hämta tabellstorlekar i Azure Synapse Analytics dedikerad SQL-pool](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | En av de viktigaste uppgifterna som en arkitekt måste utföra är att hämta mått om en ny miljö efter migreringen. Exempel på detta är insamling av inläsningstider från en lokal plats till molnet och insamling av PolyBase-inläsningstider. En av de viktigaste uppgifterna är att fastställa lagringsstorleken i SQL Data Warehouse jämfört med kundens aktuella plattform. |
| [Verktyg för att flytta lokala SQL Server inloggningar till Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Ett PowerShell-skript skapar ett T-SQL-kommandoskript för att skapa inloggningar på nytt och välja databasanvändare från en lokal instans av SQL Server till en Azure SQL PaaS-tjänst (plattform som en tjänst). Med verktyget kan du automatiskt mappa Windows Server Active Directory-konton till Azure Active Directory-konton, eller göra UPN-uppslag för varje inloggning mot lokala Windows Server Active Directory. Verktyget kan också flyttas SQL Server interna inloggningar också. Anpassade server- och databasroller skriptas, tillsammans med rollmedlemskap, databasroll och användarbehörigheter. Inneslutna databaser stöds inte och endast en delmängd av de SQL Server behörigheterna skriptas. Mer information finns i supportdokumentet och skriptet innehåller kommentarer för att underlätta förståelsen. |

Data SQL-teknikteamet utvecklade dessa resurser. Det här teamets grundstadga är att avblockera och påskynda komplex modernisering för migreringsprojekt för dataplattformar till Microsofts Azure-dataplattform.

## <a name="videos"></a>Video

Se hur [Walgreens migrerade](https://www.youtube.com/watch?v=86dhd8N1lH4) sitt lagersystem för detaljhandeln med cirka 100 TB data från Netezza till Azure Synapse Analytics i posttid.
