---
title: Konfigurera en tillgänglighets grupp (PowerShell & AZ CLI)
description: Använd antingen PowerShell eller Azure CLI för att skapa Windows-redundansklustret, tillgänglighets gruppens lyssnare och den interna belastningsutjämnaren på en SQL Server VM i Azure.
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
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359973"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Använd PowerShell eller AZ CLI för att konfigurera en tillgänglighets grupp för SQL Server på Azure VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du använder [PowerShell](/powershell/scripting/install/installing-powershell) eller [Azure CLI](/cli/azure/sql/vm) för att distribuera ett Windows-redundanskluster, lägger till SQL Server virtuella datorer i klustret och skapar den interna belastningsutjämnaren och lyssnare för en tillgänglighets grupp som alltid är tillgänglig. 

Distribution av tillgänglighets gruppen görs fortfarande manuellt via SQL Server Management Studio (SSMS) eller Transact-SQL (T-SQL). 

Den här artikeln använder PowerShell och AZ CLI för att konfigurera tillgänglighets grupps miljön, men det är också möjligt att göra det från [Azure Portal](availability-group-azure-portal-configure.md), med hjälp av [Azure snabb starts mallar](availability-group-quickstart-template-configure.md)eller [manuellt](availability-group-manually-configure-tutorial.md) . 

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera en tillgänglighets grupp som alltid är tillgänglig måste du ha följande krav: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resurs grupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQL Server 2016 (eller senare) Enterprise Edition](./create-sql-vm-portal.md) i *samma* tillgänglighets uppsättning eller *olika* tillgänglighets zoner som har [registrerats med SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md).  
- Den senaste versionen av [PowerShell](/powershell/scripting/install/installing-powershell) eller [Azure CLI](/cli/azure/install-azure-cli). 
- Två tillgängliga (används inte av någon entitet) IP-adresser. En är för den interna belastningsutjämnaren. Det andra är för tillgänglighets gruppens lyssnare i samma undernät som tillgänglighets gruppen. Om du använder en befintlig belastningsutjämnare behöver du bara en tillgänglig IP-adress för tillgänglighets gruppens lyssnare. 

## <a name="permissions"></a>Behörigheter

Du behöver följande konto behörigheter för att konfigurera tillgänglighets gruppen Always on med hjälp av Azure CLI: 

- Ett befintligt domän användar konto som har behörighet att **skapa dator objekt** i domänen. Till exempel har ett domän administratörs konto vanligt vis tillräcklig behörighet (till exempel: account@domain.com ). _Detta konto bör också vara en del av den lokala administratörs gruppen på varje virtuell dator för att skapa klustret._
- Domän användar kontot som styr SQL Server. 
 
## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

Klustret behöver ett lagrings konto för att fungera som moln vittne. Du kan använda ett befintligt lagrings konto, eller så kan du skapa ett nytt lagrings konto. Om du vill använda ett befintligt lagrings konto kan du gå vidare till nästa avsnitt. 

Följande kodfragment skapar lagrings kontot: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Du kan se felet `az sql: 'vm' is not in the 'az sql' command group` om du använder en inaktuell version av Azure CLI. Hämta den [senaste versionen av Azure CLI](/cli/azure/install-azure-cli-windows) för att få ett tidigare fel.


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

## <a name="define-cluster-metadata"></a>Definiera kluster metadata

