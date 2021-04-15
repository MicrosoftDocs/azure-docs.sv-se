---
title: Konfigurera en mall för att använda hanterade identiteter på VM-skalningsuppsättningar – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med hjälp av en Azure Resource Manager mall.
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
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e948b96022972dcf702ac5a4d8be85c9afe16e7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365985"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurera hanterade identiteter för Azure-resurser på en skalning av en virtuell Azure-dator med hjälp av en mall

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden.

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en skalningsuppsättning för virtuella Azure-datorer med hjälp Azure Resource Manager en distributionsmall:

- Aktivera och inaktivera den system tilldelade hanterade identiteten på en skalningsuppsättning för virtuella Azure-datorer
- Lägga till och ta bort en användar tilldelad hanterad identitet på en vm-skalningsuppsättning i Azure

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar tilldelad hanterad identitet.](overview.md#managed-identity-types)**
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill utföra hanteringsåtgärder i den här artikeln behöver ditt konto följande tilldelningar av rollbaserad åtkomstkontroll i Azure:

    > [!NOTE]
    > Inga ytterligare azure AD-katalogrolltilldelningar krävs.

    - [Virtuell datordeltagare för](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) att skapa en VM-skalningsuppsättning och aktivera och ta bort system och/eller användar tilldelad hanterad identitet från en VM-skalningsuppsättning.
    - [Rollen Hanterad identitetsdeltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användar tilldelad hanterad identitet.
    - [Hanterad identitetsoperatörsroll](../../role-based-access-control/built-in-roles.md#managed-identity-operator) för att tilldela och ta bort en användar tilldelad hanterad identitet från och till en VM-skalningsuppsättning.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure Portal och skript ger [Azure Resource Manager-mallar](../../azure-resource-manager/management/overview.md) möjligheten att distribuera nya eller ändrade resurser som definieras av en Azure-resursgrupp. Det finns flera alternativ för mallredigering och -distribution, både lokala och portalbaserade, inklusive:

   - Med hjälp [av en anpassad mall Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), som gör att du kan skapa en mall från grunden, eller basera den på en befintlig gemensam mall eller [snabbstartsmall.](https://azure.microsoft.com/documentation/templates/)
   - Här härledas från en befintlig resursgrupp genom att exportera en mall från antingen den ursprungliga distributionen [eller](../../azure-resource-manager/templates/export-template-portal.md)från det aktuella tillståndet [för distributionen](../../azure-resource-manager/templates/export-template-portal.md).
   - Använda en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
   - Med hjälp Visual Studio [Azure-resursgruppsprojekt för](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer är mallsyntaxen densamma under den första distributionen och omdistributionen. Du aktiverar hanterade identiteter för Azure-resurser på en ny eller befintlig virtuell dator på samma sätt. Som standard gör Azure Resource Manager inkrementell [uppdatering](../../azure-resource-manager/templates/deployment-modes.md) av distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet aktiverar och inaktiverar du den system tilldelade hanterade identiteten med hjälp av Azure Resource Manager mall.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet när du skapar en VM-skalningsuppsättning eller en befintlig VM-skalningsuppsättning

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller VM-skalningsuppsättningen.
2. Om du vill aktivera den system tilldelade hanterade identiteten läser du in mallen i ett redigeringsprogram, letar upp den intressanta resursen i resursavsnittet och lägger till egenskapen på samma `Microsoft.Compute/virtualMachinesScaleSets` `identity` nivå som `"type": "Microsoft.Compute/virtualMachinesScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

4. När du är klar bör följande avsnitt läggas till i resursavsnittet i mallen och bör likna följande:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
        
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en system tilldelad hanterad identitet från en skalningsuppsättning för virtuella Azure-datorer

Om du har en VM-skalningsuppsättning som inte längre behöver en system tilldelad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med Azure-prenumerationen som innehåller VM-skalningsuppsättningen.

2. Läs in mallen i [ett redigeringsprogram](#azure-resource-manager-templates) och `Microsoft.Compute/virtualMachineScaleSets` leta upp den intressanta resursen i `resources` avsnittet. Om du har en virtuell dator som bara har en system tilldelad hanterad identitet kan du inaktivera den genom att ändra identitetstypen till `None` .

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion är och den virtuella datorn har både system- och användartilldelade hanterade identiteter tar du bort från identitetstypen och fortsätter med ordlistevärdena `2018-06-01` `SystemAssigned` `UserAssigned` userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion är och vm-skalningsuppsättningen har både system- och användar tilldelade hanterade identiteter tar du bort från identitetstypen och håller med matrisen för de användar tilldelade hanterade `2017-12-01` `SystemAssigned` `UserAssigned` `identityIds` identiteterna.



   I följande exempel visas hur du tar bort en system tilldelad hanterad identitet från en VM-skalningsuppsättning utan användar tilldelade hanterade identiteter:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet tilldelar du en användar tilldelad hanterad identitet till en VM-skalningsuppsättning med hjälp Azure Resource Manager mall.

> [!Note]
> Information om hur du skapar en användar tilldelad hanterad identitet med hjälp Azure Resource Manager en mall finns i [Skapa en användar tilldelad hanterad identitet.](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet till en VM-skalningsuppsättning

1. Under elementet `resources` lägger du till följande post för att tilldela en användar tilldelad hanterad identitet till din VM-skalningsuppsättning.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på den användar tilldelade hanterade identitet som du skapade.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion är lagras dina användar tilldelade hanterade identiteter i ordlisteformat och värdet måste lagras i en variabel som definieras i avsnittet i `2018-06-01` `userAssignedIdentities` `<USERASSIGNEDIDENTITYNAME>` `variables` mallen.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**

   Om du är eller tidigare lagras dina användardefinierade hanterade identiteter i matrisen och värdet måste lagras i en variabel som definieras i avsnittet variabler `apiVersion` `2017-12-01` i `identityIds` `<USERASSIGNEDIDENTITYNAME>` mallen.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }

3. When you are done, your template should look similar to the following:

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API version 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort användar tilldelad hanterad identitet från en vm-skalningsuppsättning i Azure

Om du har en VM-skalningsuppsättning som inte längre behöver en användar tilldelad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller VM-skalningsuppsättningen.

2. Läs in mallen i [ett redigeringsprogram](#azure-resource-manager-templates) och `Microsoft.Compute/virtualMachineScaleSets` leta upp den intressanta resursen i `resources` avsnittet. Om du har en VM-skalningsuppsättning som bara har användar tilldelad hanterad identitet kan du inaktivera den genom att ändra identitetstypen till `None` .

   I följande exempel visas hur du tar bort alla användar tilldelade hanterade identiteter från en virtuell dator utan system tilldelade hanterade identiteter:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om du vill ta bort en enskild användar tilldelad hanterad identitet från en VM-skalningsuppsättning tar du bort den `userAssignedIdentities` från ordlistan.

   Om du har en system tilldelad identitet behåller du den `type` i värdet under värdet `identity` .

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**

   Om du vill ta bort en enskild användar tilldelad hanterad identitet från en VM-skalningsuppsättning tar du bort den från `identityIds` matrisen.

   Om du har en system tilldelad hanterad identitet behåller du den `type` i värdet under värdet `identity` .

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser.](overview.md)