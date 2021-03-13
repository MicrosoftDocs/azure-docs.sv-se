---
title: 'SQL Server till Azure Synapse Analytics: migration guide'
description: Följ den här guiden för att migrera dina SQL-databaser till Azure Synapse Analytics SQL-poolen.
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 9a7888d3ccf7e033f15f184227c65c746780aa12
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418036"
---
# <a name="migration-guide-sql-server-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migration guide: SQL Server till en dedikerad SQL-pool i Azure Synapse Analytics 
I följande avsnitt får du en översikt över vad som ingår i migreringen av en befintlig SQL Server data lager lösning till Azure Synapse Analytics SQL-poolen.

## <a name="overview"></a>Översikt
Innan du migrerar bör du kontrol lera att Azure Synapse Analytics är den bästa lösningen för din arbets belastning. Azure Synapse Analytics är ett distribuerat system utformat för att utföra analyser av stora data. För att migrera till Azure Synapse Analytics krävs vissa design ändringar som inte är svåra att förstå, men som kan ta lite tid att implementera. Om ditt företag kräver ett informations lager i företags klass är fördelarna värdefull. Men om du inte behöver kraften i Azure Synapse Analytics är det mer kostnads effektivt att använda [SQL Server](/sql/sql-server/) eller [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview).

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

## <a name="prerequisites"></a>Förutsättningar
Kontrol lera att du har följande förutsättningar för att migrera SQL Server till Azure Synapse Analytics: 

- Ett informations lager eller en analys arbets belastning 
- Den senaste versionen av [Azure Synapse utbildningsavsnitt](https://www.microsoft.com/en-us/download/details.aspx?id=102787) -verktyget för att migrera SQL Server objekt till Azure Synapse-objekt.
- En [dedikerad SQL-pool](../get-started-create-workspace.md) i Azure dataSynapses-arbetsytan. 

## <a name="pre-migration"></a>Före migrering
När du har fattat beslutet om att migrera en befintlig lösning till Azure Synapse Analytics är det viktigt att planera migreringen innan du börjar. Ett primärt mål för planering är att se till att dina data, tabell scheman och kod är kompatibla med Azure Synapse Analytics. Det finns vissa skillnader i kompatibilitet mellan ditt nuvarande system och SQL Data Warehouse som du behöver lösa. Det tar också tid att migrera stora mängder data till Azure. Noggrann planering påskyndar processen med att hämta dina data till Azure. Ett annat viktiga mål för planeringen är att justera din design för att säkerställa att din lösning utnyttjar den höga prestanda som Azure Synapse Analytics har utformats för att tillhandahålla. Att utforma data lager för skalning introducerar unika design mönster, så traditionella metoder är inte alltid det bästa. Vissa design justeringar kan göras efter migreringen, men om du gör ändringar tidigare i processen kommer du att spara tid senare.

## <a name="azure-synapse-pathway"></a>Azure Synapse-utbildningsavsnitt
En av de kritiska Blocker som kunderna möter översätter sin SQL-kod vid migrering från ett system till ett annat. [Azure Synapse-väg](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) hjälper dig att uppgradera till en modern informations lager plattform genom att automatisera kod översättningen av ditt befintliga data lager. Det är ett kostnads fritt, intuitivt och lättanvänt verktyg som automatiserar kod översättningen och möjliggör en snabb migrering till Azure Synapse Analytics.

## <a name="migrate"></a>Migrera
Om du utför en lyckad migrering måste du migrera tabell scheman, kod och data. Mer detaljerad information om de här ämnena finns i:
- Artikeln [migrerar dina scheman](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- Artikeln [migrerar din kod](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- Artikeln [migrerar dina data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Ytterligare resurser 
- KATTen (Customer Advisory team) har en bra vägledning för Azure Synapse Analytics (tidigare SQL Data Warehouse) som publiceras som blogg inlägg. Se till att ta en titt på artikeln, [migrera data till Azure SQL Data Warehouse i praktiken](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)för ytterligare vägledning om migrering.
- Kolla in white paper att [välja sökvägen till din databas migrering till Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) för ytterligare information och rekommendationer.
- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering](https://docs.microsoft.com/azure/dms/dms-tools-matrix). 

## <a name="migration-assets-from-real-world-engagements"></a>Migrera till gångar från verkliga ärenden
Mer hjälp om hur du slutför det här migreringsprocessen finns i följande resurser, som har utvecklats för att ge stöd för ett verkligt migrerings projekt.

| Rubrik/länk                              | Beskrivning                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som gör det lättare att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process. |
| [Hantera data kodnings problem vid inläsning av data till Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Den här bloggen är avsedd att ge insikt om några av de data kodnings problem som kan uppstå när PolyBase används för att läsa in data till SQL Data Warehouse. Den här artikeln innehåller också några alternativ som du kan använda för att lösa dessa problem och läsa in data. |
| [Hämtar tabell storlekar i Azure Synapse Analytics SQL-poolen](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | En av de viktigaste uppgifterna som en arkitekt måste utföra är att hämta mått om en ny miljö efter migreringen: insamling av inläsnings tider från lokal plats till molnet, insamling av PolyBase-tider osv. Av de här uppgifterna är en av de viktigaste att fastställa lagrings storleken i SQL Data Warehouse jämfört med kundens aktuella plattform. |
| [Verktyg för att flytta lokala SQL Server inloggningar till Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Ett PowerShell-skript som skapar ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från en lokal SQL Server till en Azure SQL PaaS-tjänst. Verktyget tillåter automatisk mappning av Windows AD-konton till Azure AD-konton eller kan göra UPN-sökningar för varje inloggning mot den lokala Windows-Active Directory. Verktyget kan också flytta SQL Server interna inloggningar. Anpassade Server-och databas roller är skriptade, samt roll medlemskap och databas roll och användar behörigheter. Inneslutna databaser stöds ännu inte och endast en delmängd av möjliga SQL Server behörigheter har skript ATS. t. ex. behörigheter som beviljats med Grant stöds inte (komplexa behörighets träd). Mer information finns i support dokumentet och skriptet innehåller kommentarer för att under lätta förståelsen. |

> [!NOTE]
> Dessa resurser ovan utvecklades som en del av data migration rivstart med-programmet (DM rivstart med), som sponsras av Azure Data Group Engineering-teamet. Kärn stadgan för DM rivstart med är att avblockera och påskynda komplexa modernisering och konkurrera med migreringen av data plattformar till Microsofts Azure-dataplattform. Om du tror att din organisation är intresse rad av att delta i DM rivstart med-programmet, kontakta ditt konto team och be att de skickar in en nominerare.

## <a name="videos"></a>Video
- En översikt över Azure Database migration guide och den information den innehåller finns i videon [använda Database migration guide](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- En genom gång av stegen i migreringsprocessen och information om de verktyg och tjänster som rekommenderas för att utföra utvärdering och migrering finns i video [Översikt över migrerings resan och de verktyg/tjänster som rekommenderas för att utföra utvärdering och migrering](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).