Kommando gruppen Azure CLI [AZ SQL VM Group](/cli/azure/sql/vm/group) hanterar metadata för WSFC-tjänsten (Windows Server failover Cluster) som är värd för tillgänglighets gruppen. I kluster metadata ingår Active Directory domän, kluster konton, lagrings konton som ska användas som moln vittne och SQL Server version. Använd [AZ SQL VM Group Create](/cli/azure/sql/vm/group#az-sql-vm-group-create) för att definiera metadata för WSFC, så att när den första SQL Server VM läggs till skapas klustret enligt definitionen. 

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

Om du lägger till den första SQL Server VM till klustret skapas klustret. Kommandot [AZ SQL VM Add-to-Group](/cli/azure/sql/vm#az-sql-vm-add-to-group) skapar klustret med det namn som tidigare angavs, installerar kluster rollen på de virtuella datorerna SQL Server och lägger till dem i klustret. Efterföljande användning av `az sql vm add-to-group` kommandot lägger till fler SQL Server virtuella datorer i det nya klustret. 

Följande kodfragment skapar klustret och lägger till den första SQL Server VM: 

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
Använd det här kommandot om du vill lägga till andra SQL Server virtuella datorer i klustret. Ändra endast `-n` parametern för SQL Server VM namn. 

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

För att ett redundanskluster ska kunna stödjas av Microsoft måste det klara kluster valideringen. Anslut till den virtuella datorn med den metod du föredrar, till exempel Remote Desktop Protocol (RDP) och kontrol lera att klustret klarar verifieringen innan du fortsätter. Om du inte gör det så låter klustret vara i ett tillstånd som inte stöds. 

Du kan verifiera klustret med Klusterhanteraren för växling vid fel (FCM) eller följande PowerShell-kommando:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Skapa tillgänglighets grupp

Skapa tillgänglighets gruppen manuellt som vanligt, genom att använda [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)eller [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Skapa *inte* en lyssnare just nu eftersom detta görs via Azure CLI i följande avsnitt.  

## <a name="create-internal-load-balancer"></a>Skapa intern belastningsutjämnare

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Lyssnaren för Always on-tillgänglighetsgrupper kräver en intern instans av Azure Load Balancer. Den interna belastningsutjämnaren innehåller en "flytande" IP-adress för tillgänglighets gruppens lyssnare som möjliggör snabbare redundans och åter anslutning. Om SQL Server virtuella datorer i en tillgänglighets grupp ingår i samma tillgänglighets uppsättning kan du använda en grundläggande belastningsutjämnare. Annars måste du använda en standard belastningsutjämnare.  

> [!NOTE]
> Den interna belastningsutjämnaren bör finnas i samma virtuella nätverk som SQL Server VM-instanserna. 

Följande kodfragment skapar den interna belastningsutjämnaren:

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
> Den offentliga IP-resursen för varje SQL Server VM måste ha en standard-SKU för att vara kompatibel med standard belastnings utjämningen. Ta reda på SKU: n för den virtuella datorns offentliga IP-resurs genom att gå till **resurs grupp**, välja din **offentliga IP** -adressresurs för önskad SQL Server VM och leta upp värdet under **SKU** i fönstret **Översikt** .  

## <a name="create-listener"></a>Skapa lyssnare

När du har skapat tillgänglighets gruppen manuellt kan du skapa en lyssnare med hjälp av [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create). 

*Under näts resurs-ID: t* är värdet som `/subnets/<subnetname>` läggs till i resurs-ID: t för den virtuella nätverks resursen. Så här identifierar du under nätets resurs-ID:
   1. Gå till din resurs grupp i [Azure Portal](https://portal.azure.com). 
   1. Välj den virtuella nätverks resursen. 
   1. Välj **Egenskaper** i fönstret **Inställningar** . 
   1. Identifiera resurs-ID för det virtuella nätverket och Lägg till i `/subnets/<subnetname>` slutet av det för att skapa ett under näts resurs-ID. Exempel:
      - Ditt virtuella nätverks resurs-ID är: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Under nätets namn är: `default`
      - Därför är ditt under näts resurs-ID: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Följande kodfragment skapar tillgänglighets gruppens lyssnare:

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

## <a name="modify-number-of-replicas"></a>Ändra antal repliker 
Det finns ett extra lager som är komplext när du distribuerar en tillgänglighets grupp till SQL Server virtuella datorer som finns i Azure. Resurs leverantören och gruppen för virtuella datorer hanterar nu resurserna. När du lägger till eller tar bort repliker i tillgänglighets gruppen finns det ytterligare ett steg i att uppdatera lyssnar-metadata med information om SQL Server virtuella datorer. När du ändrar antalet repliker i tillgänglighets gruppen måste du också använda kommandot [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) för att uppdatera lyssnaren med metadata för de virtuella datorerna i SQL Server. 


### <a name="add-a-replica"></a>Lägg till en replik

Så här lägger du till en ny replik i tillgänglighets gruppen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Lägg till SQL Server VM i kluster gruppen:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Använd SQL Server Management Studio för att lägga till SQL Server-instansen som en replik i tillgänglighets gruppen.
1. Lägg till SQL Server VM metadata till lyssnaren:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Lägg till SQL Server VM i kluster gruppen:

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

1. Använd SQL Server Management Studio för att lägga till SQL Server-instansen som en replik i tillgänglighets gruppen.
1. Lägg till SQL Server VM metadata till lyssnaren:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Ta bort en replik

Så här tar du bort en replik från tillgänglighets gruppen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ta bort repliken från tillgänglighets gruppen med hjälp av SQL Server Management Studio. 
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

1. Ta bort repliken från tillgänglighets gruppen med hjälp av SQL Server Management Studio. 
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
Om du senare behöver ta bort tillgänglighets gruppens lyssnare som kon figurer ATS med Azure CLI måste du gå igenom SQL IaaS agent-tillägget. Eftersom lyssnaren har registrerats via SQL IaaS agent-tillägget tar du bara bort det via SQL Server Management Studio är otillräckligt. 

Den bästa metoden är att ta bort den via SQL IaaS agent-tillägget med hjälp av följande kodfragment i Azure CLI. Om du gör det tas tillgänglighets gruppens lyssnar metadata bort från SQL IaaS agent-tillägget. Det tar också bort en lyssnare från tillgänglighets gruppen fysiskt. 

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

Ta bort alla noder från klustret för att förstöra det och ta sedan bort de klustrade metadata från SQL IaaS agent-tillägget. Du kan göra det med hjälp av Azure CLI eller PowerShell. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta först bort alla SQL Server virtuella datorer från klustret: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Om dessa är de enda virtuella datorerna i klustret kommer klustret att förstöras. Om det finns andra virtuella datorer i klustret, förutom SQL Server de virtuella datorer som har tagits bort, kommer de andra virtuella datorerna inte att tas bort och klustret kommer inte att förstöras. 

Ta sedan bort de klustrade metadatana från SQL-IaaS agent-tillägg: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta först bort alla SQL Server virtuella datorer från klustret. Detta tar bort noderna från klustret fysiskt och förstör klustret: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Om dessa är de enda virtuella datorerna i klustret kommer klustret att förstöras. Om det finns andra virtuella datorer i klustret, förutom SQL Server de virtuella datorer som har tagits bort, kommer de andra virtuella datorerna inte att tas bort och klustret kommer inte att förstöras. 

Ta sedan bort de klustrade metadatana från SQL-IaaS agent-tillägg: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server virtuella datorer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server virtuella datorer](frequently-asked-questions-faq.md)
* [Viktig information för SQL Server virtuella datorer](../../database/doc-changes-updates-release-notes.md)
* [Växla licensierings modeller för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Översikt över Always on Availability groups &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguration av en Server instans för Always on-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration av en tillgänglighets grupp &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Övervakning av tillgänglighets grupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Översikt över Transact-SQL-uttryck för Always on Availability groups &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Översikt över PowerShell-cmdletar för Always on-tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)