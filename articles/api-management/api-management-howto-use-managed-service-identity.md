---
title: Använda hanterade identiteter i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar system-tilldelade och användar tilldelade identiteter i API Management med hjälp av Azure Portal, PowerShell och en Resource Manager mall.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812254"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Använda hanterade identiteter i Azure API Management

Den här artikeln visar hur du skapar en hanterad identitet för en Azure API Management instans och hur du kommer åt andra resurser. En hanterad identitet som genereras av Azure Active Directory (Azure AD) gör att din API Management-instans enkelt och säkert kan komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Azure hanterar den här identiteten så att du inte behöver etablera eller rotera några hemligheter. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

Du kan tilldela två typer av identiteter till en API Management instans:

- En *system tilldelad identitet* är kopplad till din tjänst och tas bort om tjänsten tas bort. Tjänsten kan bara ha en system tilldelad identitet.
- En *användar tilldelad identitet* är en fristående Azure-resurs som kan tilldelas till din tjänst. Tjänsten kan ha flera användar tilldelade identiteter.

## <a name="create-a-system-assigned-managed-identity"></a>Skapa en system tilldelad hanterad identitet

### <a name="azure-portal"></a>Azure Portal

Om du vill konfigurera en hanterad identitet i Azure Portal måste du först skapa en API Management-instans och sedan aktivera funktionen.

1. Skapa en API Management instans i portalen som vanligt. Bläddra till den i portalen.
2. Välj **Hanterade identiteter.**
3. På fliken **System tilldelad** växlar du **Status** till **På**. Välj **Spara**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Val för att aktivera en system tilldelad hanterad identitet" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg går igenom hur du skapar en API Management-instans och tilldelar den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell guiden](/powershell/azure/install-az-ps). Kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

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

Du kan skapa en API Management instans med en identitet genom att inkludera följande egenskap i resursdefinitionen:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Den här egenskapen anger att Azure ska skapa och hantera identiteten för din API Management instans.

En fullständig mall Azure Resource Manager till exempel se ut så här:

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

Egenskapen `tenantId` identifierar vilken Azure AD-klientorganisation identiteten tillhör. Egenskapen `principalId` är en unik identifierare för instansens nya identitet. I Azure AD har tjänstens huvudnamn samma namn som du gav till din API Management instansen.

