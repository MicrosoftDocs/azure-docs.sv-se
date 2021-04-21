---
title: Konfigurera hanterade identiteter på en virtuell Azure-dator med Hjälp av PowerShell – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783277"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden.

I den här artikeln använder du PowerShell och lär dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en virtuell Azure-dator.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar tilldelad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempelskripten har du två alternativ:
    - Använd den [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med hjälp av **knappen Prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet lär du dig att aktivera och inaktivera den system tilldelade hanterade identiteten med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera system tilldelad hanterad identitet när en virtuell Azure-dator skapas

Om du vill skapa en virtuell Azure-dator med den system tilldelade hanterade identiteten aktiverad måste ditt konto ha [rolltilldelningen Virtuell datordeltagare.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Se någon av följande snabbstarter för virtuella Azure-datorer och slutför endast de nödvändiga avsnitten ("Logga in på Azure", "Skapa resursgrupp", "Skapa nätverksgrupp", "Skapa den virtuella datorn").

    När du kommer till avsnittet "Skapa den virtuella datorn" gör du en liten ändring i cmdlet-syntaxen [för New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Se till att lägga till en `-IdentityType SystemAssigned` parameter för att etablera den virtuella datorn med den system tilldelade identiteten aktiverad, till exempel:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera system tilldelad hanterad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera system tilldelad hanterad identitet på en virtuell dator som ursprungligen etablerades utan den måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Hämta egenskaperna för den virtuella datorn med `Get-AzVM` hjälp av cmdleten . Om du sedan vill aktivera en system tilldelad hanterad identitet använder `-IdentityType` du växeln på cmdleten [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Lägga till en system tilldelad identitet för virtuell dator i en grupp

När du har aktiverat system tilldelad identitet på en virtuell dator kan du lägga till den i en grupp.  Följande procedur lägger till en virtuell dators system tilldelade identitet till en grupp.

1. Hämta och anteckna `ObjectID` (som anges i fältet `Id` för de returnerade värdena) för den virtuella datorns huvudnamn för tjänsten:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Hämta och notera `ObjectID` (som anges i `Id` fältet för de returnerade värdena) för gruppen:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Lägg till den virtuella datorns huvudnamn för tjänsten i gruppen:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera system tilldelad hanterad identitet från en virtuell Azure-dator

Om du vill inaktivera system tilldelad hanterad identitet på en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver den system tilldelade hanterade identiteten men fortfarande behöver användar tilldelade hanterade identiteter använder du följande cmdlet:

1. Hämta egenskaperna för den virtuella datorn `Get-AzVM` med hjälp av cmdleten och `-IdentityType` ange parametern till `UserAssigned` :

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Om du har en virtuell dator som inte längre behöver en system tilldelad hanterad identitet och den inte har några användar tilldelade hanterade identiteter använder du följande kommandon:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användar tilldelad hanterad identitet från en virtuell dator med Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Tilldela en användar tilldelad hanterad identitet till en virtuell dator när den skapas

Om du vill tilldela en användar tilldelad identitet till en virtuell dator måste ditt konto ha rolltilldelningarna Virtuell [datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [och Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Se någon av följande snabbstarter för virtuella Azure-datorer och slutför endast de nödvändiga avsnitten ("Logga in på Azure", "Skapa resursgrupp", "Skapa nätverksgrupp", "Skapa den virtuella datorn").

    När du kommer till avsnittet "Skapa den virtuella datorn" gör du en liten ändring i [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet-syntaxen. Lägg till `-IdentityType UserAssigned` `-IdentityID` parametrarna och för att etablera den virtuella datorn med en användar tilldelad identitet.  Ersätt `<VM NAME>` , , och med dina egna `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` värden.  Exempel:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig virtuell Azure-dator

Om du vill tilldela en användar tilldelad identitet till en virtuell dator måste ditt konto ha rolltilldelningarna Virtuell [datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [och Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Skapa en användartilldelade hanterade identitet med cmdleten [New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Observera i `Id` utdata eftersom du behöver detta i nästa steg.

   > [!IMPORTANT]
   > Att skapa användar tilldelade hanterade identiteter stöder endast alfanumeriska tecken, understreck och bindestreck (0–9 eller a–z eller A–Z eller \_ -). Dessutom bör namnet begränsas från 3 till 128 tecken för att tilldelningen till VM/VMSS ska fungera korrekt. Mer information finns i [Vanliga frågor och svar och kända problem](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Hämta egenskaperna för den virtuella datorn med `Get-AzVM` hjälp av cmdleten . Om du sedan vill tilldela en användar tilldelad hanterad identitet till den virtuella Azure-datorn använder du och på `-IdentityType` `-IdentityID` cmdleten [Update-AzVM.](/powershell/module/az.compute/update-azvm)  Värdet för `-IdentityId` parametern är det `Id` som du antecknade i föregående steg.  Ersätt `<VM NAME>` , , och med dina egna `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` värden.

   > [!WARNING]
   > Om du vill behålla alla tidigare användar tilldelade hanterade identiteter som tilldelats den virtuella datorn frågar du egenskapen för `Identity` VM-objektet (till exempel `$vm.Identity` ).  Om användar tilldelade hanterade identiteter returneras inkluderar du dem i följande kommando tillsammans med den nya användar tilldelade hanterade identiteten som du vill tilldela till den virtuella datorn.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användar tilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användar tilldelad identitet till en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.

Om den virtuella datorn har flera användar tilldelade hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY NAME>`är den användar tilldelade hanterade identitetens namnegenskap, som ska finnas kvar på den virtuella datorn. Du hittar den här informationen genom att fråga egenskapen `Identity` för VM-objektet.  Till exempel `$vm.Identity` :

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Om den virtuella datorn inte har en system tilldelad hanterad identitet och du vill ta bort alla användar tilldelade hanterade identiteter från den använder du följande kommando:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Om den virtuella datorn har både system-tilldelade och användar tilldelade hanterade identiteter kan du ta bort alla användar tilldelade hanterade identiteter genom att byta till att endast använda system-tilldelade hanterade identiteter.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Fullständiga snabbstarter för att skapa virtuella Azure-datorer finns i:

  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
