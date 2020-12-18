---
title: Key Vault hemlighet med mall
description: Visar hur du skickar en hemlighet från ett nyckel valv som en parameter under distributionen.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 8983d51d90e07e21ac4c9489c7ed553530278d3c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672713"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använd Azure Key Vault för att skicka ett säkert parameter värde under distributionen

I stället för att ange ett säkert värde (till exempel ett lösen ord) direkt i din mall eller parameter fil kan du hämta värdet från en [Azure Key Vault](../../key-vault/general/overview.md) under en distribution. Du hämtar värdet genom att referera till nyckel valvet och hemligheten i parameter filen. Värdet exponeras aldrig eftersom du bara refererar till dess nyckelvalvs-ID. Nyckel valvet kan finnas i en annan prenumeration än den resurs grupp som du distribuerar till.

Den här artikeln fokuserar på scenariot med att skicka ett känsligt värde i som en mallparameter. Det beskriver inte scenariot med att ställa in en virtuell dator egenskap till URL: en för ett certifikat i en Key Vault. En snabb starts mall för det scenariot finns i [Installera ett certifikat från Azure Key Vault på en virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Distribuera nyckel valv och hemligheter

Om du vill få åtkomst till ett nyckel valv under mal Lav distributionen anger du `enabledForTemplateDeployment` nyckel valvet till `true` .

Om du redan har ett Key Vault kontrollerar du att det är möjligt att distribuera mallar.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Om du vill skapa en ny Key Vault och lägga till en hemlighet använder du:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Som ägare till nyckel valvet har du automatiskt åtkomst till att skapa hemligheter. Om användaren som arbetar med hemligheter inte är ägare till nyckel valvet ger du till gång till:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Mer information om hur du skapar nyckel valv och lägger till hemligheter finns i:

- [Ange och hämta en hemlighet med hjälp av CLI](../../key-vault/secrets/quick-create-cli.md)
- [Ange och hämta en hemlighet med hjälp av PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Ange och hämta en hemlighet med hjälp av portalen](../../key-vault/secrets/quick-create-portal.md)
- [Ange och hämta en hemlighet med .NET](../../key-vault/secrets/quick-create-net.md)
- [Ange och hämta en hemlighet med hjälp av Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Bevilja åtkomst till hemligheterna

Den användare som distribuerar mallen måste ha `Microsoft.KeyVault/vaults/deploy/action` behörighet för omfånget för resurs gruppen och nyckel valvet. [Ägare](../../role-based-access-control/built-in-roles.md#owner) och [deltagar](../../role-based-access-control/built-in-roles.md#contributor) roller beviljar den här åtkomsten. Om du har skapat nyckel valvet är du ägare så att du har behörigheten.

Följande procedur visar hur du skapar en roll med minsta behörighet och hur du tilldelar användaren

1. Skapa en JSON-fil för anpassad roll definition:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Ersätt "00000000-0000-0000-0000-000000000000" med prenumerations-ID: t.

2. Skapa den nya rollen med hjälp av JSON-filen:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Exemplen tilldelar användaren den anpassade rollen på resurs grupps nivå.

När du använder en Key Vault med mallen för ett [hanterat program](../managed-applications/overview.md)måste du bevilja åtkomst till tjänstens huvud namn för enhets **resurs leverantören** . Mer information finns i [åtkomst Key Vault hemlighet när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Referens hemligheter med statiskt ID

Med den här metoden refererar du till nyckel valvet i parameter filen, inte mallen. Följande bild visar hur parameter filen refererar till hemligheten och skickar värdet till mallen.

![Statiskt ID-diagram för Key Vault-integrering i Resource Manager](./media/key-vault-parameter/statickeyvault.png)

[Självstudie: integrera Azure Key Vault i Resource Manager malldistribution](./template-tutorial-use-key-vault.md) använder den här metoden.

Följande mall distribuerar en SQL-Server som innehåller ett administratörs lösen ord. Lösen ords parametern är inställd på en säker sträng. Men mallen anger inte var värdet kommer från.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Skapa nu en parameter fil för föregående mall. I parameter filen anger du en parameter som matchar namnet på parametern i mallen. Som parameter värde refererar du till hemligheten från nyckel valvet. Du refererar till hemligheten genom att skicka resurs identifieraren för nyckel valvet och namnet på hemligheten:

I följande parameter fil måste nyckel valvets hemlighet redan finnas och du anger ett statiskt värde för resurs-ID: t.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

Om du behöver använda en annan version av hemligheten än den aktuella versionen använder du `secretVersion` egenskapen.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och skicka den till parameter filen:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Referens hemligheter med dynamiskt ID

Föregående avsnitt visade hur du skickar ett statiskt resurs-ID för nyckel valvets hemlighet från parametern. I vissa fall måste du dock referera till en nyckel valvs hemlighet som varierar beroende på den aktuella distributionen. Eller så kanske du vill skicka parameter värden till mallen i stället för att skapa en referens parameter i parameter filen. I båda fallen kan du dynamiskt generera resurs-ID för en nyckel valv hemlighet genom att använda en länkad mall.

Du kan inte dynamiskt generera resurs-ID: t i parameter filen eftersom det inte går att använda Template-uttryck i parameter filen.

I den överordnade mallen lägger du till den kapslade mallen och skickar den till en parameter som innehåller det dynamiskt genererade resurs-ID: t. Följande bild visar hur en parameter i den länkade mallen refererar till hemligheten.

![Dynamiskt ID](./media/key-vault-parameter/dynamickeyvault.png)

Följande mall skapar dynamiskt Key Vault-ID och skickar det som en parameter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Allmän information om nyckel valv finns i [Azure Key Vault?](../../key-vault/general/overview.md).
- Fullständiga exempel på referenser till nyckel hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- En Microsoft Learn-modul som täcker att skicka ett säkert värde från ett nyckel valv finns i [hantera komplexa moln distributioner med hjälp av avancerade funktioner för ARM-mallar](/learn/modules/manage-deployments-advanced-arm-template-features/).
