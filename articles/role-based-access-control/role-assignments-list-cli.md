---
title: Visa en lista med Azures roll tilldelningar med Azure CLI – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av Azure CLI och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556922"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Visa en lista med Azures roll tilldelningar med Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure CLI.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="prerequisites"></a>Förutsättningar

- [Bash i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa roll tilldelningarna för en speciell användare använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Som standard visas endast roll tilldelningar för den aktuella prenumerationen. Lägg till-parametern för att Visa roll tilldelningar för den aktuella prenumerationen och nedan `--all` . Om du vill visa ärvda roll tilldelningar lägger du till `--include-inherited` parametern.

I följande exempel visas roll tilldelningarna som tilldelas direkt till *patlong \@ contoso.com* -användaren:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa en lista över roll tilldelningar som finns i ett resurs grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

I följande exempel visas roll tilldelningarna för resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

Om du vill visa en lista över alla roll tilldelningar i ett prenumerations omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta prenumerations-ID: t kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [konto listan AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Exempel:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Lista roll tilldelningar för en hanterings grupp

Om du vill visa alla roll tilldelningar i ett hanterings grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Exempel:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista roll tilldelningar för en hanterad identitet

1. Hämta ägar-ID: t för den systemtilldelade eller användarspecifika hanterade identiteten.

    Om du vill hämta ägar-ID: t för en användardefinierad hanterad identitet kan du använda [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) eller [AZ Identity List](/cli/azure/identity#az-identity-list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Om du vill hämta ägar-ID för en systemtilldelad hanterad identitet kan du använda [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Om du vill visa roll tilldelningarna använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list).

    Som standard visas endast roll tilldelningar för den aktuella prenumerationen. Lägg till-parametern för att Visa roll tilldelningar för den aktuella prenumerationen och nedan `--all` . Om du vill visa ärvda roll tilldelningar lägger du till `--include-inherited` parametern.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-roller med Azure CLI](role-assignments-cli.md)