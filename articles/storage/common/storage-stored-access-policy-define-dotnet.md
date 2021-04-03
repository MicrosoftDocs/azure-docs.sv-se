---
title: Skapa en lagrad åtkomst princip med .NET
titleSuffix: Azure Storage
description: Använd Azure Storage och .NET för att skapa en lagrad åtkomst princip. Utöva ytterligare kontroll nivåer över signaturer för delad åtkomst på tjänst nivå på servern.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010757"
---
# <a name="create-a-stored-access-policy-with-net"></a>Skapa en lagrad åtkomst princip med .NET

En lagrad åtkomst princip ger ytterligare kontroll nivå över signaturer för delad åtkomst på tjänst nivå (SAS) på Server sidan. Att definiera en lagrad åtkomst princip används för att gruppera signaturer för delad åtkomst och för att ge ytterligare begränsningar för signaturer för delad åtkomst som är kopplade till principen. Du kan använda en lagrad åtkomst princip för att ändra start tid, förfallo tid eller behörigheter för en SAS eller återkalla den när den har utfärdats.
  
Följande Azure Storage resurser har stöd för lagrade åtkomst principer:  
  
- Blobcontainrar  
- Filresurser  
- Köer  
- Tabeller  
  
> [!NOTE]
> En lagrad åtkomst princip på en behållare kan associeras med en signatur för delad åtkomst som beviljar behörigheter till själva behållaren eller till de blobbar som den innehåller. På samma sätt kan en lagrad åtkomst princip på en fil resurs associeras med en signatur för delad åtkomst som beviljar behörighet till själva resursen eller filer som den innehåller.  
>
> Lagrade åtkomst principer stöds endast för en tjänst-SAS. Lagrade åtkomst principer stöds inte för konto-SAS eller användar Delegerings-SAS.  

Mer information om lagrade åtkomst principer finns i [definiera en lagrad åtkomst princip](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Skapa en lagrad åtkomst princip

Den underliggande REST-åtgärden för att skapa en lagrad åtkomst princip har [angetts som behållar-ACL](/rest/api/storageservices/set-container-acl). Du måste auktorisera åtgärden för att skapa en lagrad åtkomst princip via en delad nyckel genom att använda kontots åtkomst nycklar i en anslutnings sträng. Det går inte att auktorisera ACL-åtgärden för **uppsättnings behållare** med Azure AD-autentiseringsuppgifter. Mer information finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

I följande kod exempel skapar du en lagrad åtkomst princip på en behållare. Du kan använda åtkomst principen för att ange begränsningar för en tjänst-SAS på behållaren eller dess blobbar.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill skapa en lagrad åtkomst princip på en behållare med version 12 av .NET-klient biblioteket för Azure Storage, anropa någon av följande metoder:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

I följande exempel skapas en lagrad åtkomst princip som gäller för en dag och som ger Läs-/skriv behörigheter:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill skapa en lagrad åtkomst princip på en behållare med version 12 av .NET-klient biblioteket för Azure Storage, anropa någon av följande metoder:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

I följande exempel skapas en lagrad åtkomst princip som gäller för en dag och som ger Läs-, skriv-och list behörigheter:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Se även

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
- [Definiera en lagrad åtkomstprincip](/rest/api/storageservices/define-stored-access-policy)
- [Konfigurera anslutningssträngar för Azure Storage](storage-configure-connection-string.md)
