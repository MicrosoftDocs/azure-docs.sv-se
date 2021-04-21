---
title: Visa en lista över Azure-rolltilldelningar med Azure CLI – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvudnamn eller hanterade identiteter har åtkomst till med hjälp av Azure CLI och rollbaserad åtkomstkontroll i Azure (Azure RBAC).
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
ms.openlocfilehash: 2d30571b68ba7e38e9960d1e434cf7844f6be852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780109"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Visa en lista över Azure-rolltilldelningar med Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] I den här artikeln beskrivs hur du visar en lista över rolltilldelningar med hjälp av Azure CLI.

> [!NOTE]
> Om din organisation har outsourcad hanteringsfunktioner [](../lighthouse/concepts/azure-delegated-resource-management.md)till en tjänstleverantör som använder Azure-delegerad resurshantering visas inte rolltilldelningar som auktoriserats av den tjänstleverantören här.

## <a name="prerequisites"></a>Förutsättningar

- [Bash i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa en lista över rolltilldelningar för en specifik användare använder [du az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee {assignee}
```

Som standard visas endast rolltilldelningar för den aktuella prenumerationen. Om du vill visa rolltilldelningar för den aktuella prenumerationen och nedan lägger du till `--all` parametern . Om du vill visa ärvda rolltilldelningar lägger du till `--include-inherited` parametern .

I följande exempel visas de rolltilldelningar som tilldelas direkt till *den contoso.com \@* användaren:

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

Om du vill visa en lista över rolltilldelningar som finns i ett resursgruppsomfång använder [du az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

I följande exempel visas rolltilldelningarna för *resursgruppen för försäljning:*

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

Om du vill visa en lista över alla rolltilldelningar i ett prenumerationsomfång använder [du az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Du kan hämta prenumerations-ID:t på **bladet** Prenumerationer i Azure Portal du kan använda [az account list](/cli/azure/account#az_account_list).

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

## <a name="list-role-assignments-for-a-management-group"></a>Visa en lista över rolltilldelningar för en hanteringsgrupp

Om du vill visa en lista över alla rolltilldelningar i ett hanteringsgruppsomfång använder [du az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Om du vill hämta hanteringsgruppens ID  hittar du det på bladet Hanteringsgrupper i Azure Portal eller så kan du använda [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list).

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

## <a name="list-role-assignments-for-a-managed-identity"></a>Visa en lista över rolltilldelningar för en hanterad identitet

1. Hämta huvudnamns-ID:t för den system-tilldelade eller användar tilldelade hanterade identiteten.

    Om du vill hämta huvudnamns-ID:t för en användar tilldelad hanterad identitet kan du använda [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) eller az identity [list](/cli/azure/identity#az_identity_list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Om du vill hämta huvudnamns-ID:t för en system tilldelad hanterad identitet kan du använda [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Om du vill visa en lista över rolltilldelningarna använder [du az role assignment list](/cli/azure/role/assignment#az_role_assignment_list).

    Som standard visas endast rolltilldelningar för den aktuella prenumerationen. Om du vill visa rolltilldelningar för den aktuella prenumerationen och nedan lägger du till `--all` parametern . Om du vill visa ärvda rolltilldelningar lägger du till `--include-inherited` parametern .

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-roller med Hjälp av Azure CLI](role-assignments-cli.md)