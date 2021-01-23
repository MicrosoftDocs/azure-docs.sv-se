---
title: Använd Azure CLI för att skapa en användar Delegerings-SAS för en behållare eller BLOB
titleSuffix: Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS med Azure Active Directory autentiseringsuppgifter med hjälp av Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 536cd01fbcf2c5d18a8c12030b709427d9bb91b1
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703614"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installera den senaste versionen av Azure CLI

Kontrol lera att du har installerat den senaste versionen av Azure CLI för att använda Azure CLI för att skydda SAS med autentiseringsuppgifter för Azure AD. Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill skapa en användar Delegerings-SAS med Azure CLI kontrollerar du att du har installerat version 2.0.78 eller senare. Använd kommandot för att kontrol lera den installerade versionen `az --version` .

## <a name="sign-in-with-azure-ad-credentials"></a>Logga in med autentiseringsuppgifter för Azure AD

Logga in på Azure CLI med dina autentiseringsuppgifter för Azure AD. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Tilldela behörigheter med Azure RBAC

Om du vill skapa en användar Delegerings-SAS från Azure PowerShell måste det Azure AD-konto som används för att logga in på Azure CLI tilldelas en roll som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Med den här behörigheten kan Azure AD-kontot begära *användar Delegerings nyckeln*. Användar Delegerings nyckeln används för att signera användar Delegerings-SAS. Rollen som tillhandahåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på lagrings kontots nivå, resurs gruppen eller prenumerationen.

Om du inte har tillräcklig behörighet för att tilldela Azure-roller till ett säkerhets objekt i Azure AD kan du behöva be kontots ägare eller administratör att tilldela de nödvändiga behörigheterna.

I följande exempel tilldelas rollen **Storage BLOB data Contributor** , som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Rollen är begränsad på lagrings kontots nivå.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda roller som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använd Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en användar Delegerings-SAS med Azure CLI skapas den användar Delegerings nyckel som används för att signera SAS för dig implicit. Start tiden och den förfallo tid som du anger för SAS används också som start tid och förfallo tid för användar Delegerings nyckeln.

Eftersom det maximala intervallet med vilken användar Delegerings nyckeln är giltigt är 7 dagar från start datumet, bör du ange en förfallo tid för SAS som ligger inom 7 dagar från start tiden. SAS är ogiltig när användar Delegerings nyckeln upphör att gälla, så en SAS med en förfallo tid på mer än 7 dagar är fortfarande bara giltig i 7 dagar.

När du skapar en användar Delegerings-SAS `--auth-mode login` `--as-user parameters` krävs och. Ange *inloggning* för `--auth-mode` parametern så att begär Anden som görs till Azure Storage auktoriseras med dina AUTENTISERINGSUPPGIFTER för Azure AD. Ange `--as-user` parametern för att indikera att den returnerade SAS: en är en användar Delegerings-SAS.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en användar Delegerings-SAS för en behållare

Om du vill skapa en användar Delegerings-SAS för en behållare med Azure CLI anropar du kommandot [AZ Storage container generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

Behörigheter som stöds för en användar Delegerings-SAS i en behållare inkluderar Lägg till, skapa, ta bort, lista, läsa och skriva. Behörigheter kan anges med enkel eller kombinerad. Mer information om dessa behörigheter finns i [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

I följande exempel returneras en användar Delegerings-SAS-token för en behållare. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Den användardefinierade SAS-token som returnerades ser ut ungefär så här:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en användar Delegerings-SAS för en BLOB

Om du vill skapa en användar Delegerings-SAS för en blob med Azure CLI anropar du kommandot [AZ Storage BLOB generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

Behörigheter som stöds för en användar Delegerings-SAS på en BLOB inkluderar Lägg till, skapa, ta bort, läsa och skriva. Behörigheter kan anges med enkel eller kombinerad. Mer information om dessa behörigheter finns i [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

Följande syntax returnerar en användar Delegerings-SAS för en blob. I exemplet anges `--full-uri` parametern, som returnerar BLOB-URI: n med den SAS-token som lagts till. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Den användardefinierade SAS-URI: n som returnerades ser ut ungefär så här:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> En användar Delegerings-SAS har inte stöd för att definiera behörigheter med en lagrad åtkomst princip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en användar Delegerings-SAS

Om du vill återkalla en användar Delegerings-SAS från Azure CLI anropar du kommandot [AZ Storage Account REVOKE-delegering-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) . Det här kommandot återkallar alla de användar Delegerings nycklar som är kopplade till det angivna lagrings kontot. Alla signaturer för delad åtkomst som är kopplade till nycklarna är ogiltiga.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Både användar Delegerings nyckeln och roll tilldelningarna i Azure cachelagras av Azure Storage, så det kan uppstå en fördröjning mellan när du initierar återkallnings processen och när en befintlig användar Delegerings-SÄKERHETSASSOCIATIONER blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta åtgärd för användar Delegerings nyckel](/rest/api/storageservices/get-user-delegation-key)
