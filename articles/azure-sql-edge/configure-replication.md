---
title: Konfigurera replikering till Azure SQL Edge
description: Lär dig hur du konfigurerar replikering till Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395231"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Konfigurera replikering till Azure SQL Edge 

Du kan konfigurera en instans av Azure SQL Edge som push-prenumerant för enkelriktad transaktionell replikering eller ögonblicks bild replikering. Den här instansen kan inte fungera som utgivare eller distributör för en transaktionell replikeringskonfiguration. Observera att Azure SQL Edge inte stöder Sammanslagningsreplikering, peer-to-peer-replikering eller Oracle-publicering.

## <a name="supported-configurations"></a>Konfigurationer som stöds
  
- Instansen av Azure SQL Edge måste vara en push-prenumerant för en utgivare.
- Utgivaren och distributören kan vara antingen:
   - En instans av SQL Server som körs lokalt eller en instans av SQL Server som körs i en virtuell Azure-dator. Mer information finns i [Översikt över SQL Server på Azure Virtual Machines](../azure-sql/virtual-machines/index.yml). SQL Server instanser måste använda en senare version än SQL Server 2016.
   - En instans av en Azure SQL-hanterad instans. SQL-hanterad instans kan vara värd för utgivare, distributör och prenumerant databaser. Mer information finns i [replikering med SQL Database Hanterad instans](/azure/sql-database/replication-with-sql-database-managed-instance/).

- Distributions databasen och-replik agenterna kan inte placeras på en instans av Azure SQL Edge.  

> [!NOTE]
> Om du försöker konfigurera replikering med hjälp av en version som inte stöds kan du få följande två fel: MSSQL_REPL20084 ("processen kunde inte ansluta till prenumeranten") och MSSQL_REPL40532 ("det går inte att öppna servern \<name> som begärdes av inloggningen. Inloggningen misslyckades. ").  

## <a name="remarks"></a>Kommentarer

Följande krav och bästa praxis är viktiga för att förstå när du konfigurerar replikering:

- Du kan konfigurera replikering med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Du kan också göra det genom att köra Transact-SQL-uttryck på utgivaren genom att använda antingen SQL Server Management Studio eller [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- Om du vill replikera till en instans av Azure SQL Edge måste du använda SQL Server autentisering för att logga in.
- Replikerade tabeller måste ha en primär nyckel.
- En enda publikation på SQL Server kan stödja både Azure SQL Edge och SQL Server (lokala och SQL Server på en virtuell Azure-dator) prenumeranter.  
- Hantering av replikering, övervakning och fel sökning måste utföras från SQL Server-instansen.  
- Det finns bara stöd för push-prenumerationer på Azure SQL Edge.  
- `@subscriber_type = 0`Stöds endast i den lagrade proceduren `sp_addsubscription` för Azure SQL Edge.  
- Azure SQL Edge stöder inte dubbelriktad, omedelbar, uppdaterbar eller peer-to-peer-replikering.
- Azure SQL Edge stöder endast en delmängd av funktioner som är tillgängliga i SQL Server eller SQL-hanterad instans. Om du försöker replikera en databas (eller objekt i databasen) som innehåller en eller flera funktioner som inte stöds, Miss lyckas försöket. Om du till exempel försöker replikera en databas som innehåller objekt med spatialdata, får du ett fel meddelande. Mer information finns i [funktioner som stöds i Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Initiera referens data på en instans av Azure SQL Edge

Du kanske vill initiera din instans med referens data som ändras över tid. Till exempel kanske du vill uppdatera Machine Learning-modeller på din instans av Azure SQL Edge, när de har tränats på en SQL Server instans. Så här initierar du instansen i ett sådant scenario:

1. Skapa en transaktionskö för replikering på en SQL Server databas.  
2. På SQL Server-instansen använder du **Guiden ny prenumeration** eller Transact-SQL-uttryck för att skapa en push-överföring till Azure SQL Edge.  
3. Du kan initiera den replikerade databasen på Azure SQL Edge genom att använda en ögonblicks bild som genererats av ögonblicks bild agenten och distribueras och levereras av distributions agenten. Du kan också initiera genom att använda en säkerhets kopia av databasen från utgivaren. Kom ihåg att om säkerhets kopian av databasen innehåller objekt eller funktioner som inte stöds av Azure SQL Edge, Miss lyckas återställnings åtgärden.

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Edge-prenumerationer:

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
- Kopiera FILESTREAM-, `hierarchyid` eller rums data typer
- Konvertera `hierarchyid` till max data typer  
- Konvertera spatialdata till MAX data typer  
- Kopiera utökade egenskaper  
- Kopiera behörigheter  

## <a name="examples"></a>Exempel

Skapa en publikation och en utgivarinitierad prenumeration. Mer information finns i:
  
- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription/) med hjälp av Azure SQL Edge-servernamnet och IP som prenumerant (till exempel **myEdgeinstance, 1433**) och ett databas namn på Azure SQL Edge-instansen som mål databas (till exempel **AdventureWorks**).  

## <a name="next-steps"></a>Nästa steg  

- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](/sql/relational-databases/replication/initialize-a-subscription)