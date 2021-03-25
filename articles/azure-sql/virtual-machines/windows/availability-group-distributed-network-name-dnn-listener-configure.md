---
title: Konfigurera DNN-lyssnare för tillgänglighets grupp
description: Lär dig hur du konfigurerar en DNN-lyssnare (Distributed Network Name) för att ersätta din VNN-lyssnare och dirigera trafik till din Always on-tillgänglighetsgrupper på SQL Server på den virtuella Azure-datorn.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bf5c4c39ea8f5705cc9788fdcf2cddd01dcb4087
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034715"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Konfigurera en DNN-lyssnare för en tillgänglighets grupp
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Med SQL Server på virtuella Azure-datorer dirigerar det distribuerade nätverks namnet (DNN) trafik till lämplig klustrad resurs. Det ger ett enklare sätt att ansluta till en Always on-tillgänglighets grupp (AG) än det virtuella nätverks namnet (VNN)-lyssnaren, utan att det behövs något Azure Load Balancer.

I den här artikeln lär du dig att konfigurera en DNN-lyssnare för att ersätta VNN-lyssnaren och dirigera trafik till din tillgänglighets grupp med SQL Server på virtuella Azure-datorer för hög tillgänglighet och haveri beredskap (HADR).

Funktionen DNN Listener är för närvarande endast tillgänglig från och med SQL Server 2019 CU8 på Windows Server 2016 och senare.

För ett alternativt anslutnings alternativ bör du överväga en [VNN-lyssnare och Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) i stället.

## <a name="overview"></a>Översikt

En lyssnare för ett distribuerat nätverks namn (DNN) ersätter det traditionella virtuella nätverks namnet (VNN) tillgänglighets grupps lyssnare när den används med [Always on-tillgänglighetsgrupper på SQL Server virtuella datorer](availability-group-overview.md). Detta eliminerar behovet av att en Azure Load Balancer dirigerar trafik, vilket fören klar distributionen, underhållet och förbättringen av redundansväxlingen.

Använd DNN-lyssnaren för att ersätta en befintlig VNN-lyssnare, eller Använd den tillsammans med en befintlig VNN-lyssnare, så att tillgänglighets gruppen har två distinkta anslutnings punkter – en med VNN lyssnar namn (och port om de inte är standard) och en med hjälp av DNN lyssnar namn och port.

