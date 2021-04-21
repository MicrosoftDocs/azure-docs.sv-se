---
title: Konfigurera hanterade identiteter på virtuella Azure-datorer med Azure CLI – Azure AD
description: Stegvisa instruktioner för att konfigurera system- och användar tilldelade hanterade identiteter på en virtuell Azure-dator med Hjälp av Azure CLI.
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
ms.openlocfilehash: a8dbe9665612e888b7e7afe95a472ba6b0de8d48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762523"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln använder du Azure CLI och lär dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en virtuell Azure-dator:

- Aktivera och inaktivera den system tilldelade hanterade identiteten på en virtuell Azure-dator
- Lägga till och ta bort en användar tilldelad hanterad identitet på en virtuell Azure-dator

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den system tilldelade hanterade identiteten på en virtuell Azure-dator med hjälp av Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera system tilldelad hanterad identitet när en virtuell Azure-dator skapas

Om du vill skapa en virtuell Azure-dator med den system tilldelade hanterade identiteten aktiverad måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator med *namnet myVM* med en system tilldelad hanterad identitet, på begäran av `--assign-identity` parametern . Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera system tilldelad hanterad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera system tilldelad hanterad identitet på en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vm identity assign](/cli/azure/vm/identity/) med kommandot enable the `identity assign` system-assigned identity to an existing VM:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Inaktivera system tilldelad identitet från en virtuell Azure-dator

Om du vill inaktivera system tilldelad hanterad identitet på en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver den system tilldelade identiteten, men som fortfarande behöver användar tilldelade identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver en system tilldelad identitet och den inte har några användar tilldelade identiteter använder du följande kommando:

> [!NOTE]
> Värdet är `none` ärendekänsligt. Det måste vara gemener. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användar tilldelad hanterad identitet från en virtuell Azure-dator med hjälp av Azure CLI. Om du skapar en användar tilldelad hanterad identitet i en annan RG än den virtuella datorn. Du måste använda URL:en för din hanterade identitet för att tilldela den till den virtuella datorn.
dvs. --identities "/subscriptions/ <SUBID> /resourcegroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användar tilldelad hanterad identitet när en virtuell Azure-dator skapas

Om du vill tilldela en användar tilldelad identitet till en virtuell dator när den skapas måste ditt konto ha rolltilldelningarna [Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare och [Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp för](~/articles/azure-resource-manager/management/overview.md#terminology) inneslutning och distribution av din användar tilldelade hanterade identitet med [hjälp av az group create](/cli/azure/group/#az_group_create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az_identity_create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Svaret innehåller information om den användar tilldelade hanterade identiteten som skapats, ungefär så här. Resurs-ID-värdet som tilldelats den användar tilldelade hanterade identiteten används i följande steg.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator som är associerad med den nya användar tilldelade identiteten, som anges av `--assign-identity` parametern . Ersätt parametervärdena `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig virtuell Azure-dator

Om du vill tilldela en användar tilldelad identitet till en virtuell dator måste ditt konto ha [rolltilldelningarna Virtuell datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [och Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Skapa en användartilldelad identitet med hjälp av [az identity create](/cli/azure/identity#az_identity_create).  Parametern `-g` anger den resursgrupp där den användar tilldelade identiteten skapas och `-n` parametern anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Det finns för närvarande inte stöd för att skapa användar tilldelade hanterade identiteter med specialtecken (dvs. understreck) i namnet. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [Vanliga frågor och svar och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användar tilldelade hanterade identiteten som skapats, ungefär så här. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Tilldela den användar tilldelade identiteten till den virtuella datorn med [az vm identity assign](/cli/azure/vm). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY NAME>`är den användar tilldelade hanterade identitetens `name` resursegenskap, som du skapade i föregående steg. Om du har skapat din användar tilldelade hanterade identitet i en annan RG än den virtuella datorn. Du måste använda URL:en för din hanterade identitet.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användar tilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användar tilldelad identitet till en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare. 

Om detta är den enda användar tilldelade hanterade identiteten som tilldelats den virtuella datorn tas `UserAssigned` den bort från värdet för identitetstyp.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY>`blir egenskapen för den användar tilldelade identiteten, som finns i `name` identitetsavsnittet på den virtuella datorn med hjälp av `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Om den virtuella datorn inte har en system tilldelad hanterad identitet och du vill ta bort alla användar tilldelade identiteter från den använder du följande kommando:

> [!NOTE]
> Värdet är `none` fallkänsligt. Det måste innehålla gemener.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om den virtuella datorn har både system-tilldelade och användar-tilldelade identiteter kan du ta bort alla användar tilldelade identiteter genom att byta till att endast använda system-tilldelade. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg
- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- De fullständiga snabbstarterna för att skapa virtuella Azure-datorer finns i: 
  - [Skapa en virtuell Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md)
