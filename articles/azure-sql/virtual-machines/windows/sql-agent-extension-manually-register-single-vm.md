---
title: Registrera med SQL IaaS agent-tillägg
description: Registrera din virtuella Azure SQL Server-dator med agent tillägget för SQL-IaaS för att aktivera funktioner för SQL Server virtuella datorer som distribueras utanför Azure Marketplace, samt efterlevnad och förbättrad hanterbarhet.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 393d0c69201f87ad7c96bd2f9a1f9f57df512e31
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964532"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrera SQL Server VM med SQL IaaS agent-tillägg
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

När du registrerar din SQL Server VM med [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md) kan du låsa upp en mängd funktions förmåner för dina SQL Server på Azure VM. 

I den här artikeln lär du dig att registrera en enda SQL Server VM med SQL IaaS agent-tillägget. Alternativt kan du registrera alla SQL Server virtuella datorer [automatiskt](sql-agent-extension-automatic-registration-all-vms.md) eller [flera virtuella datorer som har skript i bulk](sql-agent-extension-manually-register-vms-bulk.md).


## <a name="overview"></a>Översikt

När du registrerar med [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md) skapas den **virtuella SQL-datorns** _resurs_ i din prenumeration, som är en _separat_ resurs från den virtuella dator resursen. Om du avregistrerar SQL Server VM från tillägget tas den **virtuella SQL-datorns** _resurs_ bort, men den faktiska virtuella datorn tas inte bort.

Genom att distribuera en SQL Server VM Azure Marketplace-avbildning via Azure Portal registreras automatiskt SQL Server VM med tillägget. Men om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hård disk måste du registrera din SQL Server VM med SQL IaaS agent-tillägget för att kunna låsa upp fullständiga funktions förmåner och hanterbarhet. 

