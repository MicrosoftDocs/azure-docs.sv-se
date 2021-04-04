---
title: Konfigurera hanterade identiteter på virtuella Azure-datorer med REST-Azure AD
description: Stegvisa instruktioner för hur du konfigurerar ett system och användare som tilldelats hanterade identiteter på en virtuell Azure-dator med hjälp av sväng för att göra REST API-anrop.
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
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587527"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av REST API-anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad system identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln använder du en sväng för att ringa till Azure Resource Manager REST-slutpunkten. du får lära dig hur du utför följande hanterade identiteter för Azure-resurser på en virtuell Azure-dator:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en virtuell Azure-dator
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell Azure-dator

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser, se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Information om systemtilldelade och användarspecifika hanterade identitets typer finns i [hanterade identitets typer](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad hanterad identitet på en virtuell Azure-dator med hjälp av sväng för att ringa till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell Azure-dator skapas

För att skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverks gränssnitt](/cli/azure/network/nic#az-network-nic-create) för den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Med hjälp av Azure Cloud Shell skapar du en virtuell dator med hjälp av spiral för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* med en systemtilldelad hanterad identitet, som identifieras i begär ande texten av värdet `"identity":{"type":"SystemAssigned"}` . Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du begärde en Bearer-åtkomsttoken och `<SUBSCRIPTION ID>` värdet som passar din miljö.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**
   
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 
   
   **Brödtext i begäran**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator som ursprungligen etablerades utan den, måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande spiral kommando för att anropa Azure Resource Manager REST-slutpunkten för att aktivera systemtilldelad hanterad identitet på den virtuella datorn som identifieras i begär ande texten av värdet `{"identity":{"type":"SystemAssigned"}` för en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du begärde en Bearer-åtkomsttoken och `<SUBSCRIPTION ID>` värdet som passar din miljö.
   
   > [!IMPORTANT]
   > För att se till att du inte tar bort befintliga hanterade identiteter som har tilldelats den virtuella datorn, måste du ange de användar tilldelade hanterade identiteterna med hjälp av det här spiral kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har tilldelade tilldelade hanterade identiteter som har tilldelats den virtuella datorn som identifieras i `identity` värdet i svaret går du vidare till steg 3 som visar hur du behåller användarspecifika hanterade identiteter samtidigt som du aktiverar systemtilldelad hanterad identitet på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 
   
   **Brödtext i begäran**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator med befintliga användare som tilldelats hanterade identiteter måste du lägga till `SystemAssigned` `type` värdet.  
   
   Om den virtuella datorn till exempel har tilldelats tilldelade hanterade identiteter `ID1` och `ID2` tilldelats den, och du vill lägga till systemtilldelad hanterad identitet till den virtuella datorn, använder du följande anrop för att ringa upp datorn. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpliga för din miljö.

   API-version `2018-06-01` lagrar användardefinierad hanterade identiteter i `userAssignedIdentities` värdet i ett Dictionary-format i stället för `identityIds` värdet i ett mat ris format som används i API-versionen `2017-12-01` .
   
   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad hanterad identitet från en virtuell Azure-dator

Om du vill inaktivera systemtilldelad hanterad identitet på en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorn med hjälp av spiral för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelad hanterad identitet.  I följande exempel inaktive ras systemtilldelad hanterad identitet som identifieras i begär ande texten av värdet `{"identity":{"type":"None"}}` från en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du begärde en Bearer-åtkomsttoken och `<SUBSCRIPTION ID>` värdet som passar din miljö.

   > [!IMPORTANT]
   > För att se till att du inte tar bort befintliga hanterade identiteter som har tilldelats den virtuella datorn, måste du ange de användar tilldelade hanterade identiteterna med hjälp av det här spiral kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har tilldelade tilldelade hanterade identiteter som har tilldelats den virtuella datorn som identifieras i `identity` värdet i svaret går du vidare till steg 3 som visar hur du behåller användarspecifika hanterade identiteter samtidigt som du inaktiverar systemtilldelad hanterad identitet på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Om du vill ta bort systemtilldelad hanterad identitet från en virtuell dator som har tilldelade hanterade identiteter tar `SystemAssigned` du bort från `{"identity":{"type:" "}}` värdet samtidigt som `UserAssigned` du behåller värdet och `userAssignedIdentities` värdena för ord listan om du använder **API version 2018-06-01**. Behåll matrisen om du använder **API version 2017-12-01** eller tidigare `identityIds` .

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användarspecifika hanterade identiteter på en virtuell Azure-dator med hjälp av sväng för att ringa till Azure Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användardefinierad hanterad identitet när en virtuell Azure-dator skapas

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverks gränssnitt](/cli/azure/network/nic#az-network-nic-create) för den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användardefinierad hanterad identitet med hjälp av anvisningarna här: [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en virtuell dator med hjälp av sväng för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* i resurs gruppen *myResourceGroup* med en användardefinierad hanterad identitet `ID1` , som identifieras i begär ande texten med värdet `"identity":{"type":"UserAssigned"}` . Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du begärde en Bearer-åtkomsttoken och `<SUBSCRIPTION ID>` värdet som passar din miljö.
 
   **API-VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användardefinierad hanterad identitet till en befintlig virtuell Azure-dator

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) och [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en tilldelad hanterad identitet med hjälp av anvisningarna här, [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att se till att du inte tar bort befintliga användare eller systemtilldelade hanterade identiteter som har tilldelats den virtuella datorn, måste du lista de identitets typer som tilldelats den virtuella datorn med hjälp av följande kommando för vändning. Om du har hanterade identiteter som har tilldelats den virtuella datorns skal uppsättning visas de under i `identity` värdet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.

    Om du har tilldelade användare eller systemtilldelade hanterade identiteter som har tilldelats den virtuella datorn enligt `identity` värdet i svaret går du vidare till steg 5 som visar hur du behåller den systemtilldelade hanterade identiteten och lägger till en användardefinierad hanterad identitet på den virtuella datorn.

4. Om du inte har några tilldelade hanterade identiteter som tilldelats den virtuella datorn använder du följande spiral kommando för att anropa Azure Resource Manager REST-slutpunkten för att tilldela den första användarspecifika hanterade identiteten till den virtuella datorn.

   I följande exempel tilldelas en användardefinierad hanterad identitet `ID1` till en virtuell dator med namnet *myVM* i resurs gruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du begärde en Bearer-åtkomsttoken och `<SUBSCRIPTION ID>` värdet som passar din miljö.

   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        |
 
   **Brödtext i begäran**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

5. Om du har en befintlig användardefinierad eller systemtilldelad hanterad identitet som är tilldelad till den virtuella datorn:
   
   **API-VERSION 2018-06-01**

   Lägg till den användarspecifika hanterade identiteten i `userAssignedIdentities` värdet för ord listan.
    
   Om du till exempel har en systemtilldelad hanterad identitet och den användarspecifika hanterade identitet som `ID1` är kopplad till den virtuella datorn och vill lägga till den tilldelade hanterade identiteten `ID2` i den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

   Behåll de användare som tilldelats hanterade identiteter som du vill behålla i `identityIds` mat ris värdet när du lägger till den nya användarspecifika hanterade identiteten.

   Om du till exempel har en systemtilldelad hanterad identitet och den användarspecifika hanterade identitet som `ID1` är kopplad till den virtuella datorn och vill lägga till den tilldelade hanterade identiteten `ID2` i den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats en hanterad identitet från en virtuell Azure-dator

För att ta bort en tilldelad identitet till en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa en virtuell dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att se till att du inte tar bort några befintliga tilldelade hanterade identiteter som du vill behålla till den virtuella datorn eller ta bort den systemtilldelade hanterade identiteten, måste du lista de hanterade identiteterna med följande spiral kommando: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.
 
   Om du har hanterade identiteter som har tilldelats den virtuella datorn visas de i svaret i `identity` värdet.

   Om du till exempel har tilldelat användar hanterade identiteter `ID1` och `ID2` tilldelats till din virtuella dator, och du bara vill behålla `ID1` tilldelade och behålla den tilldelade identiteten:
   
   **API-VERSION 2018-06-01**

   Lägg till i `null` den användare-tilldelade hanterade identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

   Behåll endast de användare som tilldelats de hanterade identiteter som du vill behålla i `identityIds` matrisen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Om den virtuella datorn har både systemtilldelade och användarspecifika hanterade identiteter kan du ta bort alla användare tilldelade hanterade identiteter genom att växla till Använd endast systemtilldelad hanterad identitet med följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Om den virtuella datorn endast har användare tilldelade hanterade identiteter och du vill ta bort alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.| 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, listar eller tar bort användare som tilldelats hanterade identiteter med hjälp av REST finns i:

- [Skapa, Visa eller ta bort en användare som tilldelats hanterade identiteter med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)
