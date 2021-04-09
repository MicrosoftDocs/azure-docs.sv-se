---
title: Azure SQL Server replikering till Azure SQL Database
description: Du kan konfigurera en databas i Azure SQL Database som push-prenumerant i en enkelriktad transaktionell eller ögonblicks bilds replikeringstopologi.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780962"
---
# <a name="replication-to-azure-sql-database"></a>Replikering till Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Du kan konfigurera en Azure SQL Database som push-prenumerant i en enkelriktad transaktionell eller ögonblicks bilds replikeringstopologi.

> [!NOTE]
> I den här artikeln beskrivs användningen [av Transaktionsreplikering](/sql/relational-databases/replication/transactional/transactional-replication) i Azure SQL Database. Den är inte relaterad till [aktiv geo-replikering](./active-geo-replication-overview.md), en Azure SQL Database funktion som gör att du kan skapa kompletta läsbara repliker av enskilda databaser.

## <a name="supported-configurations"></a>Konfigurationer som stöds
  
- Azure SQL Database kan bara användas som push-prenumerant för en SQL Server utgivare och distributör.  
- SQL Server-instansen som fungerar som utgivare och/eller distributör kan vara en instans av [SQL Server som körs lokalt](https://www.microsoft.com/sql-server/sql-server-downloads), en [hanterad Azure SQL-instans](../managed-instance/instance-create-quickstart.md)eller en instans av [SQL Server som körs på en virtuell Azure-dator i molnet](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- Distributions databasen och-replik agenterna kan inte placeras i en databas i Azure SQL Database.  
- [Ögonblicks bilder](/sql/relational-databases/replication/snapshot-replication) och [enkelriktad transaktionell](/sql/relational-databases/replication/transactional/transactional-replication) replikering stöds. Peer-to-peer-transaktionell replikering och Sammanslagningsreplikering stöds inte.

### <a name="versions"></a>Versioner  

För att kunna replikera till en databas i Azure SQL Database måste SQL Server utgivare och distributörer använda (minst) en av följande versioner:

Det finns stöd för att publicera till en Azure SQL Database från en SQL Server-databas i följande versioner av SQL Server:

- SQL Server 2016 och senare
- SQL Server 2014 [RTM-CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) eller [SP1-CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) eller [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Försök att konfigurera replikering med en version som inte stöds kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQL_REPL40532 (det går inte att öppna servern \<name> som begärdes av inloggningen. Inloggningen misslyckades.).  

Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Typer av replikering

Det finns olika [typer av replikering](/sql/relational-databases/replication/types-of-replication):

| Replikering | Azure SQL Database | Hanterad Azure SQL-instans |
| :----| :------------- | :--------------- |
| [**Standard transaktion**](/sql/relational-databases/replication/transactional/transactional-replication) | Ja (endast som prenumerant) | Ja | 
| [**Ögonblicks bild**](/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Sammanfoga replikering**](/sql/relational-databases/replication/merge/merge-replication) | Inga | Inga|
| [**Peer-to-peer**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Inga | Inga|
| [**Dubbelriktad**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Inga | Ja|
| [**Uppdaterings bara prenumerationer**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Inga | Inga|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Kommentarer

- Endast push-prenumerationer till Azure SQL Database stöds.  
- Du kan konfigurera replikering med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eller genom att köra Transact-SQL-uttryck på utgivaren. Du kan inte konfigurera replikering med hjälp av Azure Portal.  
- Replikeringen kan bara använda SQL Server autentiserings-inloggningar för att ansluta till Azure SQL Database.
- Replikerade tabeller måste ha en primär nyckel.  
- Du måste ha en befintlig Azure-prenumeration.  
- Azure SQL Database prenumeranten kan finnas i vilken region som helst.  
- En enda publikation på SQL Server kan stödja både Azure SQL Database och SQL Server (lokalt och SQL Server på en virtuell Azure-dator) prenumeranter.  
- Hantering av replikering, övervakning och fel sökning måste utföras från SQL Server i stället för Azure SQL Database.  
- `@subscriber_type = 0`Stöds endast i **sp_addsubscription** för SQL Database.  
- Azure SQL Database stöder inte dubbelriktad, omedelbar, uppdaterbar eller peer-to-peer-replikering.

## <a name="replication-architecture"></a>Arkitektur för replikering  

![Diagrammet visar replikeringens arkitektur med Azure SQL Database, som innehåller flera prenumerations kluster i olika regioner och lokala virtuella Azure-datorer, som innehåller en utgivare, Logread körbar fil och distributions program som ansluter till fjärranslutna kluster.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenarier  

### <a name="typical-replication-scenario"></a>Vanligt scenario för replikering  

1. Skapa en transaktionskö för replikering på en SQL Server databas.  
2. På SQL Server använder du **Guiden ny prenumeration** eller Transact-SQL-uttryck för att skapa en push-överföring till en prenumeration till Azure SQL Database.  
3. Med enskilda databaser och databaser i Azure SQL Database är den ursprungliga data uppsättningen en ögonblicks bild som skapas av ögonblicks bild agenten och distribueras och tillämpas av distributions agenten. Med en SQL-hanterad instans utgivare kan du också använda en databas säkerhets kopia för att dirigera Azure SQL Database prenumeranten.

### <a name="data-migration-scenario"></a>Scenario för data migrering  

1. Använd transaktionell replikering för att replikera data från en SQL Server databas till Azure SQL Database.  
2. Dirigera om klienten eller program på mellan nivå för att uppdatera databas kopian.  
3. Sluta uppdatera SQL Server-versionen av tabellen och ta bort publikationen.  

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Database prenumerationer:

- Kopiera fil grupps Association  
- Kopiera tabell partitionerings scheman  
- Kopiera index partitionerings scheman  
- Kopiera användardefinierad statistik  
- Kopiera standard bindningar  
- Kopiera regel bindningar  
- Kopiera full text index  
- Kopiera XML XSD  
- Kopiera XML-index  
- Kopiera behörigheter  
- Kopiera rums index  
- Kopiera filtrerade index  
- Kopiera data komprimerings attribut  
- Kopiera sparse-kolumnattribut  
- Konvertera FILESTREAM till MAX data typer  
- Konvertera hierarchyid till MAX data typer  
- Konvertera spatialdata till MAX data typer  
- Kopiera utökade egenskaper  

### <a name="limitations-to-be-determined"></a>Begränsningar som ska fastställas

- Kopiera sortering  
- Körning i en serialiserad transaktion av SP  

## <a name="examples"></a>Exempel

Skapa en publikation och en utgivarinitierad prenumeration. Mer information finns i:
  
- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription/) med hjälp av Server namnet som prenumerant (till exempel **N'azuresqldbdns. Database. Windows. net**) och Azure SQL Database namn som mål databas (till exempel **AdventureWorks**).  

## <a name="see-also"></a>Se även  

- [Transaktionsreplikering](../managed-instance/replication-transactional-overview.md)
- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](/sql/relational-databases/replication/initialize-a-subscription)