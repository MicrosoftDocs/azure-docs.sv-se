---
title: Skapa Azure-prenumerationer via programmering för ett Microsoft-partneravtal med de senaste API:erna
description: Lär dig hur du skapar Azure-prenumerationer för en Microsoft-partneravtal programmatiskt med de senaste versionerna av REST API, Azure CLI, Azure PowerShell och Azure Resource Manager mallar.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/12/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 69d8910ffe0e45c4c47a035d5c32e71f19d9e04a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870683"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Skapa Azure-prenumerationer via programmering för ett Microsoft-partneravtal med de senaste API:erna

Den här artikeln beskriver hur du skapar Azure-prenumerationer för ett Microsoft-partneravtal via programmering med hjälp av de senaste API-versionerna. Om du fortfarande använder den äldre förhandsversionen läser du [Skapa Azure-prenumerationer via programmering med förhandsversioner av API:er](programmatically-create-subscription-preview.md). 

I den här artikeln får du lära dig hur du skapar prenumerationer programmatiskt med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration programmatiskt styrs prenumerationen av det avtal som du fick när du erhöll Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [Juridisk information för Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Du måste ha rollen global administratör eller administratör i din organisations molnlösningsleverantörskonto för att skapa en prenumeration för ditt faktureringskonto. Mer information finns i [Partnercenter – Tilldela användarroller och behörigheter](/partner-center/permissions-overview).

Om du inte vet om du har åtkomst till ett Microsoft Partner Agreement-konto kan du läsa mer i [Kontrollera åtkomsten till ett Microsoft-partneravtal](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement).

I följande exempel används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Sök efter faktureringskonton som du har åtkomst till

Gör följande begäran för att visa en lista med alla faktureringskonton som du har åtkomst till.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API-svaret visar en lista över faktureringskontona.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Använd egenskapen `displayName` för att identifiera det faktureringskonto som du vill skapa prenumerationer för. Se till att agreementType för kontot är *MicrosoftPartnerAgreement*. Kopiera `name` för kontot. Om du till exempel vill skapa en prenumeration för faktureringskontot `Contoso` kopierar du `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in värdet någonstans så att du kan använda det i nästa steg.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd antingen Azure CLI eller REST API för att hämta det här värdet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
Du får tillbaka en lista över alla faktureringskonton som du har åtkomst till.

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Använd egenskapen displayName för att identifiera det faktureringskonto som du vill skapa prenumerationer för. Se till att agreementType för kontot är MicrosoftPartnerAgreement. Kopiera namnet på kontot. Om du till exempel vill skapa en prenumeration för Contosos faktureringskonto kopierar du 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx. Klistra in värdet någonstans så att du kan använda det i nästa steg.

---

## <a name="find-customers-that-have-azure-plans"></a>Hitta kunder som har Azure-planer

Gör följande begäran, med `name` som kopierades från det första steget (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), för att visa en lista över alla kunder i faktureringskontot som du kan skapa Azure-prenumerationer för.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

API-svaret visar en lista över kunder i faktureringskontot med Azure-planer. Du kan skapa prenumerationer för de här kunderna.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Använd egenskapen `displayName` för att identifiera kunden som du vill skapa prenumerationer för. Kopiera `id` för kunden. Om du till exempel vill skapa en prenumeration för `Fabrikam toys` kopierar du `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Klistra in värdet någonstans så att du kan använda det i kommande steg.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd antingen Azure CLI eller REST API för att hämta det här värdet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

API-svaret visar en lista över kunder i faktureringskontot med Azure-planer. Du kan skapa prenumerationer för de här kunderna.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

Använd egenskapen `displayName` för att identifiera kunden som du vill skapa prenumerationer för. Kopiera `id` för kunden. Om du till exempel vill skapa en prenumeration för `Fabrikam toys` kopierar du `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Klistra in värdet någonstans så att du kan använda det i kommande steg.

---

## <a name="get-the-resellers-for-a-customer"></a>Hämta återförsäljarna för en kund

Det här avsnittet är endast valfritt för indirekta leverantörer.

Om du är en indirekt leverantör i modellen med två nivåer för CSP kan välja en återförsäljare när de skapar prenumerationer för kunder.

### <a name="rest"></a>[REST](#tab/rest)

Gör följande begäran, med `id` som kopierades från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), för att visa en lista över alla återförsäljare som är tillgängliga för en kund.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
API-svaret visar återförsäljarna för kunden:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Använd egenskapen `description` för att identifiera återförsäljaren som associeras med prenumerationen. Kopiera `resellerId` för återförsäljaren. Om du till exempel vill associera `Wingtip` kopierar du `3xxxxx`. Klistra in värdet någonstans så att du kan använda det i nästa steg.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd antingen Azure CLI eller REST API för att hämta det här värdet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gör följande begäran, med som `name` kopierades från det första steget ( ```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) och kunden `name` kopierade från föregående steg ( ```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ).

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

API-svaret visar återförsäljarna för kunden:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

Använd egenskapen `description` för att identifiera återförsäljaren som associeras med prenumerationen. Kopiera `resellerId` för återförsäljaren. Om du till exempel vill associera `Wingtip` kopierar du `3xxxxx`. Klistra in värdet någonstans så att du kan använda det i nästa steg.

---

## <a name="create-a-subscription-for-a-customer"></a>Skapa en prenumeration för en kund

I följande exempel skapas en prenumeration med namnet *Dev Team subscription* för *Fabrikam toys*, och återförsäljaren *Wingtip* associeras med prenumerationen. Du använder det kopierade faktureringsomfånget från föregående steg: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Begärandetext

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Svarsåtgärder

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Du kan göra en GET-begäran med samma URL för att hämta statusen för begäran.

### <a name="request"></a>Förfrågan

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Svarsåtgärder

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

En ”pågående”-status returneras som ett `Accepted`-tillstånd under `provisioningState`. 

Använd det valfria *resellerId* som kopierades från det andra steget i begärandetexten i API:et.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör `Install-Module Az.Subscription` för att installera den senaste versionen av modulen som innehåller cmdleten `New-AzSubscriptionAlias`. Se [Hämta PowerShellGet-modul](/powershell/scripting/gallery/installing-psget) om du vill installera en nyare version av PowerShellGet.

Kör följande [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-kommando med faktureringsomfånget `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

subscriptionId returneras som en del av svaret från kommandot.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Använd det valfria *resellerId* som kopierades från det andra steget i `New-AzSubscriptionAlias`-anropet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först tillägget genom att köra `az extension add --name account` och `az extension add --name alias`.

Kör följande [az account alias create](/cli/azure/account/alias#az_account_alias_create)-kommando. 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

subscriptionId returneras som en del av svaret från kommandot.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Använd det valfria *resellerId* som kopierades från det andra steget i `az account alias create`-anropet.

---

## <a name="use-arm-template"></a>Använda ARM-mall

Föregående avsnitt visade hur du skapar en prenumeration med PowerShell, CLI eller REST API. Om du behöver automatisera skapandet av prenumerationer kan du överväga att använda en Azure Resource Manager mall (ARM-mall).

Följande mall skapar en prenumeration. För `billingScope` anger du kund-ID:t. För `targetManagementGroup` anger du den hanteringsgrupp där du vill skapa prenumerationen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Distribuera mallen på [hanteringsgruppsnivå.](../../azure-resource-manager/templates/deploy-to-management-group.md)

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Med en begärandetext:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx' targetManagementGroup=mg2
```

---

## <a name="next-steps"></a>Nästa steg

* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md).
