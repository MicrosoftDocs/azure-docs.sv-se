---
title: Bevilja behörighet att skapa Azure Enterprise-prenumerationer
description: Lär dig hur du ger en användare eller ett tjänsthuvudnamn möjlighet att skapa Azure Enterprise-prenumerationer programmatiskt.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: andalmia
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: banders
ms.openlocfilehash: 039e728f6518d21ddfb9c7c359a6cf2ec743f232
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185112"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)

Som Azure-kund med [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) kan du ge en annan användare eller ett tjänsthuvudnamn behörighet att skapa prenumerationer som debiteras ditt konto. I den här artikeln får du lära dig hur du använder [rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) för att dela möjligheten att skapa prenumerationer och hur du granskar skapande av prenumerationer. Du måste ha ägarrollen för det konto som du vill dela.

> [!NOTE]
> Detta API fungerar bara med [förhandsversions-API:er för prenumerationsskapande](programmatically-create-subscription-preview.md). Om du vill använda [den allmänt tillgängliga versionen](programmatically-create-subscription-enterprise-agreement.md) använder du den senaste API-versionen på [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Om du migrerar till användning av de nyare API:erna måste du bevilja ägarbehörigheter igen med [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Din tidigare konfiguration som använder följande API:er konverteras inte automatiskt för användning med nyare API:er.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Bevilja åtkomst

För att kunna [skapa prenumerationer under ett registreringskonto](programmatically-create-subscription-enterprise-agreement.md) måste användarna ha rollen [Azure RBAC-ägare](../../role-based-access-control/built-in-roles.md#owner) för det kontot. Så här ger du en användare eller en grupp av användare rollen Azure RBAC-ägare för ett registreringskonto:

1. Hämta objekt-ID:t för det registreringskonto som du vill bevilja åtkomst till

    Om du vill ge andra rollen Azure RBAC-ägare för ett registreringskonto måste du vara antingen kontoägare eller Azure RBAC-ägare för kontot.

    # <a name="rest"></a>[REST](#tab/rest)

    Begära en lista över alla registreringskonton som du har åtkomst till:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure svarar med en lista över alla registreringskonton som du har åtkomst till:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Använd egenskapen `principalName` för att identifiera det konto som du vill bevilja Azure RBAC-ägaråtkomst till. Kopiera `name` för det kontot. Om du till exempel vill bevilja Azure RBAC-ägaråtkomst till registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att visa en lista över alla registreringskonton som du har åtkomst till. Välj **Prova** för att öppna [Azure Cloud Shell](https://shell.azure.com/). Du klistrar in koden genom att högerklicka på Shell-fönstren och välja **Klistra in**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure skickar ett svar med en lista över de registreringskonton som du har åtkomst till:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Använd egenskapen `principalName` för att identifiera det konto som du vill bevilja Azure RBAC-ägaråtkomst till. Kopiera `ObjectId` för det kontot. Om du till exempel vill bevilja Azure RBAC-ägaråtkomst till registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Klistra in det här objekt-ID:t någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Använd kommandot [az billing enrollment-account list](/cli/azure/billing) för att visa en lista över alla registreringskonton som du har åtkomst till. Välj **Prova** för att öppna [Azure Cloud Shell](https://shell.azure.com/). Du klistrar in koden genom att högerklicka på Shell-fönstren och välja **Klistra in**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure skickar ett svar med en lista över de registreringskonton som du har åtkomst till:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Använd egenskapen `principalName` för att identifiera det konto som du vill bevilja Azure RBAC-ägaråtkomst till. Kopiera `name` för det kontot. Om du till exempel vill bevilja Azure RBAC-ägaråtkomst till registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

1. <a id="userObjectId"></a>Hämta objekt-ID för den användare eller grupp som du vill ge rollen Azure RBAC-ägare

    1. Sök på **Azure Active Directory** i Azure-portalen.
    1. Om du vill ge en användare åtkomst väljer du **Användare** på menyn till vänster. Om du vill ge åtkomst till en grupp väljer du **Grupper**.
    1. Välj den användare eller grupp som du vill ge rollen Azure RBAC-ägare.
    1. Om du har valt en användare hittar du objekt-ID:t på profilsidan. Om du har valt en grupp visas objekt-ID:t på översiktssidan. Kopiera **ObjectID** genom att välja ikonen till höger om textrutan. Klistra in det här värdet någonstans så att du kan använda det som `userObjectId` i nästa steg.

1. Bevilja användaren eller gruppen rollen Azure RBAC-ägare för registreringskontot

    Använd de värden som du samlade in i de första två stegen och ge användaren eller gruppen rollen Azure RBAC-ägare för registreringskontot.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Gör följande kommando och ersätt ```<enrollmentAccountObjectId>``` med värdet för `name` som du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med det objekt-ID som du kopierade från det andra steget.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    När ägarrollen har tilldelats för omfånget Registreringskonto svarar Azure med information om rolltilldelningen:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Kör följande [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md)-kommando och ersätt ```<enrollmentAccountObjectId>``` med `ObjectId` som samlades in i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med det objekt-ID som samlades in i det andra steget.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Kör följande [az role assignment create](../../role-based-access-control/role-assignments-cli.md)-kommando och ersätt ```<enrollmentAccountObjectId>``` med `name` som du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med det objekt-ID som samlades in i det andra steget.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    När användare blir Azure RBAC-ägare för ditt registreringskonto kan de [skapa prenumerationer programmatiskt](programmatically-create-subscription-enterprise-agreement.md) under kontot. När en prenumeration har skapats av en delegerad användare är den ursprungliga kontoägaren fortfarande tjänstadministratör, men som standard har även den delegerade användaren rollen Azure RBAC-ägare.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska vem som har skapat prenumerationer med hjälp av aktivitetsloggar

Om du vill spåra prenumerationer som har skapats via det här API:et använder du [API:et för klientaktivitetsloggen](/rest/api/monitor/tenantactivitylogs). Det är för närvarande inte möjligt att använda PowerShell, CLI eller Azure-portalen för att spåra skapande av prenumerationer.

1. Som innehavaradministratör av Azure AD-klienten kan du [höja åtkomsten](../../role-based-access-control/elevate-access-global-admin.md) och sedan tilldela en läsarroll till granskningsanvändaren inom omfånget `/providers/microsoft.insights/eventtypes/management`. Den här åtkomsten är tillgänglig i rollen [läsare](../../role-based-access-control/built-in-roles.md#reader), [övervakningsdeltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) eller en [anpassad roll](../../role-based-access-control/custom-roles.md).
1. Som granskningsanvändare anropar du [API:et för klientaktivitetsloggen](/rest/api/monitor/tenantactivitylogs) för att visa aktiviteter för skapande av prenumerationer. Exempel:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Du kan anropa det här API:et från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänsthuvudnamnet har behörighet att skapa en prenumeration kan du använda den identiteten för att [skapa Azure Enterprise-prenumerationer programmatiskt](programmatically-create-subscription-enterprise-agreement.md).
* Ett exempel på hur du skapar prenumerationer med .NET finns i [exempelkod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Mer information om Azure Resource Manager och dess API:er finns i [översikten över Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md)
* Omfattande metodtips angående prenumerationsåtgärder för stora företag finns i [Azure enterprise-kodskelett – förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance)