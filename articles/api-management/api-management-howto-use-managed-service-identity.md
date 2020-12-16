---
title: Använda hanterade identiteter i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar systemtilldelade och användarspecifika identiteter i API Management med hjälp av Azure Portal, PowerShell och en Resource Manager-mall.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: 2866633503399e9f6d78e8665f7a45d9063f01d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585793"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Använda hanterade identiteter i Azure API Management

Den här artikeln visar hur du skapar en hanterad identitet för en Azure API Management-instans och hur du kommer åt andra resurser. En hanterad identitet som genereras av Azure Active Directory (Azure AD) gör att API Management-instansen enkelt och säkert kan komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Azure hanterar den här identiteten så att du inte behöver etablera eller rotera några hemligheter. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

Du kan bevilja två typer av identiteter för en API Management-instans:

- En *systemtilldelad identitet* är kopplad till din tjänst och tas bort om tjänsten tas bort. Tjänsten kan bara ha en tilldelad identitet.
- En *användardefinierad identitet* är en fristående Azure-resurs som kan tilldelas till din tjänst. Tjänsten kan ha flera användare tilldelade identiteter.

## <a name="create-a-system-assigned-managed-identity"></a>Skapa en systemtilldelad hanterad identitet

### <a name="azure-portal"></a>Azure Portal

Om du vill konfigurera en hanterad identitet i Azure Portal skapar du först en API Management instans och aktiverar sedan funktionen.

1. Skapa en API Management-instans i portalen på vanligt sätt. Bläddra till den i portalen.
2. Välj **hanterade identiteter**.
3. Växla **status** till **på** på fliken **systemtilldelad** . Välj **Spara**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Alternativ för att aktivera en systemtilldelad hanterad identitet" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg beskriver hur du skapar en API Management-instans och tilldelar den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShells guide](/powershell/azure/install-az-ps). Kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

