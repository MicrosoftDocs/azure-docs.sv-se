---
title: Konfigurera mallen för att använda hanterade identiteter på virtuella dator skalnings uppsättningar – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en skal uppsättning för virtuella datorer med hjälp av en Azure Resource Manager mall.
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
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587459"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurera hanterade identiteter för Azure-resurser på en skala för virtuella Azure-datorer med hjälp av en mall

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod.

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella Azure-datorer med hjälp av Azure Resource Manager distributions mal len:
- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en skalnings uppsättning för virtuella Azure-datorer
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell Azure-dators skalnings uppsättning

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanterings åtgärderna i den här artikeln måste ditt konto ha följande Azure-rollbaserade åtkomst kontroll tilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD Directory-roll tilldelningar krävs.

    - [Virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för att skapa en skalnings uppsättning för virtuella datorer och aktivera och ta bort system-och/eller användarspecifika hanterade identiteter från en skalnings uppsättning för virtuella datorer.
    - Rollen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användardefinierad hanterad identitet.
    - [Hanterad identitet operatörs](../../role-based-access-control/built-in-roles.md#managed-identity-operator) roll för att tilldela och ta bort en användardefinierad hanterad identitet från och till en skalnings uppsättning för virtuella datorer.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure Portal och skript ger [Azure Resource Manager](../../azure-resource-manager/management/overview.md) mallar möjlighet att distribuera nya eller ändrade resurser som definieras av en Azure-resurs grupp. Det finns flera alternativ för att redigera och distribuera mallar, både lokalt och Portal-baserat, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), som gör att du kan skapa en mall från grunden eller basera den på en befintlig gemensam eller [snabb starts mall](https://azure.microsoft.com/documentation/templates/).
   - Härleda från en befintlig resurs grupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/templates/export-template-portal.md)eller från det [aktuella läget för distributionen](../../azure-resource-manager/templates/export-template-portal.md).
   - Använda en lokal [JSON-redigerare (till exempel vs-kod)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)och ladda upp och distribuera med hjälp av POWERSHELL eller cli.
   - Använd Visual Studio [Azures resurs grupps projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för att både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är mallens syntax detsamma vid den första distributionen och omdistributionen. Att aktivera hanterade identiteter för Azure-resurser på en ny eller befintlig virtuell dator görs på samma sätt. Som standard gör Azure Resource Manager också en [stegvis uppdatering](../../azure-resource-manager/templates/deployment-modes.md) av distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet ska du aktivera och inaktivera den systemtilldelade hanterade identiteten med hjälp av en Azure Resource Manager mall.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet under skapandet av en skalnings uppsättning för virtuella datorer eller en befintlig skalnings uppsättning för virtuell dator

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorns skalnings uppsättning.
2. Om du vill aktivera den systemtilldelade hanterade identiteten läser du in mallen i ett redigerings program, letar reda på `Microsoft.Compute/virtualMachinesScaleSets` intresse resursen i avsnittet resurser och lägger till `identity` egenskapen på samma nivå som `"type": "Microsoft.Compute/virtualMachinesScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Du kan välja att tillhandahålla hanterade identiteter för Azure-resursers tillägg för skalnings uppsättning för virtuella datorer genom att ange det i `extensionProfile` elementets-mall. Det här steget är valfritt, som du kan använda för identitets slut punkten för Azure Instance Metadata Service (IMDS) för att hämta tokens också.  Mer information finns i [Migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).


4. När du är klar ska följande avsnitt läggas till i resurs avsnittet i mallen och bör likna följande:

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en systemtilldelad hanterad identitet från en skalnings uppsättning för virtuella Azure-datorer

Om du har en skalnings uppsättning för virtuella datorer som inte längre behöver en systemtilldelad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorns skalnings uppsättning.

2. Läs in mallen i en [redigerare](#azure-resource-manager-templates) och leta upp `Microsoft.Compute/virtualMachineScaleSets` resursens intresse i `resources` avsnittet. Om du har en virtuell dator som bara har systemtilldelad hanterad identitet kan du inaktivera den genom att ändra identitets typen till `None` .

   **Microsoft. Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din API version är `2018-06-01` och den virtuella datorn har både system-och användarspecifika hanterade identiteter, tar du bort `SystemAssigned` från identitets typen och hålls `UserAssigned` tillsammans med userAssignedIdentities-Ordlistans värden.

   **Microsoft. Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din API version är `2017-12-01` och din skalnings uppsättning för den virtuella datorn har både system-och användare tilldelade hanterade identiteter, tar `SystemAssigned` du bort från identitets typen och behåller `UserAssigned` `identityIds` matrisen med de användardefinierade hanterade identiteterna.



   I följande exempel visas hur du tar bort en systemtilldelad hanterad identitet från en skalnings uppsättning för virtuella datorer utan användare tilldelade hanterade identiteter:

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

I det här avsnittet tilldelar du en användardefinierad hanterad identitet till en skalnings uppsättning för virtuella datorer med hjälp av Azure Resource Manager mall.

> [!Note]
> Om du vill skapa en användardefinierad hanterad identitet med hjälp av en Azure Resource Manager mall, se [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet till en virtuell dators skalnings uppsättning

1. Under `resources` elementet lägger du till följande post för att tilldela en användardefinierad hanterad identitet till den virtuella datorns skalnings uppsättning.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på den användare-tilldelade hanterade identitet som du har skapat.

   **Microsoft. Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din API version är `2018-06-01` , lagras dina användarspecifika hanterade identiteter i `userAssignedIdentities` ord listans format och `<USERASSIGNEDIDENTITYNAME>` värdet måste lagras i en variabel som definierats i `variables` avsnittet i mallen.

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

   **Microsoft. Compute/virtualMachineScaleSets API version 2017-12-01**

   Om din `apiVersion` är `2017-12-01` eller tidigare lagras dina användares tilldelade hanterade identiteter i `identityIds` matrisen och `<USERASSIGNEDIDENTITYNAME>` värdet måste lagras i en variabel som definierats i avsnittet variabler i mallen.

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
   ```
> [!NOTE]
> Du kan välja att tillhandahålla hanterade identiteter för Azure-resursers tillägg för skalnings uppsättning för virtuella datorer genom att ange det i `extensionProfile` elementets-mall. Det här steget är valfritt, som du kan använda för identitets slut punkten för Azure Instance Metadata Service (IMDS) för att hämta tokens också.  Mer information finns i [Migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).

3. När du är färdig bör mallen se ut ungefär så här:

   **Microsoft. Compute/virtualMachineScaleSets API version 2018-06-01**   

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft. Compute/virtualMachines API version 2017-12-01**

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort användardefinierad hanterad identitet från en skalnings uppsättning för virtuella Azure-datorer

Om du har en skalnings uppsättning för virtuella datorer som inte längre behöver en användardefinierad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorns skalnings uppsättning.

2. Läs in mallen i en [redigerare](#azure-resource-manager-templates) och leta upp `Microsoft.Compute/virtualMachineScaleSets` resursens intresse i `resources` avsnittet. Om du har en skalnings uppsättning för virtuella datorer som bara har användardefinierad hanterad identitet kan du inaktivera den genom att ändra identitets typen till `None` .

   I följande exempel visas hur du tar bort alla användare tilldelade hanterade identiteter från en virtuell dator utan systemtilldelade hanterade identiteter:

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

   **Microsoft. Compute/virtualMachineScaleSets API version 2018-06-01**

   Om du vill ta bort en enskild användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer tar du bort den från `userAssignedIdentities` ord listan.

   Om du har en systemtilldelad identitet behåller du den i `type` värdet under `identity` värdet.

   **Microsoft. Compute/virtualMachineScaleSets API version 2017-12-01**

   Om du vill ta bort en enskild användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer tar du bort den från `identityIds` matrisen.

   Om du har en systemtilldelad hanterad identitet behåller du den i `type` värdet under `identity` värdet.

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md).