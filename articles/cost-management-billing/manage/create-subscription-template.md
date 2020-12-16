---
title: Skapa Azure-prenumerationer programmatiskt med en Azure Resource Manager-mall
description: Lär dig hur du skapar Azure-prenumerationer programmatiskt med hjälp av en Azure Resource Manager-mall.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 21791b096d66b73c522c50f38cae7fd8a1b9fb70
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507565"
---
# <a name="programmatically-create-azure-subscriptions-with-an-azure-resource-manager-template"></a>Skapa Azure-prenumerationer programmatiskt med en Azure Resource Manager-mall

Den här artikeln beskriver hur du skapar Azure-prenumerationer via programmering med hjälp av en Azure Resource Manager-mall (ARM-mall). Azure-kunder med ett faktureringskonto för följande avtalstyper kan skapa prenumerationer med hjälp av en ARM-mall:

- Enterprise-avtal (EA)
    - Mer information finns i [Azure Enterprise REST-API:er](ea-portal-rest-apis.md)
- Microsoft-kundavtal (MCA)
    - [REST API för Azure-fakturering](/rest/api/billing)
    - [Prenumerations-API:er för Azure](/rest/api/subscription)
- Microsoft-partneravtal (MPA)
    - [REST API för Azure-fakturering](/rest/api/billing)
    - [Prenumerations-API:er för Azure](/rest/api/subscription)

När du skapar en Azure-prenumeration programmatiskt med hjälp av en mall styrs prenumerationen av det avtal som du fick när du erhöll Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [Juridisk information för Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-using-arm-templates"></a>Skapa prenumerationer med hjälp av ARM-mallar

Du kan skapa prenumerationer med en Azure Resource Manager-mall (ARM-mall) för att automatisera distributionsprocesserna för produktions-/testmiljön. I följande exempel använder du en ARM-mall för att skapa en Azure-prenumeration och en Azure-resursgrupp.

## <a name="prerequisites"></a>Krav

Du måste ha en av följande roller för att skapa prenumerationer: 

- Azure-prenumerationsägare på ett fakturaavsnitt
- Azure-prenumerationsdeltagare på ett fakturaavsnitt
- Azure-prenumerationsskapare på ett fakturaavsnitt
- Azure-prenumerationsägare på en faktureringsprofil eller ett faktureringskonto
- Rollen Azure-prenumerationsdeltagare på en faktureringsprofil eller ett faktureringskonto

Mer information finns i [Roller och uppgifter för prenumerationsfakturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Eftersom du utför en ARM-malldistribution måste du ha skrivbehörighet för rotobjektet. För att kunna skapa ARM-distributionen under en hanteringsgrupp måste du ha skrivbehörighet för hanteringsgruppen. Åtgärden är endast till för att skapa en ARM-distribution. Om en prenumeration skapas, skapas den bara i den hanteringsgrupp som anges i ARM-mallen.

I följande exempel används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Sök efter faktureringskonton som du har åtkomst till

Visa en lista över alla faktureringskonton genom att göra följande begäran.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API-svaret visar alla faktureringskonton som du har åtkomst till.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Använd egenskapen `displayName` för att identifiera det faktureringskonto som du vill skapa prenumerationer för. Se till att agreementType för kontot är *MicrosoftCustomerAgreement*. Kopiera `name` för kontot. Om du till exempel vill skapa en prenumeration för faktureringskontot `Contoso` kopierar du `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in värdet någonstans så att du kan använda det i nästa steg.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Hitta faktureringsprofiler och fakturaavsnitt för att skapa prenumerationer

Avgifterna för din prenumeration visas i ett avsnitt på fakturan för en faktureringsprofil. Använd följande API för att hämta listan över faktureringsprofiler och fakturaavsnitt som du har behörighet att skapa Azure-prenumerationer för.

Först visas listan över faktureringsprofiler under det faktureringskonto som du har åtkomst till.

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```

API-svaret visar alla faktureringsprofiler som du har behörighet att skapa prenumerationer för:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Kopiera `id` för att identifiera fakturaavsnitten under faktureringsprofilen. Kopiera till exempel `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` och anropa följande API.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```

### <a name="response"></a>Svarsåtgärder

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Använd egenskapen `id` för att identifiera det fakturaavsnitt som du vill skapa prenumerationer för. Kopiera hela strängen. Exempelvis `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-and-resource-group-with-a-template"></a>Skapa en prenumeration och resursgrupp med en mall

Följande ARM-mall skapar en prenumeration med namnet *Dev Team Subscription* (Prenumeration för utvecklingsteam) för fakturaavsnittet *Development* (Utveckling). Prenumerationen debiteras till faktureringsprofilen *Contoso Billing Profile* (Faktureringsprofil för Contoso) och visas i avsnittet *Development* (Utveckling) på fakturan. Du använder det kopierade faktureringsomfånget från föregående steg: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="request"></a>Förfrågan

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="request-body"></a>Begärandetext

```json
{
  "properties":
    {
      "location": "westus",
      "properties": {
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {
            "uniqueAliasName": "sampleAlias"
          },
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "name": "sampleTemplate",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "variables": {
                    "uniqueAliasName": "sampleAlias"
                  },
                  "resources": [
                    {
                      "name": "[variables('uniqueAliasName')]",
                      "type": "Microsoft.Subscription/aliases",
                      "apiVersion": "2020-09-01",
                      "properties": {
                        "workLoad": "Production",
                        "displayName": "Dev Team subscription",
                        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                      },
                      "dependsOn": [],
                      "tags": {}
                    }
                  ],
                  "outputs": {
                    "subscriptionId": {
                      "type": "string",
                      "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                    }
                  }
                }
              }
            },
            {
              "name": "sampleOuterResource",
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "parameters": {
                  "subscriptionId": {
                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                  }
                },
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
                  "parameters": {
                    "subscriptionId": {
                      "type": "string"
                    }
                  },
                  "variables": {},
                  "resources": [
                    {
                      "name": "sampleInnerResource",
                      "type": "Microsoft.Resources/deployments",
                      "subscriptionId": "[parameters('subscriptionId')]",
                      "apiVersion": "2019-10-01",
                      "location": "westus",
                      "properties": {
                        "expressionEvaluationOptions": {
                          "scope": "inner"
                        },
                        "mode": "Incremental",
                        "parameters": {},
                        "template": {
                          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                          "contentVersion": "1.0.0.0",
                          "parameters": {},
                          "variables": {},
                          "resources": [
                            {
                              "type": "Microsoft.Resources/resourceGroups",
                              "apiVersion": "2020-05-01",
                              "location": "[deployment().location]",
                              "name": "sampleRG",
                              "properties": {},
                              "tags": {}
                            }
                          ],
                          "outputs": {}
                        }
                      }
                    }
                  ],
                  "outputs": {}
                }
              }
            }
          ],
          "outputs": {
            "messageFromLinkedTemplate": {
              "type": "string",
              "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
            }
          }
        },
        "mode": "Incremental"
      }
    }
}
```

### <a name="response"></a>Svarsåtgärder

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

Du kan övervaka förloppet genom att göra en GET-begäran för att hämta statusen för distributionen.

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="response"></a>Svarsåtgärder

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

I föregående exempel ser du att prenumerationen som skapades var `16edf959-11fd-48bb-9a46-85190963ead9` och resursgruppen som skapades var `sampleRG`.

## <a name="next-steps"></a>Nästa steg

* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md).
