---
title: Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-data
titleSuffix: Azure Storage
description: PowerShell har stöd för inloggning med Azure AD-autentiseringsuppgifter för att köra kommandon på BLOB-data i Azure Storage. En åtkomsttoken har angetts för sessionen och används för att auktorisera anrops åtgärder. Behörigheter beror på den Azure-roll som tilldelats Azure AD-säkerhetsobjektet.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: 45fa5cf4c76577cb5e8ba9bf482f4aab7301e3c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391498"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-data

Azure Storage innehåller tillägg för PowerShell som gör att du kan logga in och köra skript kommandon med Azure Active Directory (autentiseringsuppgifter för Azure AD). När du loggar in på PowerShell med autentiseringsuppgifter för Azure AD returneras en OAuth 2,0-åtkomsttoken. Denna token används automatiskt av PowerShell för att auktorisera efterföljande data åtgärder mot Blob Storage. För åtgärder som stöds behöver du inte längre skicka en konto nyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till BLOB-data till ett säkerhets objekt i Azure AD via rollbaserad åtkomst kontroll (Azure RBAC). Mer information om Azure-roller i Azure Storage finns i [Hantera åtkomst rättigheter för att Azure Storage data med Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Åtgärder som stöds

Azure Storage-tilläggen stöds för åtgärder på BLOB-data. Vilka åtgärder som kan anropas beror på vilka behörigheter som beviljats för det säkerhets objekt i Azure AD som du loggar in på PowerShell. Behörigheter till Azure Storage behållare tilldelas via Azure RBAC. Om du till exempel har tilldelats rollen **BLOB data Reader** kan du köra skript kommandon som läser data från en behållare. Om du har tilldelats rollen **BLOB data Contributor** kan du köra skript kommandon som läser, skriver eller tar bort en behållare eller de data som de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd i en behållare finns i [anropa lagrings åtgärder med OAuth-token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

> [!IMPORTANT]
> När ett lagrings konto är låst med ett Azure Resource Manager **skrivskyddat** lås, tillåts inte [list nyckel](/rest/api/storagerp/storageaccounts/listkeys) åtgärden för det lagrings kontot. **List nycklar** är en post-åtgärd och alla post-åtgärder förhindras när ett **skrivskyddat** lås har kon figurer ATS för kontot. Därför måste användare som inte redan har konto nycklar använda Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-data när kontot är låst med ett **skrivskyddat** lås. I PowerShell inkluderar du `-UseConnectedAccount` parametern för att skapa ett **AzureStorageContext** -objekt med dina AUTENTISERINGSUPPGIFTER för Azure AD.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Anropa PowerShell-kommandon med Azure AD-autentiseringsuppgifter

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill använda Azure PowerShell för att logga in och köra efterföljande åtgärder mot Azure Storage med Azure AD-autentiseringsuppgifter skapar du en lagrings kontext för att referera till lagrings kontot och inkluderar `-UseConnectedAccount` parametern.

I följande exempel visas hur du skapar en behållare i ett nytt lagrings konto från Azure PowerShell med dina autentiseringsuppgifter för Azure AD. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

1. Logga in på ditt Azure-konto med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Mer information om hur du loggar in på Azure med PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Skapa en Azure-resurs grupp genom att anropa [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Skapa ett lagrings konto genom att anropa [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Hämta lagrings konto kontexten som anger det nya lagrings kontot genom att anropa [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). När du agerar på ett lagrings konto kan du referera till kontexten i stället för att upprepade gånger skicka in autentiseringsuppgifterna. Inkludera `-UseConnectedAccount` parametern för att anropa eventuella efterföljande data åtgärder med dina autentiseringsuppgifter för Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Innan du skapar behållaren ska du tilldela rollen [Storage BLOB data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot. Mer information om hur du tilldelar Azure-roller finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Det kan ta några minuter att sprida Azures roll tilldelningar.

1. Skapa en behållare genom att anropa [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Eftersom det här anropet använder kontexten som skapades i föregående steg, skapas behållaren med dina autentiseringsuppgifter för Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Nästa steg

- [Använd PowerShell för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-powershell.md)
- [Ge åtkomst till blob-och Queue-data med hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md)