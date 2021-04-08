---
title: Konfigurera hanterade identiteter på virtuella datorers skalnings uppsättningar med PowerShell – Azure AD
description: Stegvisa instruktioner för hur du konfigurerar ett system och användare som tilldelats hanterade identiteter på en virtuell dators skalnings uppsättning med hjälp av PowerShell.
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
ms.openlocfilehash: bd7ec1f6cdfc8ecfe32de04f5d06b42f9492b88c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590910"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser i skalnings uppsättningar för virtuella datorer med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln använder du PowerShell för att lära dig hur du utför de hanterade identiteterna för Azure-resurser på en skalnings uppsättning för virtuella datorer:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en skal uppsättning för virtuell dator
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell dators skalnings uppsättning

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en systemtilldelad och användare hanterad identitet](overview.md#managed-identity-types)**.

- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

- För att utföra hanterings åtgärderna i den här artikeln måste ditt konto ha följande Azure-rollbaserade åtkomst kontroll tilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD Directory-roll tilldelningar krävs.

    - [Virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för att skapa en skalnings uppsättning för virtuella datorer och aktivera och ta bort systemtilldelad hanterad och/eller användardefinierad hanterad identitet från en virtuell dators skalnings uppsättning.
    - Rollen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användardefinierad hanterad identitet.
    - [Hanterad identitet operatörs](../../role-based-access-control/built-in-roles.md#managed-identity-operator) roll för att tilldela och ta bort en användardefinierad hanterad identitet från och till en skalnings uppsättning för virtuella datorer.

- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)och logga sedan in på Azure med hjälp av `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och tar bort en systemtilldelad hanterad identitet med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet under skapandet av en skalnings uppsättning för virtuella Azure-datorer

Så här skapar du en skalnings uppsättning för virtuella datorer med den systemtilldelade hanterade identiteten aktive rad:

1. Se *exempel 1* i referens artikeln [New-AzVmssConfig-](/powershell/module/az.compute/new-azvmssconfig) cmdlet för att skapa en skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.  Lägg till parametern `-IdentityType SystemAssigned` i `New-AzVmssConfig` cmdleten:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet på en befintlig skalnings uppsättning för virtuella Azure-datorer

Om du behöver aktivera en systemtilldelad hanterad identitet på en befintlig skalnings uppsättning för virtuella Azure-datorer:

1. Kontrol lera att Azure-kontot som du använder tillhör en roll som ger dig Skriv behörighet för den virtuella datorns skalnings uppsättning, till exempel "virtuell dator deltagare".
   
1. Hämta egenskaperna för skalnings uppsättningen för den virtuella datorn med [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) cmdleten. Om du vill aktivera en systemtilldelad hanterad identitet använder du `-IdentityType` växeln på cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) :

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera den systemtilldelade hanterade identiteten från en skalnings uppsättning för virtuella Azure-datorer

Om du har en skalnings uppsättning för virtuella datorer som inte längre behöver den systemtilldelade hanterade identiteten men fortfarande behöver användare tilldelade hanterade identiteter, använder du följande cmdlet:

1. Kontrol lera att ditt konto tillhör en roll som ger dig Skriv behörighet för den virtuella datorns skalnings uppsättning, till exempel "virtuell dator deltagare".

1. Kör följande cmdlet:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Använd följande kommando om du har en skalnings uppsättning för virtuella datorer som inte längre behöver systemtilldelad hanterad identitet och som inte har några användare tilldelade hanterade identiteter:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer med hjälp av Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet när en skalnings uppsättning för virtuella Azure-datorer skapas

Det finns för närvarande inte stöd för att skapa en ny skalnings uppsättning för virtuella datorer med en användardefinierad hanterad identitet via PowerShell. Se nästa avsnitt om hur du lägger till en användardefinierad hanterad identitet i en befintlig skalnings uppsättning för virtuella datorer. Kom tillbaka om för att få uppdateringar.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet till en befintlig skalnings uppsättning för virtuella Azure-datorer

Så här tilldelar du en användardefinierad hanterad identitet till en befintlig skalnings uppsättning för virtuella Azure-datorer:

1. Kontrol lera att ditt konto tillhör en roll som ger dig Skriv behörighet för den virtuella datorns skalnings uppsättning, till exempel "virtuell dator deltagare".

1. Hämta egenskaperna för skalnings uppsättningen för den virtuella datorn med `Get-AzVM` cmdleten. Tilldela sedan en användardefinierad hanterad identitet till den virtuella datorns skal uppsättning genom att använda `-IdentityType` och- `-IdentityID` växeln i cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Ersätt `<VM NAME>` ,,, `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` , `USER ASSIGNED ID2` med dina egna värden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella Azure-datorer

Om den virtuella datorns skalnings uppsättning har flera användarspecifika hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY NAME>`Är egenskapen namn för den användare som tilldelats den hanterade identitet som ska finnas kvar på den virtuella datorns skal uppsättning. Den här informationen finns i avsnittet identitet i skalnings uppsättningen för den virtuella datorn med hjälp av `az vmss show` :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Om din skalnings uppsättning för den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla tilldelade hanterade identiteter från den, använder du följande kommando:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Om den virtuella datorns skalnings uppsättning har både systemtilldelade och användarspecifika hanterade identiteter, kan du ta bort alla användare tilldelade hanterade identiteter genom att växla till Använd endast systemtilldelad hanterad identitet.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- De fullständiga snabb starterna för att skapa virtuella Azure-datorer finns i:
  
  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
