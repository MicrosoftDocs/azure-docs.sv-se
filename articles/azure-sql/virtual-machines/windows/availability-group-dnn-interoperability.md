---
title: Funktions samverkan med tillgänglighets grupper och DNN-lyssnare
description: 'Lär dig mer om ytterligare överväganden när du arbetar med vissa SQL Server funktioner och en DNN-lyssnare (Distributed Network Name) med Always on-tillgänglighets grupp på SQL Server på virtuella Azure-datorer. '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359412"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Funktions samverkan med AG och DNN-lyssnare 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Det finns vissa SQL Server funktioner som förlitar sig på ett hårdkodat virtuellt nätverks namn (VNN). När du använder DNN-lyssnaren (Distributed Network Name) med din Always on-tillgänglighetsgrupper och SQL Server på virtuella Azure-datorer kan det dock finnas några ytterligare överväganden. 

Den här artikeln beskriver SQL Server funktioner och interoperabilitet med tillgänglighets gruppens DNN-lyssnare. 


## <a name="client-drivers"></a>Klientdrivrutiner

För ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-och Node.js-drivrutiner måste användarna uttryckligen ange DNN lyssnar namn och port som server namn i anslutnings strängen. För att säkerställa snabb anslutning vid redundans lägger `MultiSubnetFailover=True` du till anslutnings strängen om SQL-klienten stöder det. 

## <a name="tools"></a>Verktyg

Användare av [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [SQLCMD](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)och [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) måste uttryckligen ange DNN-lyssnarens namn och port som server namn i anslutnings strängen för att ansluta till lyssnaren. 

Det finns för närvarande inte stöd för att skapa DNN-lyssnaren via SQL Server Management Studio (SSMS) GUI. 


## <a name="availability-groups-and-fci"></a>Tillgänglighets grupper och FCI

Du kan konfigurera en tillgänglighets grupp som alltid är tillgänglig genom att använda en FCI (failover Cluster instance) som en av replikerna. För att den här konfigurationen ska fungera med DNN-lyssnaren [måste kluster instansen även använda DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) eftersom det inte finns något sätt att ange den virtuella FCI-IP-adressen i IP-listan AG DNN. 

I den här konfigurationen måste URL: en för speglingens slut punkt för FCI-repliken använda FCI-DNN. Om FCI till exempel används som skrivskyddad replik måste den skrivskyddade dirigeringen till FCI-repliken använda FCI-DNN. 

Formatet för speglings slut punkten är: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Om t. ex. FCI DNN DNS-namnet är `dnnlsnr` och `5022` är porten för FCI speglade slut punkten, ser Transact-SQL-kodfragmentet till att skapa slut punkts-URL: en, som: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

På samma sätt är formatet för den skrivskyddade Dirigerings-URL: en: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Om t. ex. DNN DNS-namnet är `dnnlsnr` , och `1444` är porten som används av det skrivskyddade målet SQL Server FCI, ser t-SQL-kodfragmentet för att skapa den skrivskyddade Dirigerings-URL: en ut så här: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Du kan utelämna porten i URL: en om den är standard porten 1433. För en namngiven instans konfigurerar du en statisk port för den namngivna instansen och anger den i den skrivskyddade Dirigerings-URL: en.  

## <a name="distributed-availability-group"></a>Distribuerad tillgänglighets grupp

Distribuerade tillgänglighets grupper stöds för närvarande inte med DNN-lyssnaren. 

## <a name="replication"></a>Replikering

Replikering, sammanslagning och ögonblicks bild replikering alla stöd för att ersätta VNN-lyssnaren med DNN-lyssnaren och porten i replikeringsdata som ansluter till lyssnaren. 

Mer information om hur du använder replikering med tillgänglighets grupper finns i [utgivare och AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [PRENUMERANTer och AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)samt [distributör och AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

Både lokala och klustrade MSDTC stöds, men MSDTC använder en dynamisk port, vilket kräver en standard Azure Load Balancer för att konfigurera HA-porten. Därför måste den virtuella datorn använda en standard-IP-reservation, eller så kan den inte exponeras för Internet. 

Definiera två regler, en för RPC-slutpunktsmapparen Mapper port 135 och en för den verkliga MSDTC-porten. Efter redundansväxlingen ändrar du LB-regeln till den nya MSDTC-porten när den har ändrats på den nya noden. 

Om MSDTC är lokalt, se till att tillåta utgående kommunikation. 

## <a name="distributed-query"></a>Distribuerad fråga 

Distribuerad fråga förlitar sig på en länkad server som kan konfigureras med hjälp av AG DNN-lyssnare och port. Om porten inte är 1433 väljer du alternativet **Använd andra data källor** i SQL Server Management Studio (SSMS) när du konfigurerar den länkade servern. 

## <a name="filestream"></a>-

FILESTREAM stöds men inte för scenarier där användare kommer åt den begränsade fil resursen med hjälp av Windows-filapi. 

## <a name="filetable"></a>FileTable

FileTable stöds men inte för scenarier där användare kommer åt den begränsade fil resursen med hjälp av Windows-filapi. 

## <a name="linked-servers"></a>Länkade servrar

Konfigurera den länkade servern med hjälp av AG-DNN lyssnar namn och port. Om porten inte är 1433 väljer du alternativet **Använd andra data källor** i SQL Server Management Studio (SSMS) när du konfigurerar den länkade servern. 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar


- Vilken SQL Server version har stöd för AG DNN-lyssnare? 

   SQL Server 2019 CU8 och senare.

- Vad är den förväntade redundansväxlingen när DNN-lyssnaren används?

   Vid DNN-lyssnare blir redundansväxlingen bara redundansväxlingen i AG utan ytterligare tid (som avsöknings tid när du använder Azure Load Balancer).

- Finns det något versions krav för SQL-klienter som stöder DNN med OLEDB och ODBC?

   Vi rekommenderar `MultiSubnetFailover=True` stöd för anslutnings strängen för DNN-lyssnare. Den är tillgänglig från och med SQL Server 2012 (11. x).

- Krävs några SQL Server konfigurations ändringar för att jag ska kunna använda DNN-lyssnaren? 

   SQL Server kräver inte någon konfigurations ändring för att använda DNN, men vissa SQL Server funktioner kan kräva mer uppmärksamhet. 

- Stöder DNN kluster för flera undernät?

   Ja. Klustret binder DNN i DNS med de fysiska IP-adresserna för alla repliker i tillgänglighet oberoende av under nätet. SQL-klienten försöker med alla IP-adresser till DNS-namnet oavsett under nätet. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i: 

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

