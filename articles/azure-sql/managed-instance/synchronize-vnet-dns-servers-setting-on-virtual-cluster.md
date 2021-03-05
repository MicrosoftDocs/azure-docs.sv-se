---
title: Synkronisera inställningen för DNS-servrar för virtuella nätverk på det virtuella SQL-hanterade instans klustret
description: Lär dig hur du ställer in DNS-servrar för virtuella nätverk på SQL-hanterade instanser av virtuella kluster.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173559"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Synkronisera inställningen för DNS-servrar för virtuella nätverk på det virtuella SQL-hanterade instans klustret
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln beskriver när och hur du synkroniserar inställningen för DNS-servrar för virtuella nätverk på ett virtuellt SQL-hanterat instans kluster.

## <a name="when-to-synchronize-the-dns-setting"></a>När ska DNS-inställningen synkroniseras

Det finns några scenarier (till exempel db mail, länkade servrar till andra SQL Server-instanser i din moln- eller hybridmiljö) som kräver att privata värdnamn matchas från SQL Managed Instance. I så fall behöver du konfigurera en anpassad DNS i Azure. Mer information finns i [Konfigurera en anpassad DNS för Azure SQL Managed Instance](custom-dns-configure.md).

Om den här ändringen implementeras efter att du har skapat ett [virtuellt kluster](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) som är värd för hanterad instans måste du synkronisera DNS-serverns inställningar på det virtuella klustret med den virtuella nätverks konfigurationen.

> [!IMPORTANT]
> Synkronisering av DNS-servrar påverkar alla hanterade instanser som finns i det virtuella klustret.

## <a name="how-to-synchronize-the-dns-setting"></a>Så här synkroniserar du DNS-inställningen

### <a name="azure-rbac-permissions-required"></a>Azure RBAC-behörigheter krävs

Användar synkronisering av DNS-serverkonfigurationen måste ha någon av följande Azure-roller:

- Prenumerations ägarens roll eller
- Rollen hanterad instans deltagare eller
- Anpassad roll med följande behörighet:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

Hämta virtuellt nätverk där DNS-servrarna har uppdaterats.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Använd PowerShell [-kommandot Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) för att synkronisera konfigurationen av DNS-servrar för alla virtuella kluster i under nätet.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Använda Azure CLI

Hämta virtuellt nätverk där DNS-servrarna har uppdaterats.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Använd Azure CLI [-kommandot AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) för att synkronisera DNS-serverkonfigurationen för alla virtuella kluster i under nätet.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du konfigurerar en anpassad DNS [Konfigurera en anpassad DNS för Azure SQL-hanterad instans](custom-dns-configure.md).
- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
