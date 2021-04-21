---
title: Konfigurera hanterade identiteter på AZURE VM-skalningsuppsättning med hjälp av REST – Azure AD
description: Stegvisa instruktioner för att konfigurera system- och användar tilldelade hanterade identiteter på en skalningsuppsättning för virtuella Azure-datorer med HJÄLP av CURL för att göra REST API-anrop.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 585e31ab566cc990af2819fcf9cdde0506560208
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780181"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med hjälp REST API anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln använder du CURL för att göra anrop till Azure Resource Manager REST-slutpunkten och lär dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en VM-skalningsuppsättning:

- Aktivera och inaktivera den system tilldelade hanterade identiteten på en vm-skalningsuppsättning i Azure
- Lägga till och ta bort en användar tilldelad hanterad identitet på en VM-skalningsuppsättning i Azure

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

- För att kunna utföra hanteringsåtgärder i den här artikeln behöver ditt konto följande Azure-rolltilldelningar:

  - [Virtuell datordeltagare för](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) att skapa en VM-skalningsuppsättning och aktivera och ta bort system och/eller användar tilldelad hanterad identitet från en VM-skalningsuppsättning.

  - [Rollen Hanterad identitetsdeltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) för att skapa en användar tilldelad hanterad identitet.

  - [Hanterad identitetsoperatörsroll](../../role-based-access-control/built-in-roles.md#managed-identity-operator) för att tilldela och ta bort en användar tilldelad identitet från och till en VM-skalningsuppsättning.

  > [!NOTE]
  > Inga ytterligare azure AD-katalogrolltilldelningar krävs.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar system tilldelad hanterad identitet på en VM-skalningsuppsättning med hjälp av CURL för att göra anrop Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet när en VM-skalningsuppsättning skapas

Om du vill skapa en VM-skalningsuppsättning med system tilldelad hanterad identitet aktiverad måste du skapa en VM-skalningsuppsättning och hämta en åtkomsttoken för att använda CURL för att anropa Resource Manager-slutpunkten med värdet för den system tilldelade hanterade identitetstypen.

1. Skapa en [resursgrupp för](../../azure-resource-manager/management/overview.md#terminology) inneslutning och distribution av vm-skalningsuppsättningen och dess relaterade resurser med [hjälp av az group create](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverksgränssnitt för](/cli/azure/network/nic#az_network_nic_create) vm-skalningsuppsättningen:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Använd Azure Cloud Shell skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i *myResourceGroup* med en system tilldelad hanterad identitet, som identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}` . Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera system tilldelad hanterad identitet på en befintlig VM-skalningsuppsättning

Om du vill aktivera system tilldelad hanterad identitet på en befintlig VM-skalningsuppsättning måste du hämta en åtkomsttoken och sedan använda CURL för att anropa Resource Manager REST-slutpunkten för att uppdatera identitetstypen.

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkten för att aktivera system tilldelad hanterad identitet på vm-skalningsuppsättningen som identifieras i begärandetexten av värdet för en VM-skalningsuppsättning med namnet `{"identity":{"type":"SystemAssigned"}` *myVMSS*.  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och det värde som är lämpligt för din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som har tilldelats till VM-skalningsuppsättningen måste du visa en lista över användar tilldelade hanterade identiteter med hjälp av det här CURL-kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har användar tilldelade hanterade identiteter till VM-skalningsuppsättningen som identifieras i värdet i svaret går du vidare till steg 3 som visar hur du behåller användar tilldelade hanterade identiteter samtidigt som du aktiverar system tilldelad hanterad identitet på din `identity` VM-skalningsuppsättning.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera system tilldelad hanterad identitet på en VM-skalningsuppsättning med befintliga användar tilldelade hanterade identiteter måste du `SystemAssigned` lägga till i `type` värdet.  
   
   Om din VM-skalningsuppsättning till exempel har de användar tilldelade hanterade identiteterna och tilldelats till den, och du vill lägga till en system tilldelad hanterad identitet till VM-skalningsuppsättningen, använder du följande `ID1` `ID2` CURL-anrop. Ersätt `<ACCESS TOKEN>` och med värden som är lämpliga för din `<SUBSCRIPTION ID>` miljö.

   API-versionen lagrar användar tilldelade hanterade identiteter i värdet i ett ordlisteformat i stället för värdet i ett matrisformat som `2018-06-01` `userAssignedIdentities` används i `identityIds` API-versionen `2017-12-01` .
   
   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. |
 
   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Inaktivera system tilldelad hanterad identitet från en VM-skalningsuppsättning

Om du vill inaktivera en system tilldelad identitet på en befintlig VM-skalningsuppsättning måste du hämta en åtkomsttoken och sedan använda CURL för att anropa Resource Manager REST-slutpunkten för att uppdatera identitetstypen till `None` .

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera VM-skalningsuppsättningen med HJÄLP av CURL för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera system tilldelad hanterad identitet.  I följande exempel inaktiveras system tilldelad hanterad identitet som identifieras i begärandetexten av värdet från en VM-skalningsuppsättning `{"identity":{"type":"None"}}` med namnet *myVMSS*.  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som har tilldelats till VM-skalningsuppsättningen måste du visa en lista över användar tilldelade hanterade identiteter med hjälp av det här CURL-kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har en användar tilldelad hanterad identitet tilldelad till VM-skalningsuppsättningen går du vidare till steg 3 som visar hur du behåller de användar tilldelade hanterade identiteterna samtidigt som du tar bort den system tilldelade hanterade identiteten från VM-skalningsuppsättningen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Om du vill ta bort system tilldelad hanterad identitet från en VM-skalningsuppsättning som har användar tilldelade hanterade identiteter tar du bort från värdet och ordlistevärdena om du använder `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **API-version 2018-06-01.** Om du använder **API-version 2017-12-01** eller tidigare behåller du `identityIds` matrisen.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användar tilldelad hanterad identitet på en VM-skalningsuppsättning med hjälp av CURL för att göra anrop Azure Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet när en VM-skalningsuppsättning skapas

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverksgränssnitt för](/cli/azure/network/nic#az_network_nic_create) vm-skalningsuppsättningen:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användar tilldelad hanterad identitet med hjälp av anvisningarna här: [Skapa en användar tilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i resursgruppen *myResourceGroup* med en användar tilldelad hanterad identitet som identifieras i begärandetexten `ID1` med värdet `"identity":{"type":"UserAssigned"}` . Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.
 
   **API-VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. |
 
   **Begärandetext**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig skalningsuppsättning för virtuella Azure-datorer

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en användar tilldelad hanterad identitet med hjälp av anvisningarna här, [Skapa en användar tilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att säkerställa att du inte tar bort befintliga användar- eller system tilldelade hanterade identiteter som har tilldelats till VM-skalningsuppsättningen måste du lista de identitetstyper som tilldelats till VM-skalningsuppsättningen med hjälp av följande CURL-kommando. Om du har hanterade identiteter tilldelade till VM-skalningsuppsättningen visas de i `identity` värdet .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. |   
 

4. Om du inte har några användar- eller system tilldelade hanterade identiteter tilldelade till din VM-skalningsuppsättning använder du följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkten för att tilldela den första användar tilldelade hanterade identiteten till VM-skalningsuppsättningen.  Om du har en eller flera system tilldelade hanterade identiteter tilldelade till VM-skalningsuppsättningen går du vidare till steg 5 som visar hur du lägger till flera användar tilldelade hanterade identiteter i en VM-skalningsuppsättning samtidigt som den system tilldelade hanterade identiteten bibehålls.

   I följande exempel tilldelas en användar tilldelad hanterad identitet till en VM-skalningsuppsättning med namnet `ID1` *myVMSS* i resursgruppen *myResourceGroup.*  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.

   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Om du har en befintlig användar tilldelad eller system tilldelad hanterad identitet till din VM-skalningsuppsättning:
   
   **API-VERSION 2018-06-01**

   Lägg till den användar tilldelade hanterade identiteten i `userAssignedIdentities` ordlistevärdet.

   Om du till exempel har en system tilldelad hanterad identitet och den användar tilldelade hanterade identiteten som för närvarande är tilldelad till din VM-skala och vill lägga till den användar tilldelade hanterade identiteten `ID1` `ID2` i den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   Behåll de användar tilldelade hanterade identiteter som du vill behålla i matrisvärdet samtidigt som du lägger till den `identityIds` nya användar tilldelade hanterade identiteten.

   Om du till exempel har en system tilldelad identitet och den användar tilldelade hanterade identiteten som för närvarande är tilldelad till din VM-skalningsuppsättning och vill lägga till den användar tilldelade hanterade identiteten `ID1` `ID2` i den:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en användar tilldelad hanterad identitet från en VM-skalningsuppsättning

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa din VM-skalningsuppsättning med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som du vill behålla tilldelade till VM-skalningsuppsättningen eller ta bort den system tilldelade hanterade identiteten måste du lista de hanterade identiteterna med hjälp av följande CURL-kommando:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. |
   
   Om du har hanterade identiteter tilldelade till den virtuella datorn visas de i svaret i `identity` värdet . 
    
   Om du till exempel har användar tilldelade hanterade identiteter och tilldelats till din VM-skalningsuppsättning och du bara vill behålla den tilldelade och system tilldelade `ID1` `ID2` `ID1` hanterade identiteten:

   **API-VERSION 2018-06-01**

   Lägg `null` till i den användar tilldelade hanterade identiteten som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   Behåll endast de användar tilldelade hanterade identiteter som du vill behålla i `identityIds` matrisen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

   **Begärandetext**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Om din VM-skalningsuppsättning har både system-tilldelade och användar tilldelade hanterade identiteter kan du ta bort alla användar tilldelade hanterade identiteter genom att byta till att endast använda system-tilldelade med följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

**Begärandetext**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Om vm-skalningsuppsättningen endast har användar tilldelade hanterade identiteter och du vill ta bort alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken. | 

**Begärandetext**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, listar eller tar bort användar tilldelade hanterade identiteter med hjälp av REST finns i:

- [Skapa, visa eller ta bort en användar tilldelad hanterad identitet med hjälp REST API anrop](how-to-manage-ua-identity-rest.md)
