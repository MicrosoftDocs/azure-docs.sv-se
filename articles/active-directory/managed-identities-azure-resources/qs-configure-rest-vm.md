---
title: Konfigurera hanterade identiteter på virtuella Azure-datorer med hjälp av REST – Azure AD
description: Stegvisa instruktioner för att konfigurera ett system och användar tilldelade hanterade identiteter på en virtuell Azure-dator med hjälp av CURL för att göra REST API anrop.
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
ms.openlocfilehash: 9b74e7d312133c24daad448e029a3c3d4cbdce79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773089"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp REST API anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln använder du CURL för att göra anrop Azure Resource Manager REST-slutpunkten och lär dig hur du utför följande hanterade identiteter för Azure-resursåtgärder på en virtuell Azure-dator:

- Aktivera och inaktivera den system tilldelade hanterade identiteten på en virtuell Azure-dator
- Lägga till och ta bort en användar tilldelad hanterad identitet på en virtuell Azure-dator

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar system tilldelad hanterad identitet på en virtuell Azure-dator med hjälp av CURL för att göra anrop Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera system tilldelad hanterad identitet när en virtuell Azure-dator skapas

Om du vill skapa en virtuell Azure-dator med den system tilldelade hanterade identiteten aktiverad måste ditt konto ha [rolltilldelningen Virtuell datordeltagare.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverksgränssnitt för](/cli/azure/network/nic#az_network_nic_create) den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Med Azure Cloud Shell skapar du en virtuell dator med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* med en system tilldelad hanterad identitet, som identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}` . Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och det värde som är lämpligt för din miljö.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**
   
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 
   
   **Begärandetext**

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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera system tilldelad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera system tilldelad hanterad identitet på en virtuell dator som ursprungligen etablerades utan den måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Hämta en åtkomsttoken för bearer som du kommer att använda i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkten för att aktivera system tilldelad hanterad identitet på den virtuella datorn som identifieras i begärandetexten av värdet för en virtuell dator med namnet `{"identity":{"type":"SystemAssigned"}` *myVM*.  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och det värde som är lämpligt för din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som är tilldelade till den virtuella datorn måste du visa en lista över användar tilldelade hanterade identiteter med hjälp av det här CURL-kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har användar tilldelade hanterade identiteter till den virtuella datorn som identifieras i värdet i svaret går du vidare till steg 3 som visar hur du behåller användar tilldelade hanterade identiteter samtidigt som du aktiverar system tilldelad hanterad identitet på den virtuella `identity` datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 
   
   **Begärandetext**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera system tilldelad hanterad identitet på en virtuell dator med befintliga användar tilldelade hanterade identiteter måste du `SystemAssigned` lägga till i `type` värdet.  
   
   Om den virtuella datorn till exempel har de användar tilldelade hanterade identiteterna och tilldelats till den, och du vill lägga till en system tilldelad hanterad identitet till den virtuella datorn, använder du `ID1` `ID2` följande CURL-anrop. Ersätt `<ACCESS TOKEN>` och med värden som är lämpliga för din `<SUBSCRIPTION ID>` miljö.

   API-versionen lagrar användar tilldelade hanterade identiteter i värdet i ett ordlisteformat i stället för värdet i ett matrisformat som `2018-06-01` `userAssignedIdentities` används i `identityIds` API-version `2017-12-01` .
   
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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera system tilldelad hanterad identitet från en virtuell Azure-dator

Om du vill inaktivera system tilldelad hanterad identitet på en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.  Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorn med curl för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera system tilldelad hanterad identitet.  I följande exempel inaktiveras system tilldelad hanterad identitet som identifieras i begärandetexten av värdet från `{"identity":{"type":"None"}}` en virtuell dator med namnet *myVM*.  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som är tilldelade till den virtuella datorn måste du visa en lista över användar tilldelade hanterade identiteter med hjälp av det här CURL-kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Om du har användar tilldelade hanterade identiteter till den virtuella datorn som identifieras i värdet i svaret går du vidare till steg 3 som visar hur du behåller användar tilldelade hanterade identiteter när du inaktiverar system tilldelad hanterad identitet på den virtuella `identity` datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Om du vill ta bort system tilldelad hanterad identitet från en virtuell dator som har användar tilldelade hanterade identiteter tar du bort från värdet samtidigt som värdet och ordlistevärdena finns kvar om du använder `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **API-version 2018-06-01.** Om du använder **API-version 2017-12-01** eller tidigare behåller du `identityIds` matrisen.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användar tilldelad hanterad identitet på en virtuell Azure-dator med hjälp av CURL för att göra anrop till Azure Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användar tilldelad hanterad identitet när en virtuell Azure-dator skapas

Om du vill tilldela en användar tilldelad identitet till en virtuell dator måste ditt konto ha [rolltilldelningarna Virtuell datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [och Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverksgränssnitt för](/cli/azure/network/nic#az_network_nic_create) den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en åtkomsttoken för bearer som du kommer att använda i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användar tilldelad hanterad identitet med hjälp av anvisningarna här: [Skapa en användar tilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en virtuell dator med hjälp av CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* med en användar tilldelad hanterad identitet som identifieras i begärandetexten `ID1` med värdet `"identity":{"type":"UserAssigned"}` . Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och värdet efter behov för din miljö.
 
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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användar tilldelad hanterad identitet till en befintlig virtuell Azure-dator

Om du vill tilldela en användar tilldelad identitet till en virtuell dator måste ditt konto ha rolltilldelningarna Virtuell [datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [och Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitetsoperatör. Inga ytterligare azure AD-katalogrolltilldelningar krävs.

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en användar tilldelad hanterad identitet med hjälp av anvisningarna här, [Skapa en användar tilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att säkerställa att du inte tar bort befintliga användar- eller system tilldelade hanterade identiteter som är tilldelade till den virtuella datorn måste du visa en lista över de identitetstyper som tilldelats den virtuella datorn med hjälp av följande CURL-kommando. Om du har hanterade identiteter tilldelade till VM-skalningsuppsättningen visas de under i `identity` värdet .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.

    Om du har några användar- eller system tilldelade hanterade identiteter tilldelade till den virtuella datorn som identifieras i värdet i svaret går du vidare till steg 5 som visar hur du behåller den system tilldelade hanterade identiteten när du lägger till en användar tilldelad hanterad identitet på den virtuella `identity` datorn.

4. Om du inte har några användar tilldelade hanterade identiteter tilldelade till den virtuella datorn använder du följande CURL-kommando för att anropa REST-slutpunkten för Azure Resource Manager för att tilldela den första användar tilldelade hanterade identiteten till den virtuella datorn.

   I följande exempel tilldelas en användar tilldelad hanterad identitet till en virtuell dator med namnet `ID1` *myVM* i resursgruppen *myResourceGroup*.  Ersätt med det värde som du fick i föregående steg när du begärde en `<ACCESS TOKEN>` åtkomsttoken för bearer `<SUBSCRIPTION ID>` och det värde som är lämpligt för din miljö.

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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        |
 
   **Begärandetext**

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
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

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

5. Om du har en befintlig användar-tilldelad eller system tilldelad hanterad identitet till den virtuella datorn:
   
   **API-VERSION 2018-06-01**

   Lägg till den användar tilldelade hanterade identiteten i `userAssignedIdentities` ordlistevärdet.
    
   Om du till exempel har en system tilldelad hanterad identitet och den användar tilldelade hanterade identiteten som för närvarande är tilldelad till den virtuella datorn och vill lägga till den användar tilldelade hanterade `ID1` `ID2` identiteten i den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

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

   Om du till exempel har en system tilldelad hanterad identitet och den användar tilldelade hanterade identiteten som för närvarande är tilldelad till den virtuella datorn och vill lägga till den användar tilldelade hanterade identiteten `ID1` `ID2` i den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användar tilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användar tilldelad identitet till en virtuell dator måste ditt konto ha [rolltilldelningen Virtuell](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) datordeltagare.

1. Hämta en åtkomsttoken för bearer som du använder i nästa steg i auktoriseringsrubriken för att skapa den virtuella datorn med en system tilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort befintliga användar tilldelade hanterade identiteter som du vill behålla tilldelade till den virtuella datorn eller ta bort den system tilldelade hanterade identiteten måste du lista de hanterade identiteterna med hjälp av följande CURL-kommando: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.
 
   Om du har hanterade identiteter tilldelade till den virtuella datorn visas de i svaret i `identity` värdet .

   Om du till exempel har användar tilldelade hanterade identiteter och tilldelats till den virtuella datorn och du bara vill behålla den `ID1` `ID2` tilldelade `ID1` identiteten:
   
   **API-VERSION 2018-06-01**

   Lägg `null` till i den användar tilldelade hanterade identiteten som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.        | 

   **Begärandetext**

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

Om den virtuella datorn har både system-tilldelade och användar tilldelade hanterade identiteter kan du ta bort alla användar tilldelade hanterade identiteter genom att byta till att endast använda system-tilldelad hanterad identitet med hjälp av följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
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
    
Om den virtuella datorn bara har användar tilldelade hanterade identiteter och du vill ta bort alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange till en giltig `Bearer` åtkomsttoken.| 

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
