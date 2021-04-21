---
title: Använda Azure CLI för att tilldela en Azure-roll för dataåtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure CLI för att tilldela behörigheter Azure Active Directory ett säkerhetsobjekt med rollbaserad åtkomstkontroll i Azure (Azure RBAC). Azure Storage har stöd för inbyggda och anpassade Azure-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785333"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Använda Azure CLI för att tilldela en Azure-roll för åtkomst till blob- och ködata

Azure Active Directory (Azure AD) auktoriserar åtkomstbehörigheter till skyddade resurser via rollbaserad åtkomstkontroll [i Azure (Azure RBAC).](../../role-based-access-control/overview.md) Azure Storage definierar en uppsättning inbyggda Roller i Azure som omfattar vanliga uppsättningar med behörigheter som används för åtkomst till blob- eller ködata.

När en Azure-roll tilldelas till ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjekt. Åtkomsten kan vara begränsad till prenumerationsnivån, resursgruppen, lagringskontot eller en enskild container eller kö. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett huvudnamn för programtjänsten eller en [hanterad identitet för Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md)

I den här artikeln beskrivs hur du använder Azure CLI för att visa en lista över inbyggda roller i Azure och tilldela dem till användare. Mer information om hur du använder Azure CLI finns [i Azure Command-Line Interface (CLI).](/cli/azure)

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-roller för blobar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Fastställa resursomfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Lista tillgängliga Azure-roller

Om du vill visa en lista över tillgängliga inbyggda Azure-roller med Azure CLI använder du kommandot [az role definition list:](/cli/azure/role/definition#az_role_definition_list)

```azurecli-interactive
az role definition list --out table
```

De inbyggda datarollerna Azure Storage visas tillsammans med andra inbyggda roller för Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Tilldela en Azure-roll till ett säkerhetsobjekt

Om du vill tilldela en Azure-roll till ett säkerhetsobjekt använder du [kommandot az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create) Formatet för kommandot kan variera beroende på tilldelningens omfång. I följande exempel visas hur du tilldelar en roll till en användare i olika omfång, men du kan använda samma kommando för att tilldela en roll till ett säkerhetsobjekt.

> [!IMPORTANT]
> När du skapar ett Azure Storage-konto tilldelas du inte automatiskt behörighet att komma åt data via Azure AD. Du måste uttryckligen tilldela dig själv en Azure RBAC-roll för dataåtkomst. Du kan tilldela den på nivån för din prenumeration, resursgrupp, lagringskonto eller container eller kö.
>
> Om lagringskontot är låst med ett Azure Resource Manager skrivskyddat lås förhindrar låset tilldelningen av Azure RBAC-roller som är begränsade till lagringskontot eller till en datacontainer (blobcontainer eller kö).

### <a name="container-scope"></a>Containeromfång

Om du vill tilldela en roll som är begränsad till en container anger du en sträng som innehåller containerns omfång för `--scope` parametern . Omfånget för en container är i följande format:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

I följande exempel tilldelas **rollen Storage Blob Data-deltagare** till en användare, begränsad till containernivån. Ersätt exempelvärdena och platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Köomfång

Om du vill tilldela en roll som är begränsad till en kö anger du en sträng som innehåller omfånget för kön för `--scope` parametern . Omfånget för en kö är i följande format:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

I följande exempel tilldelas **rollen Storage Queue Data Contributor** till en användare, begränsad till köns nivå. Ersätt exempelvärdena och platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Omfång för lagringskonto

Om du vill tilldela en roll som är begränsad till lagringskontot anger du omfånget för lagringskontoresursen för `--scope` parametern . Omfånget för ett lagringskonto är i följande format:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visas hur du tilldelar **rollen Storage Blob Data Reader** till en användare på lagringskontots nivå. Ersätt exempelvärdena med dina egna värden: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Omfånget för resursgrupp

Om du vill tilldela en roll som är begränsad till resursgruppen anger du resursgruppens namn eller ID för `--resource-group` parametern. I följande exempel tilldelas **rollen Storage Queue Data Reader** till en användare på resursgruppens nivå. Ersätt exempelvärdena och platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Prenumerationsomfång

Om du vill tilldela en roll som är begränsad till prenumerationen anger du omfånget för prenumerationen för `--scope` parametern . Omfånget för en prenumeration är i följande format:

```
/subscriptions/<subscription>
```

I följande exempel visas hur du tilldelar **rollen Storage Blob Data Reader** till en användare på lagringskontots nivå. Ersätt exempelvärdena med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort Azure-rolltilldelningar med Azure PowerShell modulen](../../role-based-access-control/role-assignments-powershell.md)
- [Använd modulen Azure PowerShell för att tilldela en Azure-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-powershell.md)
- [Använda Azure-portalen för tilldelning av en Azure-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-portal.md)
