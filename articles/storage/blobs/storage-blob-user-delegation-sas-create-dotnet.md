---
title: Använda .NET för att skapa en användar Delegerings-SAS för en behållare eller BLOB
titleSuffix: Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS med Azure Active Directory autentiseringsuppgifter genom att använda .NET-klient biblioteket för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 512b949fceda850e968a6f97b3788ae3a602f56d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199265"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-sa för en behållare eller BLOB med Azure Storage klient biblioteket för .NET version 12.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Tilldela Azure-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker få åtkomst till BLOB-data måste säkerhets objektets behörigheter ha behörighet till resursen. Om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklings miljön, måste säkerhets objekt tilldelas en Azure-roll som ger åtkomst till BLOB-data i Azure Storage. Information om hur du tilldelar behörigheter via Azure RBAC finns i avsnittet **tilldela Azure-roller för åtkomst behörighet** i [auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Mer information om hur du autentiserar med klient biblioteket för Azure Identity från Azure Storage finns i avsnittet **autentisera med Azure Identity Library** i [ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Hämta autentiseringsuppgifter för autentiserad token

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) om du vill hämta en token-autentiseringsuppgifter som din kod kan använda för att auktorisera begär anden till Azure Storage.

Följande kodfragment visar hur du hämtar autentiseringsuppgifter för autentiserade token och använder den för att skapa en tjänst klient för Blob Storage:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Hämta användar Delegerings nyckeln

Varje SAS signeras med en nyckel. Om du vill skapa en användar Delegerings-SAS måste du först begära en användar Delegerings nyckel som sedan används för att signera SAS. Användar Delegerings nyckeln motsvarar den konto nyckel som används för att signera en tjänst-SAS eller en konto säkerhets Association, förutom att den förlitar sig på dina autentiseringsuppgifter för Azure AD. När en klient begär en användar Delegerings nyckel med en OAuth 2,0-token returnerar Azure Storage användar Delegerings nyckeln för användarens räkning.

När du har en användar Delegerings nyckel kan du använda den nyckeln för att skapa ett valfritt antal signaturer för delad åtkomst för användar delegering, under nyckelns livstid. Användar Delegerings nyckeln är oberoende av OAuth 2,0-token som används för att hämta den, så token behöver inte förnyas så länge nyckeln fortfarande är giltig. Du kan ange att nyckeln är giltig för en period på upp till sju dagar.

Använd någon av följande metoder för att begära användar Delegerings nyckeln:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Följande kodfragment hämtar användar Delegerings nyckeln och skriver ut dess egenskaper:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Hämta en användar Delegerings-SAS för en BLOB

I följande kod exempel visas den fullständiga koden för att autentisera säkerhets objekt och skapa användar Delegerings-SAS för en BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

I följande exempel testas de användar Delegerings-SAS som skapades i föregående exempel från ett simulerat klient program. Om SAS är giltig kan klient programmet läsa innehållet i blobben. Om SAS är ogiltig, till exempel om den har upphört att gälla, returnerar Azure Storage felkod 403 (förbjuden).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Hämta en användar Delegerings-SAS för en behållare

Följande kod exempel visar hur du skapar en användar Delegerings-SAS för en behållare:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

I följande exempel testas de användar Delegerings-SAS som skapades i föregående exempel från ett simulerat klient program. Om SAS är giltig kan klient programmet läsa innehållet i blobben. Om SAS är ogiltig, till exempel om den har upphört att gälla, returnerar Azure Storage felkod 403 (förbjuden).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Hämta en användar Delegerings-SAS för en katalog

Följande kod exempel visar hur du skapar en användar Delegerings-SAS för en katalog när ett hierarkiskt namn område är aktiverat:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

I följande exempel testas de användar Delegerings-SAS som skapades i föregående exempel från ett simulerat klient program. Om SAS är giltig kan klient programmet Visa fil Sök vägar för den här katalogen. Om SAS är ogiltig, till exempel om den har upphört att gälla, returnerar Azure Storage felkod 403 (förbjuden).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Hämta åtgärd för användar Delegerings nyckel](/rest/api/storageservices/get-user-delegation-key)
- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
