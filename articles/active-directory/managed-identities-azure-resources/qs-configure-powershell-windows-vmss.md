---
title: Konfigurera hanterade identiteter på VM-skalningsuppsättningar med Hjälp av PowerShell – Azure AD
description: Stegvisa instruktioner för att konfigurera ett system och användar tilldelade hanterade identiteter på en VM-skalningsuppsättning med PowerShell.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb651df2fe3b9771154f9e188d41c660f7ffd028
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774155"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på VM-skalningsuppsättningar med Hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med PowerShell, lär du dig hur du utför hanterade identiteter för Azure-resursåtgärder på en VM-skalningsuppsättning:

- Aktivera och inaktivera den system tilldelade hanterade identiteten på en VM-skalningsuppsättning
- Lägga till och ta bort en användar tilldelad hanterad identitet på en VM-skalningsuppsättning

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar hanterad tilldelad identitet.](overview.md#managed-identity-types)**

- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

- För att kunna utföra hanteringsåtgärder i den här artikeln behöver ditt konto följande tilldelningar av rollbaserad åtkomstkontroll i Azure:

    > [!NOTE]
    > Inga ytterligare azure AD-katalogrolltilldelningar krävs.

    - [Virtuell datordeltagare för](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) att skapa en VM-skalningsuppsättning och aktivera och ta bort system tilldelad hanterad och/eller användar tilldelad hanterad identitet från en VM-skalningsuppsättning.
    - [Rollen Hanterad identitetsdeltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användar tilldelad hanterad identitet.
    - [Hanterad identitetsoperatörsroll](../../role-based-access-control/built-in-roles.md#managed-identity-operator) för att tilldela och ta bort en användar tilldelad hanterad identitet från och till en VM-skalningsuppsättning.

- Om du vill köra exempelskripten har du två alternativ:
    - Använd den [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med hjälp av **knappen Prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet lär du dig att aktivera och ta bort en system tilldelad hanterad identitet med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet när en VM-skalningsuppsättning i Azure skapas

Så här skapar du en VM-skalningsuppsättning med den system tilldelade hanterade identiteten aktiverad:

1. Se exempel *1* i referensartikeln för [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) för att skapa en VM-skalningsuppsättning med en system tilldelad hanterad identitet.  Lägg till `-IdentityType SystemAssigned` parametern i `New-AzVmssConfig` cmdleten:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet på en befintlig skalningsuppsättning för virtuella Azure-datorer

Om du behöver aktivera en system tilldelad hanterad identitet på en befintlig vm-skalningsuppsättning i Azure:

1. Kontrollera att det Azure-konto som du använder tillhör en roll som ger dig skrivbehörighet för VM-skalningsuppsättningen, till exempel "Virtuell datordeltagare".
   
1. Hämta egenskaperna för VM-skalningsuppsättningen med [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) hjälp av cmdleten . Om du sedan vill aktivera en system tilldelad hanterad identitet använder `-IdentityType` du växeln på cmdleten [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera den system tilldelade hanterade identiteten från en skalningsuppsättning för virtuella Azure-datorer

Om du har en VM-skalningsuppsättning som inte längre behöver den system tilldelade hanterade identiteten men fortfarande behöver användar tilldelade hanterade identiteter använder du följande cmdlet:

1. Kontrollera att ditt konto tillhör en roll som ger dig skrivbehörighet för VM-skalningsuppsättningen, till exempel "Virtuell datordeltagare".

1. Kör följande cmdlet:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Om du har en VM-skalningsuppsättning som inte längre behöver en system tilldelad hanterad identitet och den inte har några användar tilldelade hanterade identiteter använder du följande kommando:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användar tilldelad hanterad identitet från en VM-skalningsuppsättning med hjälp av Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet när en VM-skalningsuppsättning i Azure skapas

Det går för närvarande inte att skapa en ny VM-skalningsuppsättning med en användar tilldelad hanterad identitet via PowerShell. Se nästa avsnitt om hur du lägger till en användar tilldelad hanterad identitet till en befintlig VM-skalningsuppsättning. Kom tillbaka om för att få uppdateringar.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig skalningsuppsättning för virtuella Azure-datorer

Så här tilldelar du en användar tilldelad hanterad identitet till en befintlig vm-skalningsuppsättning i Azure:

1. Kontrollera att ditt konto tillhör en roll som ger dig skrivbehörighet för VM-skalningsuppsättningen, till exempel "Virtuell datordeltagare".

1. Hämta egenskaperna för VM-skalningsuppsättningen med `Get-AzVM` hjälp av cmdleten . Om du sedan vill tilldela en användar tilldelad hanterad identitet till VM-skalningsuppsättningen använder du växeln och på `-IdentityType` `-IdentityID` cmdleten [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) Ersätt `<VM NAME>` , , , med dina egna `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` `USER ASSIGNED ID2` värden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användar tilldelad hanterad identitet från en skalningsuppsättning för virtuella Azure-datorer

Om din VM-skalningsuppsättning har flera användar tilldelade hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. är `<USER ASSIGNED IDENTITY NAME>` namnegenskapen för den användar tilldelade hanterade identiteten, som ska finnas kvar i VM-skalningsuppsättningen. Den här informationen finns i identitetsavsnittet i VM-skalningsuppsättningen med hjälp av `az vmss show` :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Om VM-skalningsuppsättningen inte har en system tilldelad hanterad identitet och du vill ta bort alla användar tilldelade hanterade identiteter från den använder du följande kommando:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Om din VM-skalningsuppsättning har både system- och användar tilldelade hanterade identiteter kan du ta bort alla användar tilldelade hanterade identiteter genom att byta till att endast använda system-tilldelade hanterade identiteter.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- De fullständiga snabbstarterna för att skapa virtuella Azure-datorer finns i:
  
  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
