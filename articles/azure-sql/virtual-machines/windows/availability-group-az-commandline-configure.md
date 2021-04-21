---
title: Konfigurera en tillgänglighetsgrupp (PowerShell & Az CLI)
description: Använd antingen PowerShell eller Azure CLI för att skapa Windows-redundansklustret, tillgänglighetsgruppens lyssnare och den interna lastbalanseraren på en SQL Server VM i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766941"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Använd PowerShell eller Az CLI för att konfigurera en tillgänglighetsgrupp för SQL Server på virtuella Azure-datorer 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du använder [PowerShell](/powershell/scripting/install/installing-powershell) eller [Azure CLI](/cli/azure/sql/vm) för att distribuera ett Windows-redundanskluster, lägger till virtuella SQL Server-datorer i klustret och skapar den interna lastbalanseraren och lyssnaren för en Always On-tillgänglighetsgrupp. 

Distribution av tillgänglighetsgruppen görs fortfarande manuellt via SQL Server Management Studio (SSMS) eller Transact-SQL (T-SQL). 

Den här artikeln använder PowerShell och Az CLI för att konfigurera tillgänglighetsgruppens miljö, men det är också [](availability-group-manually-configure-tutorial.md) möjligt att göra det från [Azure Portal,](availability-group-azure-portal-configure.md)med hjälp av [Azure-snabbstartsmallar](availability-group-quickstart-template-configure.md)eller manuellt också. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera en Always On-tillgänglighetsgrupp måste du ha följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resursgrupp med en domänkontrollant. 
- En eller flera domänanslutnings-VM:ar i Azure som kör [SQL Server 2016 (eller senare) Enterprise-version](./create-sql-vm-portal.md) i samma tillgänglighetsuppsättning eller olika tillgänglighetszoner som har registrerats med [SQL IaaS Agent-tillägget](sql-agent-extension-manually-register-single-vm.md).    
- Den senaste versionen av [PowerShell](/powershell/scripting/install/installing-powershell) eller [Azure CLI.](/cli/azure/install-azure-cli) 
- Två tillgängliga IP-adresser (används inte av någon entitet). En är för den interna lastbalanseraren. Den andra är för tillgänglighetsgruppens lyssnare i samma undernät som tillgänglighetsgruppen. Om du använder en befintlig lastbalanserare behöver du bara en tillgänglig IP-adress för tillgänglighetsgruppens lyssnare. 

## <a name="permissions"></a>Behörigheter

Du behöver följande kontobehörigheter för att konfigurera Always On-tillgänglighetsgruppen med hjälp av Azure CLI: 

- Ett befintligt domänanvändarkonto som **har behörigheten Skapa datorobjekt** i domänen. Till exempel har ett domänadministratörskonto vanligtvis tillräcklig behörighet (till exempel: account@domain.com ). _Det här kontot bör också ingå i den lokala administratörsgruppen på varje virtuell dator för att skapa klustret._
- Domänanvändarkontot som styr SQL Server. 
 
## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

Klustret behöver ett lagringskonto för att fungera som molnvittne. Du kan använda ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Om du vill använda ett befintligt lagringskonto kan du gå vidare till nästa avsnitt. 

Följande kodfragment skapar lagringskontot: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Du kan se felet `az sql: 'vm' is not in the 'az sql' command group` om du använder en inaktuell version av Azure CLI. Hämta den [senaste versionen av Azure CLI för](/cli/azure/install-azure-cli-windows) att komma förbi det här felet.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definiera klustermetadata

