---
title: Konfigurera hanterade identiteter på VM-skalningsuppsättning – Azure CLI – Azure AD
description: Stegvisa instruktioner för att konfigurera system- och användar tilldelade hanterade identiteter på en skalningsuppsättning för virtuella Azure-datorer med hjälp av Azure CLI.
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
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788195"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en skalningsuppsättning för virtuella Azure-datorer med hjälp av Azure CLI:
- Aktivera och inaktivera den system tilldelade hanterade identiteten på en skalningsuppsättning för virtuella Azure-datorer
- Lägga till och ta bort en användar tilldelad hanterad identitet på en vm-skalningsuppsättning i Azure

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

- För att kunna utföra hanteringsåtgärder i den här artikeln behöver ditt konto följande tilldelningar av rollbaserad åtkomstkontroll i Azure:

  - [Virtuell datordeltagare för](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) att skapa en VM-skalningsuppsättning och aktivera och ta bort system och/eller användar tilldelad hanterad identitet från en VM-skalningsuppsättning.

  - [Rollen Hanterad identitetsdeltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användar tilldelad hanterad identitet.

  - [Hanterad identitetsoperatörsroll](../../role-based-access-control/built-in-roles.md#managed-identity-operator) för att tilldela och ta bort en användar tilldelad hanterad identitet från och till en VM-skalningsuppsättning.

  > [!NOTE]
  > Inga ytterligare azure AD-katalogrolltilldelningar krävs.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den system tilldelade hanterade identiteten för en skalningsuppsättning för virtuella Azure-datorer med Hjälp av Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet när en VM-skalningsuppsättning i Azure skapas

Så här skapar du en VM-skalningsuppsättning med den system tilldelade hanterade identiteten aktiverad:

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av vm-skalningsuppsättningen och dess relaterade resurser med [hjälp av az group create](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Skapa](/cli/azure/vmss/#az_vmss_create) en VM-skalningsuppsättning. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* med en system tilldelad hanterad identitet, som begärs av `--assign-identity` parametern . Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet på en befintlig skalningsuppsättning för virtuella Azure-datorer

Om du behöver aktivera den [system](/cli/azure/vmss/identity/#az_vmss_identity_assign) tilldelade hanterade identiteten på en befintlig vm-skalningsuppsättning i Azure:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera system tilldelad hanterad identitet från en vm-skalningsuppsättning i Azure

Om du har en VM-skalningsuppsättning som inte längre behöver den system tilldelade hanterade identiteten, men fortfarande behöver användar tilldelade hanterade identiteter, använder du följande kommando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver en system tilldelad hanterad identitet och den inte har några användar tilldelade hanterade identiteter använder du följande kommando:

> [!NOTE]
> Värdet är `none` fallkänsligt. Det måste innehålla gemener. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och tar bort en användar tilldelad hanterad identitet med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet när en VM-skalningsuppsättning skapas

I det här avsnittet går vi igenom hur du skapar en VM-skalningsuppsättning och tilldelning av en användar tilldelad hanterad identitet till VM-skalningsuppsättningen. Om du redan har en VM-skalningsuppsättning som du vill använda kan du hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp för](~/articles/azure-resource-manager/management/overview.md#terminology) inneslutning och distribution av din användar tilldelade hanterade identitet med [hjälp av az group create](/cli/azure/group/#az_group_create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az_identity_create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Svaret innehåller information om den användar tilldelade hanterade identiteten som skapats, ungefär så här. Resursvärdet `id` som tilldelats den användar tilldelade hanterade identiteten används i följande steg.

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

3. [Skapa en](/cli/azure/vmss/#az_vmss_create) VM-skalningsuppsättning. I följande exempel skapas en VM-skalningsuppsättning som är associerad med den nya användar tilldelade hanterade identiteten, som anges av `--assign-identity` parametern . Ersätt parametervärdena `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY>` med dina egna värden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig VM-skalningsuppsättning

1. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az_identity_create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användar tilldelade hanterade identiteten som skapats, ungefär så här.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Tilldela](/cli/azure/vmss/identity) den användar tilldelade hanterade identiteten till din VM-skalningsuppsättning. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY>`är den användartilldelningsidentitetens `name` resursegenskap, som du skapade i föregående steg:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användar tilldelad hanterad identitet från en skalningsuppsättning för virtuella Azure-datorer

Om du [vill ta](/cli/azure/vmss/identity#az_vmss_identity_remove) bort en användar tilldelad hanterad identitet från en VM-skalningsuppsättning använder du `az vmss identity remove` . Om detta är den enda användar tilldelade hanterade identiteten som tilldelats vm-skalningsuppsättningen tas `UserAssigned` den bort från värdet för identitetstyp.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. är den användar tilldelade hanterade identitetens egenskap, som finns i identitetsavsnittet i `<USER ASSIGNED IDENTITY>` `name` VM-skalningsuppsättningen med hjälp av `az vmss identity show` :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Om VM-skalningsuppsättningen inte har en system tilldelad hanterad identitet och du vill ta bort alla användar tilldelade hanterade identiteter från den använder du följande kommando:

> [!NOTE]
> Värdet är `none` ärendekänsligt. Det måste vara gemener.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om din VM-skalningsuppsättning har både system-tilldelade och användar tilldelade hanterade identiteter kan du ta bort alla användar tilldelade identiteter genom att byta till att endast använda system-tilldelade hanterade identiteter. Ange följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Den fullständiga snabbstarten för att skapa skalningsuppsättningen för virtuella Azure-datorer finns [i Skapa en VM-skalningsuppsättning med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
