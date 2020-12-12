---
title: Funktions samverkan med SQL Server FCI & DNN
description: 'Lär dig mer om ytterligare överväganden när du arbetar med vissa SQL Server-funktioner och en DNN-resurs (Distributed Network Name) med en instans av redundanskluster på SQL Server på virtuella Azure-datorer. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358222"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Funktions samverkan med SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Det finns vissa SQL Server funktioner som förlitar sig på ett hårdkodat virtuellt nätverks namn (VNN). När du använder DNN-resursen (Distributed Network Name) med din instansen av redundanskluster och SQL Server på virtuella Azure-datorer finns det några ytterligare överväganden. 

I den här artikeln lär du dig hur du konfigurerar nätverks Ali Aset när du använder DNN-resursen, samt vilka SQL Server funktioner som kräver ytterligare överväganden.

## <a name="create-network-alias-fci"></a>Skapa ett nätverks Ali Aset (FCI)

Vissa komponenter på Server sidan är beroende av ett hårdkodat VNN-värde och kräver ett nätverks Ali Aset som mappar VNN till DNN DNS-namn för att fungera korrekt. Följ stegen i [skapa ett Server Ali Aset](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) för att skapa ett alias som mappar VNN till DNS-namnet DNN. 

För en standard instans kan du mappa VNN till DNN DNS-namnet direkt, t. ex. VNN = DNN DNS-namn.
Om VNN namn t. ex. är, `FCI1` `MSSQLSERVER` och DNN är `FCI1DNN` (klienter som tidigare var anslutna till `FCI` , och nu ansluter till `FCI1DNN` ), mappar du VNN `FCI1` till DNN `FCI1DNN` . 

För en namngiven instans bör mappning av nätverks Ali Aset utföras för den fullständiga instansen, till exempel `VNN\Instance`  =  `DNN\Instance` . Om VNN namn t. ex. är, `FCI1` `instA` och DNN är `FCI1DNN` (klienter som tidigare var anslutna till `FCI1\instA` , och nu ansluter till `FCI1DNN\instaA` ), mappar du VNN `FCI1\instaA` till DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Klientdrivrutiner

För ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-och Node.js-drivrutiner måste användare uttryckligen ange DNN DNS-namnet som server namn i anslutnings strängen. För att säkerställa snabb anslutning vid redundans lägger `MultiSubnetFailover=True` du till anslutnings strängen om SQL-klienten stöder det. 

## <a name="tools"></a>Verktyg