Kommandogruppen Azure CLI [az sql vm group](/cli/azure/sql/vm/group) hanterar metadata för den WSFC-tjänst (Windows Server Failover Cluster) som är värd för tillgänglighetsgruppen. Klustermetadata omfattar Active Directory-domänen, klusterkonton, lagringskonton som ska användas som molnvittne och SQL Server version. Använd [az sql vm group create](/cli/azure/sql/vm/group#az_sql_vm_group_create) för att definiera metadata för WSFC så att klustret skapas enligt definitionen när den första SQL Server VM läggs till. 

Följande kodfragment definierar metadata för klustret:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Lägga till virtuella datorer i klustret

När du lägger SQL Server VM till klustret skapas klustret. Kommandot [az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) skapar klustret med det tidigare angivna namnet, installerar klusterrollen på de virtuella SQL Server-datorerna och lägger till dem i klustret. Efterföljande användningsområden för `az sql vm add-to-group` kommandot lägger till SQL Server virtuella datorer i det nyligen skapade klustret. 

Följande kodfragment skapar klustret och lägger till det första SQL Server VM det: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Använd det här kommandot för att lägga till SQL Server virtuella datorer i klustret. Ändra endast `-n` parametern för SQL Server VM namn. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Verifiera kluster 

För att ett redundanskluster ska stödjas av Microsoft måste det klara klustervalidering. Anslut till den virtuella datorn med önskad metod, till exempel Remote Desktop Protocol (RDP) och kontrollera att klustret godkänns i valideringen innan du fortsätter. Om du inte gör det är klustret i ett tillstånd som inte stöds. 

Du kan verifiera klustret med hjälp Klusterhanteraren för växling vid fel (FCM) eller följande PowerShell-kommando:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Skapa en tillgänglighetsgrupp

Skapa tillgänglighetsgruppen manuellt som vanligt med hjälp av [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)eller [Transact-SQL.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> Skapa *inte* en lyssnare just nu eftersom detta görs via Azure CLI i följande avsnitt.  

## <a name="create-internal-load-balancer"></a>Skapa en intern lastbalanserare

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Lyssnaren för Always On-tillgänglighetsgruppen kräver en intern instans av Azure Load Balancer. Den interna lastbalanseraren tillhandahåller en "flytande" IP-adress för tillgänglighetsgruppens lyssnare som möjliggör snabbare redundans och återanslutning. Om de SQL Server virtuella datorerna i en tillgänglighetsgrupp ingår i samma tillgänglighetsuppsättning kan du använda en Basic-lastbalanserare. Annars måste du använda en standardlastbalanserare.  

> [!NOTE]
> Den interna lastbalanseraren ska finnas i samma virtuella nätverk som SQL Server VM instanser. 

Följande kodfragment skapar den interna lastbalanseraren:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Den offentliga IP-resursen för varje SQL Server VM måste ha en Standard-SKU för att vara kompatibel med Standard Load Balancer. Om du vill fastställa SKU för den virtuella datorns offentliga IP-resurs går du till Resursgrupp, väljer din offentliga IP-adressresurs för önskad SQL Server VM och letar upp värdet under **SKU** i fönstret **Översikt.**   

## <a name="create-listener"></a>Skapa lyssnare

När du har skapat tillgänglighetsgruppen manuellt kan du skapa lyssnaren med hjälp av [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create). 

*Resurs-ID:t för undernätet* är värdet `/subnets/<subnetname>` för som läggs till i resurs-ID:t för den virtuella nätverksresursen. Så här identifierar du undernätets resurs-ID:
   1. Gå till din resursgrupp i [Azure Portal](https://portal.azure.com). 
   1. Välj den virtuella nätverksresursen. 
   1. Välj **Egenskaper** i **fönstret** Inställningar. 
   1. Identifiera resurs-ID:t för det virtuella nätverket och lägg till `/subnets/<subnetname>` i slutet av det för att skapa undernätets resurs-ID. Exempel:
      - Resurs-ID:t för det virtuella nätverket är: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Undernätets namn är: `default`
      - Därför är ditt undernäts resurs-ID: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Följande kodfragment skapar tillgänglighetsgruppens lyssnare:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Ändra antalet repliker 
Det finns ett extra lager av komplexitet när du distribuerar en tillgänglighetsgrupp till en SQL Server virtuella datorer som finns i Azure. Resursprovidern och den virtuella datorgruppen hanterar nu resurserna. När du lägger till eller tar bort repliker i tillgänglighetsgruppen finns det därför ytterligare ett steg för att uppdatera lyssnarmetadata med information om de virtuella SQL Server datorerna. När du ändrar antalet repliker i tillgänglighetsgruppen måste du också använda kommandot [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) för att uppdatera lyssnaren med metadata för de virtuella SQL Server datorerna. 


### <a name="add-a-replica"></a>Lägga till en replik

Så här lägger du till en ny replik i tillgänglighetsgruppen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Lägg SQL Server VM till klustergruppen:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Använd SQL Server Management Studio för att lägga SQL Server instansen som en replik i tillgänglighetsgruppen.
1. Lägg till SQL Server VM metadata i lyssnaren:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Lägg SQL Server VM till klustergruppen:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Använd SQL Server Management Studio att lägga SQL Server instansen som en replik i tillgänglighetsgruppen.
1. Lägg SQL Server VM metadata till lyssnaren:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Ta bort en replik

Så här tar du bort en replik från tillgänglighetsgruppen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ta bort repliken från tillgänglighetsgruppen med hjälp av SQL Server Management Studio. 
1. Ta bort SQL Server VM metadata från lyssnaren:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Ta bort SQL Server VM från klustret:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ta bort repliken från tillgänglighetsgruppen med hjälp av SQL Server Management Studio. 
1. Ta bort SQL Server VM metadata från lyssnaren:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Ta bort SQL Server VM från klustret:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Ta bort lyssnare
Om du senare behöver ta bort tillgänglighetsgruppens lyssnare som konfigurerats med Azure CLI måste du gå igenom SQL IaaS Agent-tillägget. Eftersom lyssnaren är registrerad via SQL IaaS Agent-tillägget räcker det inte att ta bort den via SQL Server Management Studio. 

Den bästa metoden är att ta bort den via SQL IaaS Agent-tillägget med hjälp av följande kodfragment i Azure CLI. Detta tar bort metadata för tillgänglighetsgruppens lyssnare från SQL IaaS Agent-tillägget. Dessutom tas lyssnaren bort fysiskt från tillgänglighetsgruppen. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Ta bort kluster

Ta bort alla noder från klustret för att förstöra det och ta sedan bort klustermetadata från SQL IaaS Agent-tillägget. Du kan göra det med hjälp av Azure CLI eller PowerShell. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta först bort alla SQL Server virtuella datorerna från klustret: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Om det här är de enda virtuella datorerna i klustret förstörs klustret. Om det finns andra virtuella datorer i klustret förutom de virtuella SQL Server SQL Server-datorer som har tagits bort tas inte de andra virtuella datorerna bort och klustret förstörs inte. 

Ta sedan bort klustermetadata från SQL IaaS Agent-tillägget: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta först bort alla SQL Server virtuella datorer från klustret. Detta tar fysiskt bort noderna från klustret och förstör klustret: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Om det här är de enda virtuella datorerna i klustret förstörs klustret. Om det finns andra virtuella datorer i klustret förutom de virtuella SQL Server SQL Server-datorer som har tagits bort tas inte de andra virtuella datorerna bort och klustret förstörs inte. 

Ta sedan bort klustermetadata från SQL IaaS Agent-tillägget: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över virtuella SQL Server datorer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar SQL Server virtuella datorer](frequently-asked-questions-faq.md)
* [Information om virtuella SQL Server datorer](../../database/doc-changes-updates-release-notes.md)
* [Växla licensieringsmodeller för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Översikt över Always On-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguration av en serverinstans för Always On-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration av en &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Övervakning av tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Översikt över Transact-SQL-uttryck för Always On-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Översikt över PowerShell-cmdlets för Always On-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)