Om du vill använda SQL IaaS agent-tillägget måste du först [Registrera din prenumeration med **Microsoft. SqlVirtualMachine** -providern](#register-subscription-with-rp), som ger SQL IaaS-tillägget möjlighet att skapa resurser i den aktuella prenumerationen.

> [!IMPORTANT]
> SQL IaaS agent-tillägget samlar in data i Express syfte att ge kunderna valfria förmåner när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licens granskningar utan kundens medgivande. Se [SQL Server sekretess tillägg](/sql/sql-server/sql-server-privacy#non-personal-data) för mer information.

## <a name="prerequisites"></a>Krav

Om du vill registrera SQL Server VM med tillägget måste du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En virtuell dator med Azure resurs modell [Windows Server 2008 (eller senare)](../../../virtual-machines/windows/quick-create-portal.md) med [SQL Server 2008 (eller senare)](https://www.microsoft.com/sql-server/sql-server-downloads) distribueras till det offentliga molnet eller Azure Government molnet. 
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell (minst 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Registrera prenumeration med RP

Om du vill registrera SQL Server VM med agent tillägget för SQL-IaaS måste du först registrera din prenumeration med **Microsoft. SqlVirtualMachine** -providern. Detta ger SQL IaaS agent-tillägget möjlighet att skapa resurser i din prenumeration.  Du kan göra detta med hjälp av Azure Portal, Azure CLI eller Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Öppna Azure Portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj en prenumeration på intresse.  
1. På sidan **prenumerationer** går du till **tillägg**. 
1. Ange **SQL** i filtret för att ta fram de SQL-relaterade tilläggen. 
1. Välj **Registrera**, **Registrera** om eller **avregistrera** för  **Microsoft. SqlVirtualMachine** -providern, beroende på vilken åtgärd du önskar. 

   ![Ändra providern](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Kommandorad

Registrera din Azure-prenumeration med **Microsoft. SqlVirtualMachine** -providern med hjälp av antingen Azure CLI eller Azure PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registrera med tillägg

Det finns tre hanterings lägen för [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Att registrera tillägget i fullständigt hanterings läge startar om den SQL Server tjänsten, så vi rekommenderar att du först registrerar tillägget i Lightweight-läge och sedan [uppgraderar till fullständig](#upgrade-to-full) under underhålls perioden. 

### <a name="lightweight-management-mode"></a>Läge för förenklad hantering

Använd Azure CLI eller Azure PowerShell för att registrera SQL Server VM med tillägget i Lightweight-läge. Det här startar inte om SQL Server tjänsten. Du kan sedan uppgradera till fullständigt läge när som helst, men om du gör det startas tjänsten om SQL Server tjänsten så att du kan vänta tills en schemalagd underhålls period är aktiv. 

Ange SQL Server licens typ som antingen "betala `PAYG` per användning" () för att betala per användning, Azure Hybrid-förmån ( `AHUB` ) för att använda din egen licens eller haveri beredskap ( `DR` ) för att aktivera den [kostnads fria Dr Replica-licensen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Kluster instanser för växling vid fel och distributioner med flera instanser kan bara registreras med SQL IaaS agent-tillägget i Lightweight-läge. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera en SQL Server VM i lättviktigt läge med Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrera en SQL Server VM i Lightweight-läge med Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Fullständigt hanterings läge

Om du registrerar SQL Server VM i full läge startas om SQL Servers tjänsten. Fortsätt med försiktighet. 

Om du vill registrera din SQL Server VM direkt i fullständigt läge (och eventuellt starta om din SQL Server tjänst) använder du följande Azure PowerShell kommando: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Hanterings läge för noagent 

SQL Server 2008 och 2008 R2 som är installerade på Windows Server 2008 (_inte R2_) kan registreras med SQL IaaS agent-tillägget i [läget noagent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Det här alternativet säkerställer efterlevnad och tillåter att SQL Server VM övervakas i Azure Portal med begränsad funktionalitet.


För **licens typen** anger du antingen: `AHUB` , `PAYG` eller `DR` . För **avbildnings erbjudandet** anger du antingen `SQL2008-WS2008` eller `SQL2008R2-WS2008`

För att registrera SQL Server 2008 ( `SQL2008-WS2008` ) eller 2008 R2 ( `SQL2008R2-WS2008` ) på Windows Server 2008-instansen använder du följande Azure CLI-eller Azure PowerShell kodfragment: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera din SQL Server virtuella dator i noagent-läge med Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registrera SQL Server virtuella datorn i noagent-läge med Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Kontrol lera läge

Du kan visa det aktuella läget för SQL Server IaaS-agenten genom att använda Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Uppgradera till fullständig  

SQL Server virtuella datorer som har registrerat tillägget i *lättviktigt* läge kan uppgradera till _full_ användning med Azure Portal, Azure CLI eller Azure PowerShell. SQL Server virtuella datorer i _Noagent_ -läge kan uppgraderas till _full_ efter att operativ systemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera – om du vill göra det måste du [avregistrera](#unregister-from-extension) SQL Server VM från SQL IaaS agent-tillägget. Om du gör det tas den **virtuella SQL-datorns** _resurs_ bort, men den faktiska virtuella datorn tas inte bort. 

> [!NOTE]
> När du uppgraderar hanterings läget för SQL IaaS-tillägget till full startar det om SQL Server tjänsten. I vissa fall kan omstarten orsaka att tjänstens huvud namn (SPN) som är associerade med SQL Server-tjänsten ändras till fel användar konto. Om du har problem med anslutningen när du har uppgraderat hanterings läget till fullständig [avregistrerar du och omregistrerar dina SPN](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections).


### <a name="azure-portal"></a>Azure Portal

Följ dessa steg om du vill uppgradera tillägget till fullständigt läge med Azure Portal: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resursen för [virtuella SQL-datorer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Välj din SQL Server VM och välj **Översikt**. 
1. För SQL Server virtuella datorer med noagent-eller Lightweight IaaS-läge väljer du de **enda licens typ-och versions uppdateringar som är tillgängliga med meddelandet SQL IaaS-tillägg** .

   ![Val för att ändra läget från portalen](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Markera kryss rutan **Jag accepterar att starta om SQL Server tjänsten på den virtuella datorn** och välj sedan **Bekräfta** för att uppgradera IaaS-läget till full. 

    ![Kryss ruta för att komma överens om att starta om SQL Server tjänsten på den virtuella datorn](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Om du vill uppgradera tillägget till fullständigt läge kör du följande kod avsnitt för Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Om du vill uppgradera tillägget till fullständigt läge kör du följande Azure PowerShell kodfragment:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verifiera registrerings status
Du kan kontrol lera om din SQL Server VM redan har registrerats med tillägget för SQL IaaS-agenten med hjälp av Azure Portal, Azure CLI eller Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

Följ dessa steg om du vill kontrol lera registrerings statusen med hjälp av Azure Portal: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till dina [SQL Server virtuella datorer](manage-sql-vm-portal.md).
1. Välj din SQL Server VM i listan. Om din SQL Server VM inte visas här, har den förmodligen inte registrerats med SQL IaaS agent-tillägget. 
1. Visa värdet under **status**. Om **statusen** är **klar** har SQL Server VM registrerats med agent tillägget för SQL-IaaS korrekt. 

   ![Verifiera status med SQL RP-registrering](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Kommandorad

Verifiera aktuell SQL Server VM registrerings status med hjälp av antingen Azure CLI eller Azure PowerShell. `ProvisioningState` visar `Succeeded` om registreringen lyckades. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Kontrol lera registrerings statusen med hjälp av Azure CLI genom att köra följande kodfragment:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Om du vill kontrol lera registrerings statusen med Azure PowerShell kör du följande kodfragment:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ett fel indikerar att SQL Server VM inte har registrerats med tillägget. 


## <a name="unregister-from-extension"></a>Avregistrera från tillägg

Om du vill avregistrera SQL Server VM med agent tillägget för SQL IaaS tar du bort den virtuella SQL-datorns *resurs* med hjälp av Azure Portal eller Azure CLI. Om du tar bort den virtuella SQL-datorns *resurs* tas inte SQL Server VM bort. Använd dock försiktighet och följ stegen noggrant, eftersom det är möjligt att oavsiktligt ta bort den virtuella datorn när du försöker ta bort *resursen*. 

Att avregistrera den virtuella SQL-datorn med agent tillägget för SQL IaaS krävs för att nedgradera hanterings läget från full. 

### <a name="azure-portal"></a>Azure Portal

Följ dessa steg om du vill avregistrera SQL Server VM från tillägget med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till den virtuella SQL-resursen. 
  
   ![Resurs för virtuella SQL-datorer](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Välj **Ta bort**. 

   ![Välj Ta bort i det övre navigerings fältet](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Skriv namnet på den virtuella SQL-datorn och **avmarkera kryss rutan bredvid den virtuella datorn**.

   ![Avmarkera den virtuella datorn för att förhindra att den faktiska virtuella datorn tas bort och välj sedan ta bort för att fortsätta med att ta bort den virtuella SQL-resursen](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Om du inte avmarkerar kryss rutan bredvid namnet på den virtuella datorn tas den virtuella datorn *bort* helt. Avmarkera kryss rutan för att avregistrera SQL Server VM från tillägget, men *Ta inte bort den faktiska virtuella datorn*. 

1. Välj **ta bort** för att bekräfta borttagningen av den virtuella SQL-datorns *resurs*, inte SQL Server VM. 

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill avregistrera SQL Server VM från tillägget med Azure CLI använder du kommandot [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Detta tar bort den SQL Server VM *resursen* men tar inte bort den virtuella datorn. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill avregistrera SQL Server VM från tillägget med Azure PowerShell använder du kommandot [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Detta tar bort den SQL Server VM *resursen* men tar inte bort den virtuella datorn. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)  
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)
