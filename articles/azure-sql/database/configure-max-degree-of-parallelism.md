---
title: Konfigurera Max graden av parallellitet (MAXDOP)
titleSuffix: Azure SQL Database
description: Lär dig mer om den högsta graden av parallellitet (MAXDOP).
ms.date: 03/29/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112044"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurera Max graden av parallellitet (MAXDOP) i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Den här artikeln beskriver den **högsta graden av parallellitet (MAXDOP)** i Azure SQL Database och hur det kan konfigureras. 

> [!NOTE]
> **Det här innehållet fokuserar på Azure SQL Database.** Azure SQL Database baseras på den senaste stabila versionen av Microsoft SQL Server databas motorn, så mycket av innehållet liknar om fel söknings-och konfigurations alternativen skiljer sig. Mer information om MAXDOP i SQL Server finns i [Konfigurera Max graden av konfigurations alternativ för parallella servrar](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Översikt
  I Azure SQL Database är standard inställningen för MAXDOP för varje ny databas och Elastic pool-databasen 8. Det innebär att databas motorn kan köra frågor med flera trådar. Till skillnad från SQL Server, där standardvärdet för Server-wide MAXDOP är 0 (obegränsat), är nya databaser i Azure SQL Database inställda på MAXDOP 8. Den här standarden förhindrar onödigt resursutnyttjande och garanterar konsekvent kund upplevelse. Det är normalt nödvändigt att ytterligare konfigurera MAXDOP i Azure SQL Database arbets belastningar, men det kan ge fördelar som en avancerad prestanda justerings övning.

> [!Note]
>   I september Azure SQL Database 2020 [valdes MAXDOP](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) -tjänsten som standard för nya databaser som ett optimalt värde för de många olika kund arbets belastningarna. Detta standardvärde har hjälpt till att förhindra prestanda problem på grund av överdriven parallellitet. Innan det var standard inställningen för nya databaser MAXDOP 0. Konfigurations alternativet MAXDOP Database omfånget ändrades inte för befintliga databaser som skapats före september 2020.

  I allmänhet är körnings tiden snabbare om databas motorn väljer att köra en fråga med parallellitet. Överskjutande parallellitet kan dock förbruka överskotts processor resurser utan att förbättra frågans prestanda. Vid skalning kan överskotts parallellitet negativt påverka frågans prestanda för alla frågor som körs på samma databas motor instans, så att en övre gräns för parallellitet har varit en gemensam prestanda justerings övning i SQL Server arbets belastningar.

  I följande tabell beskrivs hur databas motorn fungerar när du kör frågor med olika MAXDOP-värden:

| MAXDOP | Beteende | 
|--|--|
| = 1 | Databas motorn kör inte frågor med flera samtidiga trådar. | 
| > 1 | Databas motorn anger en övre gränser för antalet parallella trådar. Databas motorn väljer hur många extra arbets trådar som ska användas. Det totala antalet arbets trådar som används för att köra en fråga kan vara högre än det angivna MAXDOP-värdet. |
| = 0 | Databas motorn kan använda ett antal parallella trådar med en övre gränser som är beroende av det totala antalet logiska processorer. Databas motorn väljer antalet parallella trådar som ska användas.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Angående  

-   I Azure SQL Database kan du ändra standard värde för MAXDOP:
    -   Använd **MAXDOP** [-frågetipset på nivån fråga.](/sql/t-sql/queries/hints-transact-sql-query)     
    -   Använd **MAXDOP** - [databasens omfattnings konfiguration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)på databas nivå.

-   MAXDOPa överväganden och [rekommendationer](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) är tillgängliga för Azure SQL Database. SQL Server 

-   MAXDOP tillämpas per [uppgift](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Den tillämpas inte per [förfrågan](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) eller per fråga. Det innebär att när en parallell frågekörning körs kan en enskild begäran skapa flera aktiviteter med en övre gränser som bestäms av MAXDOP. Mer information finns i avsnittet *Schemalägga parallella uppgifter* i [arkitektur guiden för tråd och aktivitet](/sql/relational-databases/thread-and-task-architecture-guide). 
  
-   Index åtgärder som skapar eller återskapar ett index, eller som släpper ett grupperat index, kan vara resurs krävande. Du kan åsidosätta databasens Max grad av parallelity-värde för index åtgärder genom att ange index alternativet MAXDOP `CREATE INDEX` i `ALTER INDEX` instruktionen or. MAXDOP-värdet tillämpas på instruktionen vid körning och lagras inte i indexets metadata. Mer information finns i [Konfigurera Parallel index-åtgärder](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Förutom frågor och index åtgärder styr det omfångiska konfigurations alternativet för MAXDOP också parallellitet av DBCC CHECKTABLE, DBCC CHECKDB och DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Rekommenderade  

  Ändring av MAXDOP för databasen kan ha stor påverkan på frågornas prestanda och resursutnyttjande, både positiva och negativa. Det finns dock inget enskilt MAXDOP-värde som är optimalt för alla arbets belastningar. Rekommendationerna för att ställa in MAXDOP är nyanserade och är beroende av många faktorer. 

  Vissa arbets belastningar med hög belastning kan användas bättre med en annan MAXDOP än andra. En korrekt konfigurerad MAXDOP bör minska risken för prestanda-och tillgänglighets incidenter, och i vissa fall minska kostnaderna genom att kunna undvika onödig resursutnyttjande och därmed skala ned till ett lägre tjänst mål.

### <a name="excessive-parallelism"></a>Överdriven parallellitet

  En högre MAXDOP minskar ofta varaktigheten för CPU-intensiva frågor. Alltför parallellitet kan dock förvärra andra samtidiga arbets belastnings prestanda genom att svälter andra frågor om processor-och arbets tråds resurser. I extrema fall kan Överdriven parallellitet förbruka alla databaser eller elastiska pool resurser, vilket orsakar tids gränser, fel och program avbrott. 

  Vi rekommenderar att kunderna undviker MAXDOP 0 även om de inte verkar orsaka problem för närvarande. Överdriven parallellitet blir mest problematisk när processor-och arbets trådar tar emot fler samtidiga begär Anden än vad som kan stödjas av tjänst målet. Undvik MAXDOP 0 för att minska risken för potentiella framtida problem på grund av överdriven parallellitet om en databas skalas upp, eller om framtida maskin varu generationer i Azure SQL Database tillhandahåller fler kärnor för samma databas tjänst mål.

### <a name="modifying-maxdop"></a>Ändra MAXDOP 

  Om du bestämmer att en annan MAXDOP-inställning är optimal för din Azure SQL Database-arbetsbelastning kan du använda `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-instruktionen. Exempel finns i avsnittet [använda Transact-SQL](#examples) nedan. Lägg till det här steget i distributions processen för att ändra MAXDOP när databasen har skapats.

  Om icke-MAXDOP fördelar endast en delmängd frågor i arbets belastningen, kan du åsidosätta MAXDOP på frågeparametern genom att lägga till alternativ-tipset (MAXDOP). Exempel finns i avsnittet [använda Transact-SQL](#examples) nedan. 

  Testa noggrant dina MAXDOP-konfigurations ändringar med belastnings testning som involverar realistiska inläsningar av frågor. 

  MAXDOP för primära och sekundära repliker kan konfigureras separat för att dra nytta av olika optimala MAXDOP-inställningar för skrivskyddade och skrivskyddade arbets belastningar. Detta gäller för Azure SQL Database [Läs-och utskalning](read-scale-out.md), [geo-replikering](active-geo-replication-overview.md)och [Azure SQL Database sekundär repliker](service-tier-hyperscale.md). Som standard ärver alla sekundära repliker MAXDOP-konfigurationen för den primära repliken.

## <a name="security"></a><a name="Security"></a> Bullet  
  
###  <a name="permissions"></a><a name="Permissions"></a> Åtkomst  
  `ALTER DATABASE SCOPED CONFIGURATION`Instruktionen måste köras som Server administratör, som en medlem i databas rollen `db_owner` eller en användare som har beviljats `ALTER ANY DATABASE SCOPED CONFIGURATION` behörigheten.
 
## <a name="examples"></a>Exempel   

  I de här exemplen används den senaste **AdventureWorksLT** -exempel databasen när `SAMPLE` alternativet väljs för en ny databas med Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP databas omfångs konfiguration   

  I det här exemplet visas hur du kan använda [Alter Database områdes konfigurations](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) instruktion för att konfigurera `max degree of parallelism` alternativet till `2` . Inställningen utförs direkt. PowerShell-cmdleten [Invoke-Sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) kör T-SQL-frågorna för att ställa in och returnera MAXDOP-databasens omfattnings konfiguration. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Det här exemplet är till för användning med Azure SQL-databaser med [skalbara repliker aktiverade](read-scale-out.md), [geo-replikering](active-geo-replication-overview.md)och [Azure SQL Database storskaliga sekundära repliker](service-tier-hyperscale.md). Som exempel är den primära repliken inställd på en annan standard-MAXDOP som den sekundära repliken, vilket förväntar sig att det kan finnas skillnader mellan en skrivskyddad och skrivskyddad arbets belastning.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  Du kan använda [Azure Portal Frågeredigeraren](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)eller [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) för att köra T-SQL-frågor mot din Azure SQL Database.

1.  Anslut till Azure SQL Database. Du kan inte ändra databasens omfångs konfigurationer i Master-databasen.
  
2.  Välj **ny fråga** från standard fältet.   
  
3.  Kopiera och klistra in följande exempel till frågefönstret och välj **Kör**. 


#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP databas omfångs konfiguration

  I det här exemplet visas hur du fastställer den aktuella databasen MAXDOP-databasens omfångs konfiguration med hjälp av vyn [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) system katalog.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  I det här exemplet visas hur du kan använda [Alter Database områdes konfigurations](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) instruktion för att konfigurera `max degree of parallelism` alternativet till `8` . Inställningen utförs direkt.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Det här exemplet är till för användning med Azure SQL-databaser med [skalbara repliker aktiverade](read-scale-out.md), [geo-replikering](active-geo-replication-overview.md)och [Azure SQL Database storskaliga sekundära repliker](service-tier-hyperscale.md). Som exempel är den primära repliken inställd på en annan standard-MAXDOP som den sekundära repliken, vilket förväntar sig att det kan finnas skillnader mellan en skrivskyddad och skrivskyddad arbets belastning. `value_for_secondary`Kolumnen i `sys.database_scoped_configurations` innehåller inställningar för den sekundära repliken.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-frågetipset

  Det här exemplet visar hur du kör en fråga med hjälp av frågetipset för att tvinga fram `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP index alternativ

  Det här exemplet visar hur du återskapar ett index med hjälp av index alternativet för att tvinga fram `max degree of parallelism` `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Se även  
* [Ändra databas OMFATTNINGs konfiguration &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurera parallella index åtgärder](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Frågetipset &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Ange index alternativ](/sql/relational-databases/indexes/set-index-options)     
* [Förstå och lösa Azure SQL Database spärrnings problem](understand-resolve-blocking.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och justera för prestanda](/sql/relational-databases/performance/monitor-and-tune-for-performance)
