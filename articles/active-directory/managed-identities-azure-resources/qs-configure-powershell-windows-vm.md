---
title: Konfigurera hanterade identiteter på en virtuell Azure-dator med PowerShell – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e56a5d8607ac2472ba4ef4bdb090468691c93de6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505018"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod.

I den här artikeln använder du PowerShell för att lära dig hur du utför följande hanterade identiteter för Azure-resurser på en virtuell Azure-dator.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan gjort det.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad hanterad identitet med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell Azure-dator skapas

För att skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Se något av följande snabb starter för Azure VM, som endast fyller i de nödvändiga avsnitten ("logga in på Azure", "skapa resurs grupp", "skapa nätverks grupp", "skapa den virtuella datorn").

    När du kommer till avsnittet "skapa den virtuella datorn" gör du en mindre ändring av cmdlet [-syntaxen New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Se till att lägga till en `-AssignIdentity:$SystemAssigned` parameter för att etablera den virtuella datorn med den systemtilldelade identiteten aktive rad, till exempel:

    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator som ursprungligen etablerades utan den, måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Logga in på Azure med `Connect-AzAccount` . Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta först egenskaperna för den virtuella datorn med `Get-AzVM` cmdleten. Om du vill aktivera en systemtilldelad hanterad identitet använder du `-AssignIdentity` växeln på cmdleten [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Lägg till den virtuella datorns tilldelade identitet i en grupp

När du har aktiverat systemtilldelad identitet på en virtuell dator kan du lägga till den i en grupp.  Följande procedur lägger till en virtuell dators tilldelade identitet i en grupp.

1. Logga in på Azure med `Connect-AzAccount` . Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta och anteckna `ObjectID` (enligt vad som anges i `Id` fältet för de returnerade värdena) för den virtuella datorns tjänst huvud namn:

   ```powershell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Hämta och anteckna `ObjectID` (enligt vad som anges i `Id` fältet i de returnerade värdena) för gruppen:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Lägg till den virtuella datorns tjänst huvud namn i gruppen:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad hanterad identitet från en virtuell Azure-dator

Om du vill inaktivera systemtilldelad hanterad identitet på en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

Använd följande cmdlet om du har en virtuell dator som inte längre behöver den systemtilldelade hanterade identiteten men fortfarande behöver användare tilldelade hanterade identiteter:

1. Logga in på Azure med `Connect-AzAccount` . Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta de virtuella dator egenskaperna med `Get-AzVM` cmdleten och ange `-IdentityType` parametern till `UserAssigned` :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Om du har en virtuell dator som inte längre behöver systemtilldelad hanterad identitet och den inte har några användare tilldelade hanterade identiteter, använder du följande kommandon:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användardefinierad hanterad identitet från en virtuell dator med hjälp av Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Tilldela en användardefinierad hanterad identitet till en virtuell dator när den skapas

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Se något av följande snabb starter för Azure VM, som endast fyller i de nödvändiga avsnitten ("logga in på Azure", "skapa resurs grupp", "skapa nätverks grupp", "skapa den virtuella datorn").

    När du kommer till avsnittet "skapa den virtuella datorn" gör du en liten ändring av [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet-syntaxen. Lägg till `-IdentityType UserAssigned` `-IdentityID` parametrarna och för att etablera den virtuella datorn med en tilldelad identitet.  Ersätt `<VM NAME>` , `<SUBSCRIPTION ID>` , `<RESROURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden.  Ett exempel:

    ```powershell
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användardefinierad hanterad identitet till en befintlig virtuell Azure-dator

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Logga in på Azure med `Connect-AzAccount` . Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Skapa en användardefinierad hanterad identitet med hjälp av cmdleten [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Observera `Id` i resultatet eftersom du kommer att behöva detta i nästa steg.

   > [!IMPORTANT]
   > Att skapa användarspecifika hanterade identiteter stöder endast alfanumeriska tecken, under streck och bindestreck (0-9 eller a-z eller A-Z eller \_ -). Dessutom bör namnet begränsas från 3 till 128 tecken längd för tilldelningen till VM/VMSS för att fungera korrekt. Mer information finns i [vanliga frågor och svar](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Hämta egenskaperna för den virtuella datorn med `Get-AzVM` cmdleten. Om du sedan vill tilldela en användardefinierad hanterad identitet till den virtuella Azure-datorn använder du `-IdentityType` och `-IdentityID` -växeln på [Update-AzVM-](/powershell/module/az.compute/update-azvm) cmdlet: en.  Värdet för `-IdentityId` parametern är det `Id` du antecknade i föregående steg.  Ersätt `<VM NAME>` , `<SUBSCRIPTION ID>` , `<RESROURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden.

   > [!WARNING]
   > Om du vill behålla tidigare tilldelade hanterade identiteter som har tilldelats den virtuella datorn frågar du `Identity` egenskapen för VM-objektet (till exempel `$vm.Identity` ).  Om en användare som tilldelats hanterade identiteter returneras inkluderar du dem i följande kommando tillsammans med den nya användare som tilldelats den hanterade identitet som du vill tilldela till den virtuella datorn.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats en hanterad identitet från en virtuell Azure-dator

För att ta bort en tilldelad identitet till en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

Om den virtuella datorn har flera användarspecifika hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY NAME>`Är egenskapen namn för den användare som tilldelats den hanterade identitet som ska finnas kvar på den virtuella datorn. Den här informationen hittar du genom att skicka en fråga till `Identity` egenskapen för VM-objektet.  Till exempel `$vm.Identity` :

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Om den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla tilldelade hanterade identiteter från den, använder du följande kommando:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Om den virtuella datorn har både systemtilldelade och användarspecifika hanterade identiteter kan du ta bort alla användare tilldelade hanterade identiteter genom att växla till Använd endast systemtilldelade hanterade identiteter.

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- De fullständiga snabb starterna för att skapa virtuella Azure-datorer finns i:

  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
