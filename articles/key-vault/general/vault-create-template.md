---
title: Skapa ett Azure-nyckelvalv och en åtkomstprincip för valv med hjälp av EN ARM-mall
description: Den här artikeln visar hur du skapar Azure-nyckelvalv och åtkomstprinciper för valv med hjälp Azure Resource Manager mall.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: d157419614ee3a3f89036177e962e5b7fc4466b2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815041"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Så här skapar du ett Azure-nyckelvalv och en åtkomstprincip för valv med hjälp Resource Manager en mall

[Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för hemligheter som nycklar, lösenord och certifikat. I den här artikeln beskrivs processen för att distribuera en Azure Resource Manager (ARM-mall) för att skapa ett nyckelvalv.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Förutsättningar

Så här slutför du stegen i den här artikeln:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-a-key-vault-resource-manager-template"></a>Skapa en Key Vault Resource Manager mall

Följande mall visar ett grundläggande sätt att skapa ett nyckelvalv. Vissa värden anges i mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Mer information om Key Vault finns i Key Vault [ARM-mallreferens.](/azure/templates/microsoft.keyvault/vaults)

> [!IMPORTANT]
> Om en mall omdistribueras åsidosätts alla befintliga åtkomstprinciper i nyckelvalvet. Vi rekommenderar att du fyller i egenskapen `accessPolicies` med befintliga åtkomstprinciper för att undvika att förlora åtkomsten till nyckelvalvet. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Lägga till en åtkomstprincip i en Key Vault Resource Manager mall

Du kan distribuera åtkomstprinciper till ett befintligt nyckelvalv utan att omdistribuera hela nyckelvalvsmallen. Följande mall visar ett grundläggande sätt att skapa åtkomstprinciper:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Mer information om Key Vault finns i Key Vault [arm-mallreferens.](/azure/templates/microsoft.keyvault/vaults/accesspolicies)

## <a name="more-key-vault-resource-manager-templates"></a>Fler Key Vault Resource Manager mallar

Det finns andra Resource Manager tillgängliga för Key Vault objekt:

| Hemligheter | Nycklar | Certifikat |
|--|--|--|
|<ul><li>[Snabbstart](../secrets/quick-create-template.md)<li>[Referens](/azure/templates/microsoft.keyvault/vaults/secrets)|Saknas|Saknas|

Du hittar fler Key Vault här: Key Vault Resource Manager [referens](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Distribuera mallarna

Du kan använda Azure Portal för att distribuera mallarna  ovan med hjälp av alternativet Skapa en egen mall i redigeraren enligt beskrivningen här: Distribuera resurser från [en anpassad mall](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Du kan också spara mallarna ovan i filer och använda [följande kommandon:](/cli/azure/deployment/group#az_deployment_group_create) [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och az deployment group create :

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna dessa resurser på plats. Ta bort resursgruppen när du inte behöver resurserna längre. Om du tar bort gruppen tas även nyckelvalvet och relaterade resurser bort. Om du vill ta bort resursgruppen med hjälp av Azure CLI eller Azure PowerShell gör du följande:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Resurser

- Läs en [översikt över Azure Key Vault](../general/overview.md).
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Granska Azure Key Vault [säkerhetsöversikten](security-features.md)

## <a name="next-steps"></a>Nästa steg

- [Säker åtkomst till ett nyckelvalv](security-features.md)
- [Autentisera till ett nyckelvalv](authentication.md)
- [Utvecklarguide för Azure Key Vault](developers-guide.md)
