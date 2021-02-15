---
title: Använd Azure CLI för att tilldela en Azure-roll för data åtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure CLI för att tilldela behörigheter till ett Azure Active Directory säkerhets objekt med rollbaserad åtkomst kontroll i Azure (Azure RBAC). Azure Storage stöder inbyggda och Azure-anpassade roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: c42061520b73966f2cd516716039d78c2b9cbeb8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375994"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Använd Azure CLI för att tilldela en Azure-roll för åtkomst till blob-och Queue-data

Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att få åtkomst till BLOB-eller Queue-data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, lagrings kontot eller en enskild behållare eller kö. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure CLI för att visa en lista över inbyggda Azure-roller och tilldela dem till användare. Mer information om hur du använder Azure CLI finns i [azure Command-Line Interface (CLI)](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resurs omfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Lista tillgängliga Azure-roller

Om du vill visa en lista över tillgängliga Azure inbyggda roller med Azure CLI använder du kommandot [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list) :

```azurecli-interactive
az role definition list --out table
```

Du ser de inbyggda Azure Storage data rollerna i listan, tillsammans med andra inbyggda roller för Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Tilldela en Azure-roll till ett säkerhets objekt

Om du vill tilldela en Azure-roll till ett säkerhets objekt använder du kommandot [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) . Kommandots format kan variera beroende på tilldelningens omfattning. I följande exempel visas hur du tilldelar en roll till en användare i olika scope, men du kan använda samma kommando för att tilldela en roll till alla säkerhets objekt.

> [!IMPORTANT]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data via Azure AD. Du måste uttryckligen tilldela en Azure RBAC-roll för data åtkomst. Du kan tilldela den på nivån för din prenumeration, resurs grupp, lagrings konto eller behållare eller kö.
>
> Om lagrings kontot är låst med ett Azure Resource Manager skrivskyddat lås, förhindrar låset tilldelningen av Azure RBAC-roller som är begränsade till lagrings kontot eller till en data behållare (BLOB container eller queue).

### <a name="container-scope"></a>Container omfång

Om du vill tilldela en roll begränsad till en behållare anger du en sträng som innehåller omfånget för `--scope` parametern. Omfånget för en behållare är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

I följande exempel tilldelas rollen **Storage BLOB data Contributor** till en användare som är begränsad till behållarens nivå. Se till att ersätta exempel värdena och plats hållarna inom hakparenteser med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Köa omfång

Om du vill tilldela en roll som är begränsad till en kö anger du en sträng som innehåller omfånget för- `--scope` parametern. Omfattningen för en kö är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

I följande exempel tilldelas rollen **Storage Queue data Contributor** till en användare som är begränsad till köns nivå. Se till att ersätta exempel värdena och plats hållarna inom hakparenteser med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Lagrings konto omfång

Om du vill tilldela en roll som är begränsad till lagrings kontot anger du omfånget för lagrings konto resursen för `--scope` parametern. Omfånget för ett lagrings konto har formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visas hur du tilldelar rollen **Storage BLOB data Reader** till en användare på lagrings kontots nivå. Se till att ersätta exempel värdena med dina egna värden: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Omfånget för resursgrupp

Om du vill tilldela en roll som är begränsad till resurs gruppen anger du resurs gruppens namn eller ID för `--resource-group` parametern. I följande exempel tilldelas rollen **data läsare för lagrings köer** till en användare på nivån i resurs gruppen. Ersätt värdena för exempel värden och plats hållare inom hak paren tes med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Prenumerations omfång

Om du vill tilldela en roll som är begränsad till prenumerationen anger du omfånget för `--scope` parameterns prenumeration. Omfånget för en prenumeration är i formatet:

```
/subscriptions/<subscription>
```

I följande exempel visas hur du tilldelar rollen **Storage BLOB data Reader** till en användare på lagrings kontots nivå. Se till att ersätta exempel värdena med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar i Azure med hjälp av modulen Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Använd Azure PowerShell-modulen för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-powershell.md)
- [Använda Azure-portalen för tilldelning av en Azure-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-portal.md)