> [!NOTE]
> En API Management-instans kan ha både system-tilldelade och användar-tilldelade identiteter på samma gång. I det här fallet `type` är egenskapen `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Scenarier som stöds med system tilldelad identitet

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Hämta ett anpassat TLS/SSL-certifikat för API Management instansen från Azure Key Vault
Du kan använda den systemtilldeade identiteten för en API Management instans för att hämta anpassade TLS/SSL-certifikat som lagras i Azure Key Vault. Du kan sedan tilldela dessa certifikat till anpassade domäner i API Management instansen. Tänk på följande:

- Innehållstypen för hemligheten måste vara *application/x-pkcs12*.
- Använd den Key Vault slutpunkt för certifikathemlighet som innehåller hemligheten.

> [!Important]
> Om du inte anger objektversionen av certifikatet hämtar API Management automatiskt den nya versionen av certifikatet inom fyra timmar efter att det har uppdaterats i Key Vault.

I följande exempel visas Azure Resource Manager mall som innehåller följande steg:

1. Skapa en API Management instans med en hanterad identitet.
2. Uppdatera åtkomstprinciperna för en Azure Key Vault instans och tillåt att API Management-instansen hämtar hemligheter från den.
3. Uppdatera instansen API Management genom att ange ett anpassat domännamn via ett certifikat från Key Vault instansen.

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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Autentisera till backend-servern med hjälp av API Management identitet

Du kan använda den system tilldelade identiteten för att autentisera till serverslutet via [principen för autentisering hanterad identitet.](api-management-authentication-policies.md#ManagedIdentity)

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Ansluta till Azure-resurser bakom IP-brandväggen med hjälp av system tilldelad hanterad identitet


API Management är en betrodd Microsoft-tjänst till följande resurser. Detta gör att tjänsten kan ansluta till följande resurser bakom en brandvägg. När du uttryckligen har tilldelat lämplig Azure-roll till den system tilldelade hanterade identiteten för den resursinstansen motsvarar åtkomstomfånget för instansen den [Azure-roll](../active-directory/managed-identities-azure-resources/overview.md) som tilldelats den hanterade identiteten.


|Azure-tjänst | Länk|
|---|---|
|Azure Storage | [Trusted-access-to-azure-storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Trusted-access-to-azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

> [!NOTE]
> Du kan associera en API Management-instans med upp till 10 användar tilldelade hanterade identiteter.

### <a name="azure-portal"></a>Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en API Management-instans och aktiverar sedan funktionen.

1. Skapa en API Management instans i portalen som vanligt. Bläddra till den i portalen.
2. Välj **Hanterade identiteter.**
3. På fliken **Användar tilldelad** väljer du Lägg **till**.
4. Sök efter den identitet som du skapade tidigare och välj den. Välj **Lägg till**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Val för att aktivera en användar tilldelad hanterad identitet" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg går igenom hur du skapar en API Management-instans och tilldelar den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell guiden](/powershell/azure/install-az-ps). Kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

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

Du kan skapa en API Management instans med en identitet genom att inkludera följande egenskap i resursdefinitionen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Om du lägger till den användar tilldelade typen anger du att Azure ska använda den användar tilldelade identiteten som angetts för din instans.

En fullständig mall Azure Resource Manager till exempel se ut så här:

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

När tjänsten har skapats har den följande ytterligare egenskaper:

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

Egenskapen `principalId` är en unik identifierare för den identitet som används för Azure AD-administration. Egenskapen är en unik identifierare för programmets nya identitet som används för att ange vilken `clientId` identitet som ska användas under körningssamtal.

> [!NOTE]
> En API Management-instans kan ha både system-tilldelade och användar-tilldelade identiteter på samma gång. I det här fallet `type` är egenskapen `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Scenarier som stöds med användar tilldelad hanterad identitet

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Hämta ett anpassat TLS/SSL-certifikat för API Management instansen från Azure Key Vault
Du kan använda valfri användar tilldelad identitet för att upprätta förtroende mellan en API Management instans och KeyVault. Det här förtroendet kan sedan användas för att hämta anpassade TLS/SSL-certifikat som lagras i Azure Key Vault. Du kan sedan tilldela dessa certifikat till anpassade domäner i API Management instansen. 

Tänk på följande:

- Innehållstypen för hemligheten måste vara *application/x-pkcs12.*
- Använd den Key Vault slutpunkt för certifikathemlighet, som innehåller hemligheten.

> [!Important]
> Om du inte anger objektversionen av certifikatet hämtar API Management automatiskt den nya versionen av certifikatet inom fyra timmar efter att det har uppdaterats i Key Vault.

Den fullständiga mallen finns i använda [API Management KeyVault-baserad SSL med användar tilldelad identitet](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

I den här mallen distribuerar du:

* Azure API Management
* Hanterad användar tilldelad identitet i Azure
* Azure KeyVault för lagring av SSL/TLS-certifikatet

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Autentisera till backend-servern med hjälp av en användar tilldelad identitet

Du kan använda den användar tilldelade identiteten för att autentisera till serverslutet via [principen för autentisering hanterad identitet.](api-management-authentication-policies.md#ManagedIdentity)

## <a name="remove-an-identity"></a><a name="remove"></a>Ta bort en identitet

Du kan ta bort en system tilldelad identitet genom att inaktivera funktionen via portalen eller Azure Resource Manager på samma sätt som den skapades. Användar tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du identitetstypen till `"None"` .

Om du tar bort en system tilldelad identitet på det här sättet tas den även bort från Azure AD. System tilldelade identiteter tas också bort automatiskt från Azure AD när API Management-instansen tas bort.

Om du vill ta bort alla identiteter med hjälp Azure Resource Manager mallen uppdaterar du det här avsnittet:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Om en API Management-instans har konfigurerats med ett anpassat SSL-certifikat från Key Vault och du försöker inaktivera en hanterad identitet, misslyckas begäran.
>
> Du kan avblockera dig själv genom att växla från ett Azure Key Vault-certifikat till ett infogade kodat certifikat och sedan inaktivera den hanterade identiteten. Mer information finns i avsnittet [Så här konfigurerar du ett eget domännamn](configure-custom-domain.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om hanterade identiteter för Azure-resurser:

* [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)
* [Autentisera med en hanterad identitet i en princip](./api-management-authentication-policies.md#ManagedIdentity)
