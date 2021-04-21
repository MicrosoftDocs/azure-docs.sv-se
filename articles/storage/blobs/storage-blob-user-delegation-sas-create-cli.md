---
title: Använda Azure CLI för att skapa en SAS för användardelegering för en container eller blob
titleSuffix: Azure Storage
description: Lär dig hur du skapar en SAS för användardelegering med Azure Active Directory autentiseringsuppgifter med hjälp av Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788555"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Skapa en SAS för användardelegering för en container eller blob med Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory autentiseringsuppgifter (Azure AD) för att skapa en SAS för användardelegering för en container eller blob med Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installera den senaste versionen av Azure CLI

Om du vill använda Azure CLI för att skydda en SAS med Azure AD-autentiseringsuppgifter kontrollerar du först att du har installerat den senaste versionen av Azure CLI. Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Om du vill skapa en SAS för användardelegering med hjälp av Azure CLI kontrollerar du att du har installerat version 2.0.78 eller senare. Kontrollera den installerade versionen med hjälp av `az --version` kommandot .

## <a name="sign-in-with-azure-ad-credentials"></a>Logga in med Azure AD-autentiseringsuppgifter

Logga in på Azure CLI med dina autentiseringsuppgifter för Azure AD. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Tilldela behörigheter med Azure RBAC

Om du vill skapa en SAS för användardelegering från Azure PowerShell måste Azure AD-kontot som används för att logga in på Azure CLI tilldelas en roll som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Den här behörigheten gör att Azure AD-kontot kan begära nyckeln *för användardelegering.* Nyckeln för användardelegering används för att signera SAS för användardelegering. Rollen som tillhandahåller **åtgärden Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på nivån för lagringskontot, resursgruppen eller prenumerationen.

Om du inte har tillräcklig behörighet för att tilldela Azure-roller till ett Azure AD-säkerhetsobjekt kan du behöva be kontoägaren eller administratören att tilldela nödvändiga behörigheter.

I följande exempel tilldelas **rollen Storage Blob Data-deltagare,** som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Rollen är begränsad på lagringskontots nivå.

Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda rollerna som omfattar **åtgärden Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** finns i [Inbyggda roller](../../role-based-access-control/built-in-roles.md)i Azure.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använda Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en SAS för användardelegering med Azure CLI skapas den användardelegeringsnyckel som används för att signera SAS implicit åt dig. Starttiden och förfallotiden som du anger för SAS används också som starttid och förfallotid för användardelegeringsnyckeln.

Eftersom det maximala intervallet som nyckeln för användardelegering är giltig är 7 dagar från startdatumet, bör du ange en förfallotid för SAS som är inom 7 dagar från starttiden. SAS är ogiltig när nyckeln för användardelegering upphör att gälla, så en SAS med en förfallotid som är längre än 7 dagar är fortfarande endast giltig i 7 dagar.

När du skapar en SAS för användardelegering `--auth-mode login` krävs `--as-user parameters` och . Ange *inloggning* för `--auth-mode` parametern så att begäranden som görs till Azure Storage auktoriserats med dina Autentiseringsuppgifter för Azure AD. Ange `--as-user` parametern för att ange att den SAS som returneras ska vara en SAS för användardelegering.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en SAS för användardelegering för en container

Om du vill skapa en SAS för användardelegering för en container med Azure CLI anropar du [kommandot az storage container generate-sas.](/cli/azure/storage/container#az_storage_container_generate_sas)

Behörigheter som stöds för en SAS för användardelegering på en container är bland annat Lägg till, Skapa, Ta bort, Lista, Läsa och Skriva. Behörigheter kan anges separat eller kombineras. Mer information om dessa behörigheter finns i Skapa en [SAS för användardelegering.](/rest/api/storageservices/create-user-delegation-sas)

I följande exempel returneras en SAS-token för användardelegering för en container. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

SAS-token för användardelegering returneras ungefär så här:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en SAS för användardelegering för en blob

Om du vill skapa en SAS för användardelegering för en blob med Azure CLI anropar du [kommandot az storage blob generate-sas.](/cli/azure/storage/blob#az_storage_blob_generate_sas)

Behörigheter som stöds för en SAS för användardelegering på en blob är bland annat Lägg till, Skapa, Ta bort, Läsa och Skriva. Behörigheter kan anges var för sig eller kombineras. Mer information om dessa behörigheter finns i Skapa en SAS [för användardelegering.](/rest/api/storageservices/create-user-delegation-sas)

Följande syntax returnerar en SAS för användardelegering för en blob. Exemplet anger parametern `--full-uri` , som returnerar blob-URI:en med SAS-token bifogad. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

SAS-URI:et för användardelegering returneras ungefär så här:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> En SAS för användardelegering stöder inte definition av behörigheter med en lagrad åtkomstprincip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en SAS för användardelegering

Om du vill återkalla en SAS för användardelegering från Azure CLI anropar du [kommandot az storage account revoke-delegation-keys.](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) Det här kommandot återkallar alla de användardelegeringsnycklar som är associerade med det angivna lagringskontot. Signaturer för delad åtkomst som är associerade med dessa nycklar ogiltigförklaras.

Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Både användardelegeringsnyckeln och Azure-rolltilldelningarna cachelagras av Azure Storage, så det kan uppstå en fördröjning mellan när du initierar återkallningsprocessen och när en befintlig SAS för användardelegering blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SAS för användardelegering (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta nyckelåtgärd för användardelegering](/rest/api/storageservices/get-user-delegation-key)
