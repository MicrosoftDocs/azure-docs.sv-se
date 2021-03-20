---
title: Hantera användare som tilldelats hanterade identiteter med REST-Azure AD
description: Stegvisa instruktioner för hur du skapar, visar och tar bort en användardefinierad hanterad identitet för att göra REST API-anrop.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546700"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av REST API-anrop

Hanterade identiteter för Azure-resurser ger Azure-tjänster möjlighet att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av sväng för att göra REST API-anrop.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Du kan köra alla kommandon i den här artikeln antingen i molnet eller lokalt:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)för att köra i molnet.
    - Om du vill köra lokalt installerar du [sväng](https://curl.haxx.se/download.html) och [Azure CLI](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Hämta en åtkomst-token för ett innehav

1. Om du kör lokalt loggar du in på Azure via Azure CLI:

    ```
    az login
    ```

1. Hämta en åtkomsttoken med [AZ-konto get-Access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        |

**Brödtext i begäran**

|Name  |Beskrivning  |
|---------|---------|
|location     | Krävs. Resurs plats.        |

## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](../../role-based-access-control/built-in-roles.md#managed-identity-operator) eller [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

> [!NOTE]
> Om du tar bort en tilldelad hanterad identitet tas inte referensen bort från den resurs som den tilldelades. Om du vill ta bort en användardefinierad hanterad identitet från en virtuell dator med hjälp av vändning ser du [ta bort en användardefinierad identitet från en virtuell Azure-dator](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken.        |

## <a name="next-steps"></a>Nästa steg

Information om hur du tilldelar en användardefinierad hanterad identitet till en Azure VM/VMSS med hjälp av vändning finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av REST API samtal](qs-configure-rest-vm.md#user-assigned-managed-identity) och [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med hjälp av REST API-anrop](qs-configure-rest-vmss.md#user-assigned-managed-identity).