Användare av [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [SQLCMD](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)och [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) måste uttryckligen ange DNN DNS-namnet som server namn i anslutnings strängen. 

## <a name="availability-groups-and-fci"></a>Tillgänglighets grupper och FCI

Du kan konfigurera en tillgänglighets grupp som alltid är tillgänglig genom att använda en instans av ett redundanskluster som en av replikerna. I den här konfigurationen måste URL: en för speglingens slut punkt för FCI-repliken använda FCI-DNN. Om FCI till exempel används som skrivskyddad replik måste den skrivskyddade dirigeringen till FCI-repliken använda FCI-DNN. 

Formatet för speglings slut punkten är: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Om t. ex. DNN DNS-namn är `dnnlsnr` , och `5022` är porten för FCIs speglings slut punkt, ser Transact-SQL-kodfragmentet till att skapa slut punkts-URL: en, som: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

På samma sätt är formatet för den skrivskyddade Dirigerings-URL: en: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Om t. ex. DNN DNS-namnet är `dnnlsnr` , och `1444` är porten som används av det skrivskyddade målet SQL Server FCI, ser t-SQL-kodfragmentet för att skapa den skrivskyddade Dirigerings-URL: en ut så här: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Du kan utelämna porten i URL: en om den är standard porten 1433. För en namngiven instans konfigurerar du en statisk port för den namngivna instansen och anger den i den skrivskyddade Dirigerings-URL: en.  

## <a name="replication"></a>Replikering

Replikeringen har tre komponenter: utgivare, distributör, prenumerant. Någon av dessa komponenter kan vara en instans av en redundanskluster. Eftersom FCI-VNN används kraftigt i replikeringskonfiguration, både explicit och implicit, kan ett aliasnamn som mappar VNN till DNN behövas för att replikeringen ska fungera. 

Fortsätt att använda VNN-namnet som FCI namn inom replikering, men skapa ett alias i följande fjärrsituationer *innan du konfigurerar replikering*:

| **Replikeringspartner (FCI med DNN)** | **Fjärrkomponent** | **Mappning av nätverks Ali Aset** | **Server med nätverks mappning**| 
|---------|---------|---------|-------- | 
|Publisher | Möjligheter | Publisher-VNN till Publisher DNN| Möjligheter| 
|Möjligheter|Prenumerant |Distributör VNN till distributör DNN| Prenumerant | 
|Möjligheter|Publisher | Distributör VNN till distributör DNN | Publisher| 
|Prenumerant| Möjligheter| Prenumerantens VNN till DNN | Möjligheter| 

Anta till exempel att du har en utgivare som är konfigurerad som en FCI med DNN i en replikeringstopologi och att distributören är fjärran sluten. I det här fallet skapar du ett nätverks Ali Aset på distributörs servern för att mappa utgivarens VNN till utgivarens DNN: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Konfigurera DNS-namnet DNN som det alias som använder Konfigurationshanteraren för SQL Server." :::

Använd det fullständiga instans namnet för en namngiven instans, t. ex. följande avbildnings exempel: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Använd det fullständiga instans namnet när du konfigurerar ett nätverks Ali Aset för en namngiven instans." :::

## <a name="database-mirroring"></a>Databasspegling

Du kan konfigurera databas spegling med en FCI som en databas speglings partner. Konfigurera den med hjälp av [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) i stället för SQL Server Management Studio GUI. Med hjälp av T-SQL ser du till att slut punkten för databas spegling skapas med hjälp av DNN i stället för VNN. 

Om t. ex. DNN DNS-namn är `dnnlsnr` och databas speglingens slut punkt är 7022, konfigurerar följande T-SQL-kodfragment databas speglings partnern: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

För klient åtkomst kan egenskapen för **redundansrelationen** hantera växling vid fel i databas spegling, men inte FCI redundans. 

## <a name="msdtc"></a>MSDTC

FCI kan delta i distribuerade transaktioner koordinerade av Microsoft koordinator för distribuerad transaktion (MSDTC). Även om både klustrade MSDTC och lokala MSDTC stöds med FCI DNN, i Azure, är en belastningsutjämnare fortfarande nödvändig för klustrade MSDTC. DNN som definieras i FCI ersätter inte Azure Load Balancer kravet för klustrade MSDTC i Azure. 

## <a name="filestream"></a>-

Även om FileStream stöds för en databas i en FCI, stöds inte åtkomst till FileStream eller FileTable med hjälp av API: er för fil system med DNN. 

## <a name="linked-servers"></a>Länkade servrar

Det finns stöd för att använda en länkad server med en FCI-DNN. Använd antingen DNN direkt för att konfigurera en länkad server eller Använd ett nätverks Ali Aset för att mappa VNN till DNN. 


Om du till exempel vill skapa en länkad server med DNN DNS-namn `dnnlsnr` för namngiven instans `insta1` använder du följande Transact-SQL-kommando (t-SQL):

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Du kan också skapa den länkade servern med hjälp av det virtuella nätverks namnet (VNN) i stället, men du måste definiera ett nätverks Ali Aset för att mappa VNN till DNN. 

Om du till exempel `insta1` `vnnname` `dnnlsnr` vill skapa en länkad server med hjälp av namnet VNN använder du följande Transact-SQL-kommando (t-SQL) för instans namn, VNN och DNN:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Skapa sedan ett nätverks Ali Aset att mappa `vnnname\insta1` till `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Vanliga frågor och svar


- Vilken SQL Server version har stöd för DNN? 

   SQL Server 2019 CU2 och senare.

- Vad är den förväntade redundansväxlingen när DNN används?

   Vid DNN blir redundansväxlingen bara den FCI redundansväxlingen, utan att någon tid läggs till (t. ex. avsöknings tid när du använder Azure Load Balancer).

- Finns det något versions krav för SQL-klienter som stöder DNN med OLEDB och ODBC?

   Vi rekommenderar `MultiSubnetFailover=True` stöd för anslutnings strängar för DNN. Den är tillgänglig från och med SQL Server 2012 (11. x).

- Krävs några SQL Server konfigurations ändringar för att jag ska kunna använda DNN? 

   SQL Server kräver inte någon konfigurations ändring för att använda DNN, men vissa SQL Server funktioner kan kräva mer uppmärksamhet. 

- Stöder DNN kluster för flera undernät?

   Ja. Klustret binder DNN i DNS med de fysiska IP-adresserna för alla noder i klustret, oavsett under nätet. SQL-klienten försöker med alla IP-adresser till DNS-namnet oavsett under nätet. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i: 

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

