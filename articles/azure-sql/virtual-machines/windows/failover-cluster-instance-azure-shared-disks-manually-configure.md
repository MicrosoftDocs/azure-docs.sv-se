---
title: Skapa en FCI med Azure delade diskar
description: Använd Azure delade diskar för att skapa en FCI-instans (failover Cluster instance) med SQL Server på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 244fae9f8611acd21f2ee6cd7dafa45b88606456
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359361"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Skapa en FCI med Azure Shared disks (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln förklaras hur du skapar en instans av en redundanskluster (FCI) med hjälp av Azure delade diskar med SQL Server på Azure Virtual Machines (VM). 

Mer information finns i Översikt över [FCI med SQL Server på Azure VM](failover-cluster-instance-overview.md) och [kluster metod tips](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Förutsättningar 

Innan du slutför instruktionerna i den här artikeln bör du redan ha:

- En Azure-prenumeration. Kom igång [kostnads fritt](https://azure.microsoft.com/free/). 
- [Två eller flera virtuella Windows Azure-datorer](failover-cluster-instance-prepare-vm.md). [Tillgänglighets uppsättningar](../../../virtual-machines/windows/tutorial-availability-sets.md) och [närhets placerings grupper](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (PPGs) som stöds för Premium SSD-och [tillgänglighets zoner](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) stöds för Ultra disks. Om du använder en PPG måste alla noder finnas i samma grupp.
- Ett konto som har behörighet att skapa objekt både på virtuella Azure-datorer och i Active Directory.
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-azure-shared-disk"></a>Lägg till Azure-delad disk
Distribuera en hanterad Premium SSD disk med funktionen för delad disk aktive rad. Ställ in `maxShares` så att den **överensstämmer med antalet klusternoder** för att göra disken SHAREABLE över alla FCI-noder. 

Lägg till en Azure-delad disk genom att göra följande: 


1. Spara följande skript som *SharedDiskConfig.jspå*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Kör *SharedDiskConfig.jspå* med hjälp av PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Initiera de bifogade delade diskarna som GUID Partition Table (GPT) för varje virtuell dator och formatera dem som nya Technology File System (NTFS) genom att köra det här kommandot: 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>Skapa redundanskluster

Du behöver följande om du vill skapa ett kluster för växling vid fel:

- Namnen på de virtuella datorer som kommer att bli klusternoder.
- Ett namn för redundansklustret.
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Följande PowerShell-skript skapar ett redundanskluster. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Följande PowerShell-skript skapar ett redundanskluster. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Mer information finns i [redundansklustret: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurera kvorum

Konfigurera den kvorumresurs som passar dina affärs behov bäst. Du kan konfigurera ett [disk vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), ett [moln vittne](/windows-server/failover-clustering/deploy-cloud-witness)eller ett [fil resurs vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Mer information finns i [kvorum med SQL Server virtuella datorer](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Verifiera kluster
Verifiera klustret i användar gränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användar gränssnittet genom att göra följande på en av de virtuella datorerna:

1. Under **Serverhanteraren** väljer du **verktyg** och väljer sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel** väljer du **åtgärd** och väljer sedan **Verifiera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster** anger du namnen på de båda virtuella datorerna.
1. Under **test alternativ** väljer **du kör endast test som jag väljer**. 
1. Välj **Nästa**.
1. Under **Val av test** väljer du alla tester *utom* **lagring**

## <a name="test-cluster-failover"></a>Testa redundanskluster

Testa redundansväxlingen av klustret. I **Klusterhanteraren för växling vid fel** högerklickar du på klustret, väljer **fler åtgärder**  >  **Flytta kärn kluster resurs**  >  **Välj nod** och välj sedan den andra noden i klustret. Flytta kärn kluster resursen till varje nod i klustret och flytta tillbaka den till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testa redundanskluster genom att flytta kärn resursen till de andra noderna":::

## <a name="create-sql-server-fci"></a>Skapa SQL Server FCI

När du har konfigurerat redundansklustret och alla kluster komponenter, inklusive lagring, kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av Remote Desktop Protocol (RDP).

1. Kontrol lera i **Klusterhanteraren för växling vid fel** att alla kärn kluster resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full` . 

1. Välj **installation**.

1. I **SQL Server installations Center** väljer du **installation**.

1. Välj **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

FCI data kataloger måste finnas på de Azure-delade diskarna. 

1. När du har slutfört anvisningarna i guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **installations Center för SQL Server** och välj sedan **installation**.

1. Välj **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning som innehåller SQL Server, inkluderades SQL Server verktyg med avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server verktyg separat. Mer information finns i [Ladda ned SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >

## <a name="register-with-the-sql-vm-rp"></a>Registrera dig för SQL VM RP

Om du vill hantera din SQL Server VM från portalen registrerar du den med SQL IaaS agent Extension (RP) i [läget för förenklad hantering](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), för närvarande det enda läge som stöds med FCI och SQL Server på virtuella Azure-datorer. 


Registrera en SQL Server VM i Lightweight-läge med PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurera anslutning 

Om du vill dirigera trafiken korrekt till den aktuella primära noden konfigurerar du anslutnings alternativet som är lämpligt för din miljö. Du kan skapa en [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) eller, om du använder SQL Server 2019 CU2 (eller senare) och Windows Server 2016 (eller senare), kan du använda funktionen [distribuerat nätverks namn](failover-cluster-instance-distributed-network-name-dnn-configure.md) i stället. 

## <a name="limitations"></a>Begränsningar

- Det finns bara stöd för registrering med SQL IaaS agent extension i [läget för förenklad hantering](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det konfigurerar du anslutningen till din FCI med ett [virtuellt nätverks namn och en Azure Load Balancer eller ett](failover-cluster-instance-vnn-azure-load-balancer-configure.md) [distribuerat nätverks namn (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Om Azure-delade diskar inte är lämplig FCI lagrings lösning, kan du överväga att skapa din FCI med [Premium-filresurser](failover-cluster-instance-premium-file-share-manually-configure.md) eller [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md) i stället. 

Mer information finns i en översikt över [FCI med SQL Server på](failover-cluster-instance-overview.md) bästa praxis för Azure VM och [kluster konfiguration](hadr-cluster-best-practices.md).

Mer information finns i: 
- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)