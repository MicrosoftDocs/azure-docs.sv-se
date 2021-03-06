---
title: Skapa prenumerationer på Azure Enterprise-avtal via programmering med de senaste API:erna
description: Lär dig hur du skapar prenumerationer på Azure Enterprise-avtal via programmering med de senaste versionerna av REST API, Azure CLI och Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 4de89892d27bb811be6670c1a14ca85859342ecc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218918"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Skapa prenumerationer på Azure Enterprise-avtal via programmering med de senaste API:erna

Den här artikeln hjälper dig att skapa prenumerationer för Azure Enterprise-avtal (EA) via programmering för ett EA-faktureringskonto med de senaste API-versionerna. Om du fortfarande använder den äldre förhandsversionen läser du [Skapa Azure-prenumerationer via programmering med förhandsversioner av API:er](programmatically-create-subscription-preview.md). 

I den här artikeln får du lära dig hur du skapar prenumerationer programmatiskt med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration programmatiskt styrs prenumerationen av det avtal som du fick när du erhöll Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [Juridisk information för Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna skapa en prenumeration måste du ha en ägarroll för ett registreringskonto. Det finns två sätt att få denna roll:

* Företagsadministratören för din registrering kan [utse dig till kontoägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs), vilket innebär att du blir ägare till registreringskontot.
* En befintlig ägare till registreringskontot kan [ge dig åtkomst](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Om du vill skapa en EA-prenumeration med hjälp av ett tjänsthuvudnamn måste du på liknande sätt [bevilja tjänsthuvudnamnet behörighet att skapa prenumerationer](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > [!NOTE]
  > Se till att du använder rätt API-version för att ge registreringskontot ägarbehörighet. För den här artikeln och för de API:er som dokumenteras i den använder du API:et [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Om du migrerar till användning av de nyare API:erna måste du bevilja ägarbehörighet igen med [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Din tidigare konfiguration som skapades med [version 2015-07-01](grant-access-to-create-subscription.md) konverteras inte automatiskt för användning med nyare API:er.

## <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till ett registreringskonto som är associerat med en kontoägare använder Azure konto-till-registrering-relationen för att ta reda på var prenumerationsavgifterna ska debiteras. Alla prenumerationer som skapats under kontot debiteras till den EA-registrering som kontot ingår i. För att kunna skapa prenumerationer måste du ange värden om registreringskontot och användarens huvudnamn så att du är ägare för prenumerationen.

För att kunna köra följande kommandon måste du vara inloggad på kontoägarens *hemkatalog*, vilket är den katalog som prenumerationer skapas i som standard.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

Begära en lista över alla registreringskonton som du har åtkomst till:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API-svaret visar alla registreringskonton som du har åtkomst till:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Värdet för ett faktureringsomfång och `id` är samma sak. `id` för ditt registreringskonto är det faktureringsomfång som prenumerationsbegäran initieras i. Det är viktigt att känna till ID:t eftersom det är en obligatorisk parameter som du ska använda senare i artikeln för att skapa en prenumeration.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getEnrollments)

Begära en lista över alla registreringskonton som du har åtkomst till:

```azurecli-interactive
> az billing account list
```

Svar visar alla registrerings konton som du har åtkomst till

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```
Värdet för ett faktureringsomfång och `id` är samma sak. `id` för ditt registreringskonto är det faktureringsomfång som prenumerationsbegäran initieras i. Det är viktigt att känna till ID:t eftersom det är en obligatorisk parameter som du ska använda senare i artikeln för att skapa en prenumeration.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett specifikt registreringskonto

I följande exempel skapas en prenumeration med namnet *Dev Team Subscription* i det registreringskonto som valdes i föregående steg. 

### <a name="rest"></a>[REST](#tab/rest-EA)

Anropa PUT-API:et för att skapa ett alias/en begäran om att skapa en prenumeration.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

I begärandetexten anger du `id` från ett av dina `enrollmentAccounts` som `billingScope`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Tillåtna värden för `Workload` är `Production` och `DevTest`.

#### <a name="response"></a>Svarsåtgärder

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Kör `Install-Module Az.Subscription` för att installera den senaste versionen av modulen som innehåller cmdleten `New-AzSubscriptionAlias`. Se [Hämta PowerShellGet-modul](/powershell/scripting/gallery/installing-psget) om du vill installera en nyare version av PowerShellGet.

Kör följande [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-kommando med faktureringsomfånget `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

subscriptionId returneras som en del av svaret från kommandot.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

Installera först tillägget genom att köra `az extension add --name account` och `az extension add --name alias`.

Kör följande [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create)-kommando och ange `billing-scope` och `id` från ett av dina `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

subscriptionId returneras som en del av svaret från kommandot.

```azurecli
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

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar för API:et för att skapa Azure Enterprise-prenumerationer

- Endast Azure Enterprise-prenumerationer skapas med API:et.
- Det finns en gräns på 2 000 prenumerationer per registreringskonto. Därefter kan du bara skapa fler prenumerationer för kontot i Azure-portalen. Om du vill skapa fler prenumerationer med hjälp av API:et skapar du ett till registreringskonto. Avbrutna, borttagna och överförda prenumerationer tas med i beräkningen för gränsen på 2000.
- Användare som inte är kontoägare, men som har lagts till i ett registreringskonto via Azure RBAC, kan inte skapa prenumerationer i Azure-portalen.
- Du kan inte välja den klientorganisation som prenumerationen ska skapas i. Prenumerationen skapas alltid i kontoägarens hemklientorganisation. Information om hur du flyttar prenumerationen till en annan klientorganisation finns i avsnittet om att [ändra klientorganisation för prenumerationer](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Nästa steg

* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md).
