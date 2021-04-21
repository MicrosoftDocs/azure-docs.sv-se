---
title: Registrera med SQL IaaS Agent-tillägget
description: Registrera din virtuella Azure SQL Server-dator med SQL IaaS Agent-tillägget för att aktivera funktioner för SQL Server virtuella datorer som distribueras utanför Azure Marketplace, samt efterlevnad och förbättrad hanterbarhet.
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
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765457"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrera SQL Server VM med SQL IaaS Agent-tillägget
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Genom att registrera SQL Server VM med [SQL IaaS Agent-tillägget](sql-server-iaas-agent-extension-automate-management.md) får du tillgång till en mängd olika funktionsfördelar för dina SQL Server på virtuella Azure-datorer. 

I den här artikeln lär du dig att registrera en SQL Server VM med SQL IaaS Agent-tillägget. Du kan också registrera alla virtuella SQL Server automatiskt [eller](sql-agent-extension-automatic-registration-all-vms.md) flera [virtuella datorer som har skriptats i grupp.](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="overview"></a>Översikt

När du registrerar [med SQL Server IaaS Agent-tillägget](sql-server-iaas-agent-extension-automate-management.md) skapas den virtuella **SQL-datorresursen**  i din prenumeration, som är en separat resurs från den virtuella datorresursen.  Om du avregistrerar SQL Server VM från tillägget tas den virtuella **SQL-datorresursen** _bort,_ men den faktiska virtuella datorn tas inte bort.

När du distribuerar SQL Server VM Azure Marketplace avbildningen via Azure Portal automatiskt SQL Server VM med tillägget . Men om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hårddisk måste du registrera din SQL Server VM med SQL IaaS Agent-tillägget för att få tillgång till alla fördelar med funktioner och hanterbarhet. 

Om du vill använda SQL IaaS Agent-tillägget måste du först registrera din prenumeration med [ **Providern Microsoft.SqlVirtualMachine,**](#register-subscription-with-resource-provider)som ger SQL IaaS-tillägget möjlighet att skapa resurser inom den specifika prenumerationen.

> [!IMPORTANT]
> SQL IaaS Agent-tillägget samlar in data för att ge kunderna valfria fördelar när de använder SQL Server i Azure Virtual Machines. Microsoft kommer inte att använda dessa data för licensieringsgranskningar utan kundens medgivande i förväg. Mer [information SQL Server finns](/sql/sql-server/sql-server-privacy#non-personal-data) i SQL Server tillägg för sekretess.

## <a name="prerequisites"></a>Förutsättningar

Om du SQL Server VM med tillägget behöver du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En virtuell Dator med [Azure Resource Model Windows Server 2008 (eller högre)](../../../virtual-machines/windows/quick-create-portal.md) med SQL Server [2008 (eller högre)](https://www.microsoft.com/sql-server/sql-server-downloads) distribuerad till det offentliga eller Azure Government molnet. 
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell (minst 5.0).](/powershell/azure/install-az-ps) 


## <a name="register-subscription-with-resource-provider"></a>Registrera prenumeration med resursprovider

Om du vill SQL Server VM med SQL IaaS Agent-tillägget måste du först registrera din prenumeration med **resursprovidern Microsoft.SqlVirtualMachine.** Detta ger SQL IaaS Agent-tillägget möjlighet att skapa resurser i din prenumeration.  Du kan göra det med hjälp Azure Portal, Azure CLI eller Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Öppna Azure Portal och gå till **Alla tjänster.** 
1. Gå till **Prenumerationer** och välj den intressanta prenumerationen.  
1. På sidan **Prenumerationer** går du till **tillägg .** 
1. Ange **sql** i filtret för att visa de SQL-relaterade tilläggen. 
1. Välj **Registrera,** **Registrera om eller** **Avregistrera för**  **Providern Microsoft.SqlVirtualMachine,** beroende på önskad åtgärd. 

   ![Ändra providern](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Kommandorad

Registrera din Azure-prenumeration med **Microsoft.SqlVirtualMachine-providern** med antingen Azure CLI eller Azure PowerShell. 

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

Det finns tre hanteringslägen för [SQL Server IaaS-agenttillägget](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

När du registrerar tillägget i fullständigt hanteringsläge startas SQL Server-tjänsten om så vi rekommenderar att [](#upgrade-to-full) du registrerar tillägget i förenklat läge först och sedan uppgraderar till full under en underhållsfönstret. 

### <a name="lightweight-management-mode"></a>Förenklat hanteringsläge

Använd Azure CLI eller Azure PowerShell för att registrera SQL Server VM med tillägget i förenklat läge. Detta startar inte om SQL Server tjänsten. Du kan sedan uppgradera till fullständigt läge när som helst, men om du gör det startas SQL Server-tjänsten om så vi rekommenderar att du väntar tills ett schemalagt underhåll har startats. 

Ange SQL Server-licenstypen som antingen betala per användning ( ) att betala per användning, Azure Hybrid-förmån ( ) för att använda din egen licens eller haveriberedskap ( ) för att aktivera den kostnadsfria `PAYG` `AHUB` `DR` [DR-repliklicensen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Redundansklusterinstanser och distributioner med flera instanser kan bara registreras med SQL IaaS Agent-tillägget i förenklat läge. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera en SQL Server VM i förenklat läge med Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrera en SQL Server VM i förenklat läge med Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Fullständigt hanteringsläge

När du registrerar SQL Server VM i fullständigt läge startas SQL Server tjänsten om. Tänk efter innan du går vidare. 

Om du vill SQL Server VM direkt i fullständigt läge (och eventuellt starta om SQL Server-tjänsten) använder du följande Azure PowerShell kommando: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent-hanteringsläge 

SQL Server 2008 och 2008 R2 som installerats på Windows Server 2008 (inte _R2)_ kan registreras med SQL IaaS Agent-tillägget i [NoAgent-läget](sql-server-iaas-agent-extension-automate-management.md#management-modes). Det här alternativet säkerställer efterlevnad och SQL Server VM kan övervakas i Azure Portal med begränsad funktionalitet.


För **licenstypen anger** du antingen: `AHUB` , eller `PAYG` `DR` . För **avbildningserbjudandet** anger du `SQL2008-WS2008` antingen eller `SQL2008R2-WS2008`

Om du vill registrera SQL Server 2008 ( ) eller `SQL2008-WS2008` 2008 R2 ( ) på `SQL2008R2-WS2008` Windows Server 2008-instansen använder du följande Azure CLI eller Azure PowerShell kodfragment: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera din SQL Server virtuella dator i NoAgent-läge med Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registrera den SQL Server virtuella datorn i NoAgent-läge med Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Verifiera läge

Du kan visa det aktuella läget för din SQL Server IaaS-agent med hjälp av Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Uppgradera till full  

SQL Server virtuella datorer som har registrerat  tillägget i förenklat läge kan uppgraderas till full med Azure Portal, Azure CLI eller Azure PowerShell.  SQL Server virtuella datorer i _NoAgent-läge_ kan uppgraderas till _full_ när operativsystemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera – om [](#unregister-from-extension) du vill göra det måste du avregistrera SQL Server VM från SQL IaaS Agent-tillägget. Om du gör det tas **den virtuella SQL-datorresursen** bort, men den faktiska virtuella datorn tas inte bort. 

> [!NOTE]
> När du uppgraderar hanteringsläget för SQL IaaS-tillägget till fullt startar det om SQL Server tjänsten. I vissa fall kan omstarten orsaka att tjänstens huvudnamn (SPN) som är associerade med SQL Server-tjänsten ändras till fel användarkonto. Om du har anslutningsproblem när du har uppgraderat hanteringsläget till fullständigt [avregistrerar du och omregistrerar dina SPN:er.](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)


### <a name="azure-portal"></a>Azure Portal

Följ dessa steg om du vill uppgradera tillägget till Azure Portal läge med hjälp av Azure Portal: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resursen [för dina virtuella SQL-datorer.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Välj SQL Server VM och välj **Översikt.** 
1. För SQL Server virtuella datorer med NoAgent eller enkelt IaaS-läge väljer du meddelandet Endast licenstyp och uppdateringar av utgåva är tillgängliga med **SQL IaaS-tillägget.**

   ![Val för att ändra läget från portalen](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Markera kryssrutan **Jag godkänner att starta SQL Server** på den virtuella  datorn och välj sedan Bekräfta för att uppgradera IaaS-läget till fullt. 

    ![Kryssruta för att godkänna att starta SQL Server på den virtuella datorn](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Om du vill uppgradera tillägget till fullständigt läge kör du följande Azure CLI-kodfragment:

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

## <a name="verify-registration-status"></a>Verifiera registreringsstatus
Du kan kontrollera om SQL Server VM redan har registrerats med SQL IaaS Agent-tillägget med hjälp av Azure Portal, Azure CLI eller Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

Följ dessa steg om du vill Azure Portal registreringsstatus med hjälp av följande steg: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till din [SQL Server virtuella datorer](manage-sql-vm-portal.md).
1. Välj SQL Server VM i listan. Om ditt SQL Server VM inte visas här har det förmodligen inte registrerats med SQL IaaS Agent-tillägget. 
1. Visa värdet under **Status**. Om **Status** **är Lyckades** har SQL Server VM med SQL IaaS Agent-tillägget. 

   ![Verifiera status med SQL RP-registrering](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Kommandorad

Kontrollera aktuell SQL Server VM registreringsstatus med hjälp av antingen Azure CLI eller Azure PowerShell. `ProvisioningState` visar `Succeeded` om registreringen lyckades. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Kontrollera registreringsstatusen med hjälp av Azure CLI genom att köra följande kodfragment:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kontrollera registreringsstatusen med hjälp Azure PowerShell kodfragmentet:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ett fel indikerar att SQL Server VM har registrerats med tillägget. 


## <a name="unregister-from-extension"></a>Avregistrera från tillägg

Om du vill avregistrera SQL Server VM med SQL IaaS Agent-tillägget  tar du bort den virtuella SQL-datorresursen med hjälp av Azure Portal eller Azure CLI. Om du tar bort den *virtuella SQL-datorresursen* tas inte SQL Server VM. Var dock försiktig och följ stegen noggrant eftersom det är möjligt att oavsiktligt ta bort den virtuella datorn när du försöker ta bort *resursen*. 

Du måste avregistrera den virtuella SQL-datorn med SQL IaaS Agent-tillägget för att nedgradera hanteringsläget från fullständig. 

### <a name="azure-portal"></a>Azure Portal

Om du vill avregistrera SQL Server VM från tillägget med hjälp Azure Portal gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resursen för den virtuella SQL-datorn. 
  
   ![Resurs för virtuella SQL-datorer](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Välj **Ta bort**. 

   ![Välj Ta bort i det övre navigeringsfönstret](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Skriv namnet på den virtuella SQL-datorn **och avmarkera kryssrutan bredvid den virtuella datorn**.

   ![Avmarkera den virtuella datorn för att förhindra att den faktiska virtuella datorn tas bort och välj sedan Ta bort för att fortsätta med att ta bort SQL VM-resursen](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Om du inte avmarkerar kryssrutan bredvid namnet på den virtuella datorn tas *den* virtuella datorn bort helt. Avmarkera kryssrutan för att avregistrera SQL Server VM från tillägget men inte *ta bort den faktiska virtuella datorn*. 

1. Välj **Ta** bort för att bekräfta borttagningen av den virtuella *SQL-datorresursen* och inte SQL Server VM. 

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill avregistrera SQL Server VM från tillägget med Azure CLI använder du kommandot [az sql vm delete.](/cli/azure/sql/vm#az_sql_vm_delete) Då tas den virtuella SQL Server VM *bort,* men den virtuella datorn tas inte bort. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill avregistrera SQL Server VM från tillägget med Azure PowerShell använder du kommandot [Remove-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) Då tas den virtuella SQL Server VM *bort,* men den virtuella datorn tas inte bort. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)  
* [Prisvägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Information om hur du SQL Server en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)