> [!CAUTION]
> Routningens beteende när du använder en DNN skiljer sig när du använder en VNN. Använd inte port 1433. Mer information finns i avsnittet [port överväganden](#port-considerations) längre fram i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Innan du slutför stegen i den här artikeln bör du redan ha:

- SQL Server 2019 på CU8 eller senare, på Windows Server 2016 och senare
- Beslut om det distribuerade nätverks namnet är det lämpliga [anslutnings alternativet för din hadr-lösning](hadr-cluster-best-practices.md#connectivity).
- Konfigurerat din [tillgänglighets grupp för alltid](availability-group-overview.md). 
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps)har installerats. 
- Identifierade den unika port som du ska använda för DNN-lyssnaren. Porten som används för en DNN-lyssnare måste vara unik för alla repliker av tillgänglighets gruppen eller instansen av redundanskluster.  Ingen annan anslutning kan dela samma port.



## <a name="create-script"></a>Skapa skript

Använd PowerShell för att skapa resursen för distribuerade nätverks namn (DNN) och koppla den till din tillgänglighets grupp.

Det gör du på följande sätt:

1. Öppna en textredigerare, t.ex. Notepad.
1. Kopiera och klistra in följande skript:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Spara skriptet som en `.ps1` fil, till exempel `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Kör skript

Om du vill skapa DNN-lyssnaren kör du skriptet som överför i parametrarna för namnet på tillgänglighets gruppen, lyssnar namnet och porten.

Anta till exempel att du antar namnet på tillgänglighets gruppen `ag1` , lyssnarens namn `dnnlsnr` och lyssnar porten som `6789` , enligt följande steg:

1. Öppna ett kommando rads gränssnitts verktyg, till exempel kommando tolken eller PowerShell.
1. Navigera till den plats där du sparade `.ps1` skriptet, till exempel c:\Documents.
1. Kör skriptet: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Exempel:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Verifiera lyssnare

Använd antingen SQL Server Management Studio eller Transact-SQL för att bekräfta att din DNN-lyssnare har skapats.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Expandera **tillgänglighets grupps lyssnare** i [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att visa din DNN-lyssnare:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Visa DNN-lyssnaren under tillgänglighets grupps lyssnare i SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Använd Transact-SQL för att visa status för DNN-lyssnaren:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Värdet `1` för `is_distributed_network_name` anger att lyssnaren är en DNN-lyssnare (Distributed Network Name):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Använd sys.availability_group_listeners för att identifiera DNN-lyssnare som har värdet 1 i is_distributed_network_name":::

## <a name="update-connection-string"></a>Uppdatera anslutnings sträng

Uppdatera anslutnings strängar för program så att de ansluter till DNN-lyssnaren. Anslutnings strängar för DNN-lyssnare måste ange port numret för DNN. För att säkerställa snabb anslutning vid redundans lägger `MultiSubnetFailover=True` du till anslutnings strängen om SQL-klienten stöder det.

## <a name="test-failover"></a>Redundanstest

Testa redundansväxlingen för tillgänglighets gruppen för att säkerställa funktionaliteten.

Följ dessa steg om du vill testa redundans:

1. Anslut till DNN-lyssnaren eller en av replikerna genom att använda [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Expandera **Always on tillgänglighets grupp** i **Object Explorer**.
1. Högerklicka på tillgänglighets gruppen och välj **redundans** för att öppna **guiden redundansväxla**.
1. Följ anvisningarna för att välja ett mål för redundans och redundansväxla tillgänglighets gruppen till en sekundär replik.
1. Bekräfta att databasen är i ett synkroniserat tillstånd på den nya primära repliken.
1. Valfritt Växla tillbaka till den ursprungliga primära eller en annan sekundär replik.

## <a name="test-connectivity"></a>Testa anslutning

Testa anslutningen till DNN-lyssnaren med följande steg:

1. Öppna [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Anslut till din DNN-lyssnare.
1. Öppna ett nytt frågefönster och kontrol lera vilken replik som du är ansluten till genom att köra `SELECT @@SERVERNAME` .
1. Rapportera inte tillgänglighets gruppen över till en annan replik.
1. Efter en rimlig tid kan `SELECT @@SERVERNAME` du köra för att bekräfta att din tillgänglighets grupp nu finns på en annan replik.

## <a name="limitations"></a>Begränsningar

- För närvarande stöds en DNN-lyssnare för en tillgänglighets grupp bara för SQL Server 2019 CU8 och senare på Windows Server 2016 och senare. 
- DNN-lyssnare **måste** konfigureras med en unik port.  Porten kan inte delas med någon annan anslutning på någon replik.
- Det kan finnas ytterligare överväganden när du arbetar med andra SQL Server funktioner och en tillgänglighets grupp med en DNN. Mer information finns i [AG med DNN-interoperabilitet](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Port överväganden

DNN-lyssnare har utformats för att lyssna på alla IP-adresser, men på en speciell, unik port. DNS-posten för lyssnar namnet måste matcha adresserna för alla repliker i tillgänglighets gruppen. Detta görs automatiskt med PowerShell-skriptet som anges i avsnittet [skapa skript](#create-script) . Eftersom DNN-lyssnare accepterar anslutningar på alla IP-adresser är det viktigt att lyssnar porten är unik och inte används av någon annan replik i tillgänglighets gruppen. Eftersom SQL Server alltid lyssnar på port 1433, antingen direkt eller via SQL Browser-tjänsten, kan port 1433 inte användas för någon DNN-lyssnare.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du SQL Server HADR-funktioner i Azure i [tillgänglighets grupper](availability-group-overview.md) och [instansen av redundanskluster](failover-cluster-instance-overview.md). Du kan också lära dig [metod tips](hadr-cluster-best-practices.md) för att konfigurera din miljö för hög tillgänglighet och haveri beredskap. 
