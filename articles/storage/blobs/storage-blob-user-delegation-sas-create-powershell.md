---
title: Använd PowerShell för att skapa en användar Delegerings-SAS för en behållare eller BLOB
titleSuffix: Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS med Azure Active Directory autentiseringsuppgifter med hjälp av PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714522"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Om du vill skapa en användar Delegerings-SAS med PowerShell installerar du version 1.10.0 eller senare av modulen AZ. Storage. Följ de här stegen för att installera den senaste versionen av modulen:

1. Avinstallera tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med hjälp av inställningen **appar & funktioner** under **Inställningar**.
    - Ta bort alla **Azure** -moduler från `%Program Files%\WindowsPowerShell\Modules` .

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen när du har installerat PowerShellGet.

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Kontrol lera att du har installerat Azure PowerShell version 3.2.0 eller senare. Kör följande kommando för att installera den senaste versionen av Azure Storage PowerShell-modulen:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen.

Kör följande kommando för att kontrol lera vilken version av modulen AZ. Storage som är installerad:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Logga in på Azure PowerShell med Azure AD

Anropa kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in med ditt Azure AD-konto:

```powershell
Connect-AzAccount
```

Mer information om hur du loggar in med PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Tilldela behörigheter med Azure RBAC

Om du vill skapa en användar Delegerings-SAS från Azure PowerShell måste det Azure AD-konto som används för att logga in på PowerShell tilldelas en roll som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Med den här behörigheten kan Azure AD-kontot begära *användar Delegerings nyckeln*. Användar Delegerings nyckeln används för att signera användar Delegerings-SAS. Rollen som tillhandahåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på lagrings kontots nivå, resurs gruppen eller prenumerationen. Mer information om Azure RBAC-behörigheter för att skapa en användar Delegerings-SAS finns i avsnittet **tilldela behörigheter med Azure RBAC** i [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

Om du inte har tillräcklig behörighet för att tilldela Azure-roller till ett säkerhets objekt i Azure AD kan du behöva be kontots ägare eller administratör att tilldela de nödvändiga behörigheterna.

I följande exempel tilldelas rollen **Storage BLOB data Contributor** , som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Rollen är begränsad på lagrings kontots nivå.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda roller som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använd Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en användar Delegerings-SAS med Azure PowerShell skapas den användar Delegerings nyckel som används för att signera SAS för implicit. Start tiden och den förfallo tid som du anger för SAS används också som start tid och förfallo tid för användar Delegerings nyckeln. 

Eftersom det maximala intervallet med vilken användar Delegerings nyckeln är giltigt är 7 dagar från start datumet, bör du ange en förfallo tid för SAS som ligger inom 7 dagar från start tiden. SAS är ogiltig när användar Delegerings nyckeln upphör att gälla, så en SAS med en förfallo tid på mer än 7 dagar är fortfarande bara giltig i 7 dagar.

Skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure PowerShell genom att först skapa ett nytt Azure Storage-kontext objekt och ange `-UseConnectedAccount` parametern. `-UseConnectedAccount`Parametern anger att kontext objekt skapas under det Azure AD-konto som du loggade in med.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en användar Delegerings-SAS för en behållare

Om du vill returnera en användar delegering SAS-token för en behållare anropar du kommandot [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) och skickar det Azure Storage-kontext objekt som du skapade tidigare.

I följande exempel returneras en användar Delegerings-SAS-token för en behållare. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Den användardefinierade SAS-token som returnerades ser ut ungefär så här:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en användar Delegerings-SAS för en BLOB

Om du vill returnera en användar delegering SAS-token för en BLOB anropar du kommandot [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) och skickar det Azure Storage-kontext objekt som du skapade tidigare.

Följande syntax returnerar en användar Delegerings-SAS för en blob. I exemplet anges `-FullUri` parametern, som returnerar BLOB-URI: n med den SAS-token som lagts till. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Den användardefinierade SAS-URI: n som returnerades ser ut ungefär så här:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> En användar Delegerings-SAS har inte stöd för att definiera behörigheter med en lagrad åtkomst princip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en användar Delegerings-SAS

Om du vill återkalla en användar Delegerings-SAS från Azure PowerShell anropar du kommandot **REVOKE-AzStorageAccountUserDelegationKeys** . Det här kommandot återkallar alla de användar Delegerings nycklar som är kopplade till det angivna lagrings kontot. Alla signaturer för delad åtkomst som är kopplade till nycklarna är ogiltiga.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Både användar Delegerings nyckeln och roll tilldelningarna i Azure cachelagras av Azure Storage, så det kan uppstå en fördröjning mellan när du initierar återkallnings processen och när en befintlig användar Delegerings-SÄKERHETSASSOCIATIONER blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta åtgärd för användar Delegerings nyckel](/rest/api/storageservices/get-user-delegation-key)
