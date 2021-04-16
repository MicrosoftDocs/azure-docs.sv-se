---
title: Konfigurera maximal grad av parallellitet (MAXDOP)
titleSuffix: Azure SQL Database
description: Lär dig mer om maxgraden av parallellitet (MAXDOP).
ms.date: 04/12/2021
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
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536907"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurera den maximala graden av parallellitet (MAXDOP) i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  I den här artikeln beskrivs **maxgraden av parallellitet (MAXDOP)** i Azure SQL Database. 

> [!NOTE]
> **Det här innehållet fokuserar på Azure SQL Database.** Azure SQL Database baseras på den senaste stabila versionen av Microsoft SQL Server-databasmotorn, så mycket av innehållet är liknande även om felsöknings- och konfigurationsalternativen skiljer sig åt. Mer information om MAXDOP i SQL Server finns [i Configure the max degree of parallelism Server Configuration Option](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Översikt
  MAXDOP styr intrafrågeparallellitet i databasmotorn. Högre MAXDOP-värden resulterar vanligtvis i fler parallella trådar per fråga och snabbare frågekörning. 

  I Azure SQL Database är maxdop-standardinställningen för varje ny enkel databas och elastisk pooldatabas 8. Den här standardinställningen förhindrar onödig resursanvändning, samtidigt som databasmotorn kan köra frågor snabbare med parallella trådar. Det är vanligtvis inte nödvändigt att ytterligare konfigurera MAXDOP i Azure SQL Database arbetsbelastningar, även om det kan ge fördelar som en avancerad prestandajusteringsövning.

> [!Note]
>   I september 2020 gjordes, baserat på år av telemetri i Azure SQL Database-tjänsten [](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)MAXDOP 8, standardvärdet för nya databaser , som det optimala värdet för bredast möjliga antal kundarbetsbelastningar. Den här standardinställningen hjälpte till att förhindra prestandaproblem på grund av överdriven parallellitet. Innan det var standardinställningen för nya databaser MAXDOP 0. MAXDOP ändrades inte automatiskt för befintliga databaser som skapats före september 2020.

  Om databasmotorn i allmänhet väljer att köra en fråga med parallellitet går körningstiden snabbare. Överflödig parallellitet kan dock förbruka ytterligare processorresurser utan att förbättra frågeprestanda. I stor skala kan överflödig parallellitet påverka frågeprestanda negativt för alla frågor som körs på samma databasmotorinstans. Traditionellt har det varit en vanlig prestandajusteringsövning att ange en övre gräns för parallellitet i SQL Server arbetsbelastningar.

  I följande tabell beskrivs databasmotorns beteende vid körning av frågor med olika MAXDOP-värden:

| MAXDOP | Beteende | 
|--|--|
| = 1 | Databasmotorn använder en enda serietråd för att köra frågor. Parallella trådar används inte. | 
| > 1 | Databasmotorn anger antalet ytterligare [schemaläggare](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) som ska användas av parallella trådar till MAXDOP-värdet, eller det totala antalet logiska processorer, beroende på vilket som är mindre. |
| = 0 | Databasmotorn anger antalet ytterligare [schemaläggare som](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) ska användas av parallella trådar till det totala antalet logiska processorer eller 64, beroende på vilket som är mindre. | 
| | |

> [!Note]
> Varje fråga körs med minst en schemaläggare och en arbetstråd i schemaläggaren.
>
> En fråga som körs med parallellitet använder ytterligare schemaläggare och ytterligare parallella trådar. Eftersom flera parallella trådar kan köras i samma schemaläggare kan det totala antalet trådar som används för att köra en fråga vara högre än det angivna MAXDOP-värdet eller det totala antalet logiska processorer. Mer information finns i Schemaläggning [av parallella uppgifter.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Överväganden  

-   I Azure SQL Database kan du ändra maxdop-standardvärdet:
    -   Använd **MAXDOP-frågetipset** [på frågenivå.](/sql/t-sql/queries/hints-transact-sql-query)     
    -   På databasnivå använder du konfigurationen **med MAXDOP-databasomfång.** [](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)

-   Långvariga överväganden SQL Server MAXDOP och [rekommendationer](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) gäller för Azure SQL Database. 

-   Indexåtgärder som skapar eller återskapar ett index, eller som släpper ett grupperat index, kan vara resurskrävande. Du kan åsidosätta databasens MAXDOP-värde för indexåtgärder genom att ange indexalternativet MAXDOP i `CREATE INDEX` `ALTER INDEX` instruktionen eller . MAXDOP-värdet tillämpas på -instruktionen vid körningen och lagras inte i indexmetadata. Mer information finns i [Konfigurera parallella indexåtgärder.](/sql/relational-databases/indexes/configure-parallel-index-operations)  
  
-   Förutom frågor och indexåtgärder styr konfigurationsalternativet för databasomfång för MAXDOP även parallellitet för andra instruktioner som kan använda parallell körning, till exempel DBCC CHECKTABLE, DBCC CHECKDB och DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Rekommendationer  

  Att ändra MAXDOP för databasen kan ha stor inverkan på frågeprestanda och resursutnyttjande, både positivt och negativt. Det finns dock inget enskilt MAXDOP-värde som är optimalt för alla arbetsbelastningar. [Rekommendationerna](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) för att ange MAXDOP är nyanserade och beror på många faktorer. 

  Vissa toppar för samtidiga arbetsbelastningar kan fungera bättre med en annan MAXDOP än andra. En korrekt konfigurerad MAXDOP bör minska risken för prestanda- och tillgänglighetsincidenter och i vissa fall minska kostnaderna genom att kunna undvika onödigt resursutnyttjande och därmed skala ned till ett lägre tjänstmål.

### <a name="excessive-parallelism"></a>Överdriven parallellitet

  En högre MAXDOP minskar ofta varaktigheten för CPU-intensiva frågor. Överdriven parallellitet kan dock försämra andra samtidiga arbetsbelastningsprestanda genom att andra frågor från CPU- och arbetstrådsresurser blir utsvultna. I extrema fall kan överdriven parallellitet förbruka alla databas- eller elastiska poolresurser, vilket orsakar tidsgränser för frågor, fel och programavbrott. 

> [!Tip]
> Vi rekommenderar att kunder undviker att ange MAXDOP till 0 även om det inte verkar orsaka problem för närvarande.

  Överdriven parallellitet blir mest problematiskt när det finns fler samtidiga begäranden än vad som kan stödjas av processor- och arbetstrådresurserna som tillhandahålls av tjänstmålet. Undvik MAXDOP 0 för att minska risken för potentiella framtida problem på grund av överdriven parallellitet om en databas skalas upp eller om framtida maskinvarugenerationer i Azure SQL Database tillhandahåller fler kärnor för samma mål för databastjänsten.

### <a name="modifying-maxdop"></a>Ändra MAXDOP 

  Om du bestämmer att en MAXDOP-inställning som skiljer sig från standardinställningen är optimal för din Azure SQL Database-arbetsbelastning kan du använda `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-instruktionen. Exempel finns i avsnittet [Exempel med Transact-SQL](#examples) nedan. Om du vill ändra MAXDOP till ett annat värde än standardvärdet för varje ny databas som du skapar lägger du till det här steget i databasdistributionsprocessen.

  Om MAXDOP som inte är standard endast har en liten delmängd frågor i arbetsbelastningen kan du åsidosätta MAXDOP på frågenivå genom att lägga till TIPSET ALTERNATIV (MAXDOP). Exempel finns i avsnittet [Exempel med Transact-SQL](#examples) nedan. 

  Testa dina MAXDOP-konfigurationsändringar noggrant med belastningstestning som involverar realistiska samtidiga frågebelastningar. 

  MAXDOP för primära och sekundära repliker kan konfigureras oberoende av varandra om olika MAXDOP-inställningar är optimala för dina skrivskyddade och skrivskyddade arbetsbelastningar. Detta gäller för Azure SQL Database [skrivskyddade,](read-scale-out.md) [geo-replikering och](active-geo-replication-overview.md)sekundära [hyperskala-repliker.](service-tier-hyperscale.md) Som standard ärver alla sekundära repliker MAXDOP-konfigurationen för den primära repliken.

## <a name="security"></a><a name="Security"></a> Säkerhet  
  
###  <a name="permissions"></a><a name="Permissions"></a> Behörigheter  
  Instruktionen måste köras som serveradministratör, som medlem i databasrollen , eller en `ALTER DATABASE SCOPED CONFIGURATION` användare som har beviljats `db_owner` `ALTER ANY DATABASE SCOPED CONFIGURATION` behörigheten.
 
## <a name="examples"></a>Exempel   

  I de här exemplen används **den senaste AdventureWorksLT-exempeldatabasen** när alternativet `SAMPLE` väljs för en ny enkel databas Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Konfiguration med MAXDOP-databasomfång   

  Det här exemplet visar hur du [använder ALTER DATABASE SCOPED CONFIGURATION-instruktionen](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) för att ange `MAXDOP` konfigurationen till `2` . Inställningen börjar gälla omedelbart för nya frågor. PowerShell-cmdleten [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) kör T-SQL-frågorna som ska ställas in och returnerar konfigurationen med MAXDOP-databasomfång. 

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

Det här exemplet används med Azure SQL-databaser med skrivskyddade skalbara repliker aktiverade, [geo-replikering](active-geo-replication-overview.md)Azure SQL Database sekundära [hyperskala-repliker](service-tier-hyperscale.md). [](read-scale-out.md) Till exempel anges den primära repliken till en annan standard-MAXDOP som den sekundära repliken, vilket för förutser att det kan finnas skillnader mellan en skrivskyddad arbetsbelastning och en skrivskyddad arbetsbelastning.

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
  
  Du kan använda [Azure Portal-frågeredigeraren,](connect-query-portal.md) [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)eller [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) för att köra T-SQL-frågor mot Azure SQL Database.

1.  Öppna ett nytt frågefönster.

2.  Anslut till databasen där du vill ändra MAXDOP. Du kan inte ändra konfigurationer med databasomfång i huvuddatabasen.
  
3.  Kopiera och klistra in följande exempel i frågefönstret och välj **Kör.** 

#### <a name="maxdop-database-scoped-configuration"></a>Konfiguration med MAXDOP-databasomfång

  Det här exemplet visar hur du fastställer konfigurationen för den aktuella databasens MAXDOP-databasomfång [med hjälp sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) av systemkatalogvyn.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Det här exemplet visar hur du använder [ALTER DATABASE SCOPED CONFIGURATION-instruktionen](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) för att `MAXDOP` ange konfigurationen till `8` . Inställningen utförs direkt.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Det här exemplet används med Azure SQL databaser med skrivskyddade skalbara repliker aktiverade, [geo-replikering](active-geo-replication-overview.md)och sekundära hyperskala-repliker. [](read-scale-out.md) [](service-tier-hyperscale.md) Den primära repliken är till exempel inställd på en annan MAXDOP än den sekundära repliken, vilket förutser att det kan finnas skillnader mellan skrivskyddade och skrivskyddade arbetsbelastningar. Alla instruktioner körs på den primära repliken. Kolumnen `value_for_secondary` i innehåller inställningar för den sekundära `sys.database_scoped_configurations` repliken.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-frågetips

  Det här exemplet visar hur du kör en fråga med frågetipset för att tvinga `max degree of parallelism` fram till `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP-indexalternativ

  Det här exemplet visar hur du återskapar ett index med indexalternativet för att tvinga `max degree of parallelism` fram till `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Se även  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurera parallella indexåtgärder](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Frågetips &#40;Transact-SQL-&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Ange indexalternativ](/sql/relational-databases/indexes/set-index-options)     
* [Förstå och lösa Azure SQL Database blockerande problem](understand-resolve-blocking.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och finjustera prestanda](/sql/relational-databases/performance/monitor-and-tune-for-performance)
