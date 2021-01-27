---
title: Lägg till en hanterad identitet till en Service Fabric hanterad kluster nodtyp (för hands version)
description: Den här artikeln visar hur du lägger till en hanterad identitet till en Service Fabric hanterad cluster node-typ
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9edcf75451f43f2a00cd01d5ca7f385704b1ea7f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878434"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Lägg till en hanterad identitet till en Service Fabric hanterad kluster nodtyp (för hands version)

Varje nodtyp i ett Service Fabric hanterat kluster backas upp av en skalnings uppsättning för virtuella datorer. För att tillåta att hanterade identiteter används med en hanterad klusternod, har en egenskap `vmManagedIdentity` lagts till i definitioner av nodtyper som innehåller en lista över identiteter som kan användas `userAssignedIdentities` . Funktionen speglar hur hanterade identiteter kan användas i icke-hanterade kluster, till exempel att använda en hanterad identitet med [Azure Key Vault tillägg för skalnings uppsättning för virtuella datorer](../virtual-machines/extensions/key-vault-windows.md).


Ett exempel på en Service Fabric hanterad kluster distribution som använder hanterad identitet på en Node-typ finns i [den här mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). En lista över regioner som stöds finns i [vanliga frågor och svar om hanterade kluster](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Det finns för närvarande stöd för användare tilldelade identiteter för den här funktionen.

## <a name="prerequisites"></a>Krav

Innan du börjar:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* Om du planerar att använda PowerShell [installerar](/cli/azure/install-azure-cli) du Azure CLI för att köra CLI-referens kommandon.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

En användardefinierad hanterad identitet kan definieras i avsnittet resurser i en Azure Resource Manager-mall (ARM) för att skapas vid distributionen:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

eller skapats via PowerShell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Lägg till en roll tilldelning med Service Fabric Resource Provider

Lägg till en roll tilldelning till den hanterade identiteten med Service Fabric Resource provider-program. Med den här tilldelningen kan Service Fabric resurs leverantör tilldela identiteten till det hanterade klustrets skal uppsättning för den virtuella datorn. 

Följande värden måste användas där tillämpligt:

|Name|Motsvarande Service Fabric resurs leverantörs värde|
|----|-------------------------------------|
|Program-ID|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Objekt-ID|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Roll definitions namn|Roll Definitions-ID|
|----|-------------------------------------|
|Hanterad identitets operator|f1a07417-d97a-45cb-824c-7a7467783830
|



Den här roll tilldelningen kan definieras i avsnittet resurser med ID för objekt-ID och roll definition:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

eller skapats via PowerShell med antingen program-ID och roll Definitions-ID:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

eller objekt-ID och roll Definitions-ID:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Lägg till hanterade identitets egenskaper till nodtypen definition

Slutligen lägger du till `vmManagedIdentity` och- `userAssignedIdentities` egenskaperna i det hanterade klustrets nodtyp definition:

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

Efter distributionen har den skapade hanterade identiteten lagts till i den angivna nodtypens skalnings uppsättning för virtuella datorer och kan användas som förväntat, precis som i alla icke-hanterade kluster.

## <a name="troubleshooting"></a>Felsökning

Om det inte går att lägga till en roll tilldelning korrekt uppfylls följande fel vid distributionen:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure Portal distributions fel som visar klienten med SFRP objekt/program-ID som inte har behörighet att utföra aktiviteten för identitets hantering":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en app till ett Service Fabric hanterat kluster](./tutorial-managed-cluster-deploy-app.md)