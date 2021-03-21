---
title: Ange en kundspecifik nyckel på en begäran till Blob Storage med .NET-Azure Storage
description: Lär dig hur du anger en kundanged nyckel på en begäran till Blob Storage med hjälp av .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693518"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ange en kundspecifik nyckel på en begäran till Blob Storage med .NET

Klienter som begär förfrågningar mot Azure Blob Storage har möjlighet att tillhandahålla en AES-256-krypterings nyckel på en enskild begäran. Inklusive krypterings nyckeln på begäran ger detaljerad kontroll över krypterings inställningarna för Blob Storage-åtgärder. Kundspecifika nycklar kan lagras i Azure Key Vault eller i en annan nyckel lagrings plats.

Den här artikeln visar hur du anger en kunds nyckel på en begäran med .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Om du vill veta mer om hur du autentiserar med klient biblioteket för Azure Identity kan du läsa mer i [Azure Identity Client library för .net](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Använd en kundanged nyckel för att skriva till en BLOB

I följande exempel visas en AES-256-nyckel när du laddar upp en blob med V12-klient biblioteket för Blob Storage. I exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -objektet för att auktorisera skrivbegäran med Azure AD, men du kan också auktorisera begäran med autentiseringsuppgifter för delad nyckel.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Ange en krypterings nyckel för en begäran till Blob Storage](encryption-customer-provided-keys.md)
- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