2. Använd följande kod för att skapa instansen. Fler exempel på hur du använder Azure PowerShell med en API Management-instans finns i [API Management PowerShell-exempel](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Uppdatera en befintlig instans för att skapa identiteten:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resurs definitionen:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Den här egenskapen anger att Azure ska skapa och hantera identiteten för din API Management-instans.

En fullständig Azure Resource Manager mall kan till exempel se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

När instansen skapas har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`Egenskapen identifierar vilken Azure AD-klient identiteten tillhör. `principalId`Egenskapen är en unik identifierare för instansens nya identitet. I Azure AD har tjänstens huvud namn samma namn som du gav API Management-instansen.


> [!NOTE]
> En API Management instans kan ha både tilldelade och användarspecifika identiteter på samma gång. I det här fallet är `type` egenskapen `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Scenarier som stöds med hjälp av systemtilldelad identitet

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Hämta ett anpassat TLS/SSL-certifikat för API Management-instansen från Azure Key Vault
Du kan använda den systemtilldelade identiteten för en API Management instans för att hämta anpassade TLS/SSL-certifikat som lagras i Azure Key Vault. Du kan sedan tilldela dessa certifikat till anpassade domäner i API Management-instansen. Tänk på följande:

- Innehålls typen för hemligheten måste vara *Application/x-PKCS12*.
- Använd slut punkten för Key Vault certifikatets hemlighet, som innehåller hemligheten.

> [!Important]
> Om du inte anger objekt versionen av certifikatet får API Management automatiskt en nyare version av certifikatet inom fyra timmar efter det att det har uppdaterats i Key Vault.

I följande exempel visas en Azure Resource Manager mall som innehåller följande steg:

1. Skapa en API Management-instans med en hanterad identitet.
2. Uppdatera åtkomst principerna för en Azure Key Vault-instans och Tillåt att API Management-instansen hämtar hemligheter från den.
3. Uppdatera API Management-instansen genom att ange ett eget domän namn genom ett certifikat från Key Vault-instansen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Autentisera till Server delen med hjälp av en API Management identitet

Du kan använda den systemtilldelade identiteten för att autentisera till Server delen via principen för [autentisering som hanteras av identitet](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

> [!NOTE]
> Du kan associera en API Management-instans med upp till 10 hanterade identiteter som användaren tilldelats.

### <a name="azure-portal"></a>Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en API Management-instans och aktiverar sedan funktionen.

1. Skapa en API Management-instans i portalen på vanligt sätt. Bläddra till den i portalen.
2. Välj **hanterade identiteter**.
3. På fliken **användare tilldelad** väljer du **Lägg till**.
4. Sök efter den identitet som du skapade tidigare och markera den. Välj **Lägg till**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Alternativ för att aktivera en användardefinierad hanterad identitet" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg beskriver hur du skapar en API Management-instans och tilldelar den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [guiden Azure PowerShell](/powershell/azure/install-az-ps). Kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

2. Använd följande kod för att skapa instansen. Fler exempel på hur du använder Azure PowerShell med en API Management-instans finns i [API Management PowerShell-exempel](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Uppdatera en befintlig tjänst för att tilldela en identitet till tjänsten:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resurs definitionen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Genom att lägga till en användardefinierad typ anger att Azure ska använda den användar tilldelnings identitet som angetts för din instans.

En fullständig Azure Resource Manager mall kan till exempel se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

När tjänsten skapas har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`Egenskapen är en unik identifierare för den identitet som används för Azure AD-administration. `clientId`Egenskapen är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas vid körnings anrop.

> [!NOTE]
> En API Management instans kan ha både tilldelade och användarspecifika identiteter på samma gång. I det här fallet är `type` egenskapen `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Scenarier som stöds med hjälp av användare tilldelad hanterad identitet

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Hämta ett anpassat TLS/SSL-certifikat för API Management-instansen från Azure Key Vault
Du kan använda valfri användardefinierad identitet för att upprätta förtroende mellan en API Management-instans och ett nyckel valv. Förtroendet kan sedan användas för att hämta anpassade TLS/SSL-certifikat som lagras i Azure Key Vault. Du kan sedan tilldela dessa certifikat till anpassade domäner i API Management-instansen. 

Tänk på följande:

- Innehålls typen för hemligheten måste vara *Application/x-PKCS12*.
- Använd slut punkten för Key Vault certifikatets hemlighet, som innehåller hemligheten.

> [!Important]
> Om du inte anger objekt versionen av certifikatet får API Management automatiskt en nyare version av certifikatet inom fyra timmar efter det att det har uppdaterats i Key Vault.

Den fullständiga mallen finns i [API Management med nyckel valv baserat SSL med hjälp av användarens tilldelade identitet](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

I den här mallen kommer du att distribuera:

* Azure API Management
* Azure-hanterad användare tilldelad identitet
* Azure-nyckel valv för att lagra SSL/TLS-certifikat

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Autentisera till Server delen med hjälp av en användardefinierad identitet

Du kan använda den användare-tilldelade identiteten för att autentisera till Server delen via principen för autentisering av den [autentiserade identiteten](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="remove-an-identity"></a><a name="remove"></a>Ta bort en identitet

Du kan ta bort en tilldelad identitet genom att inaktivera funktionen via portalen eller Azure Resource Manager-mallen på samma sätt som den skapades. Användare-tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du identitets typen till `"None"` .

Om du tar bort en tilldelad identitet på det här sättet tas även den bort från Azure AD. Systemtilldelade identiteter tas också automatiskt bort från Azure AD när API Management-instansen tas bort.

Uppdatera det här avsnittet om du vill ta bort alla identiteter med hjälp av Azure Resource Manager-mallen:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Om en API Management instans har kon figurer ATS med ett anpassat SSL-certifikat från Key Vault och du försöker inaktivera en hanterad identitet, kommer begäran att Miss sen.
>
> Du kan häva blockeringen själv genom att växla från ett Azure Key Vault certifikat till ett infogat kodat certifikat och sedan inaktivera den hanterade identiteten. Mer information finns i avsnittet [Så här konfigurerar du ett eget domännamn](configure-custom-domain.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hanterade identiteter för Azure-resurser:

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)
* [Autentisera med en hanterad identitet i en princip](./api-management-authentication-policies.md#ManagedIdentity)
