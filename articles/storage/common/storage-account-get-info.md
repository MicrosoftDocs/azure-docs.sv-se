---
title: Hämta lagrings konto typ och SKU-namn med .NET
titleSuffix: Azure Storage
description: Lär dig hur du hämtar Azure Storage konto typ och SKU-namn med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fd693573858df095b62a7a7917563141ac19c5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94579342"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Hämta lagrings konto typ och SKU-namn med .NET

Den här artikeln visar hur du hämtar Azure Storage konto typ och SKU-namn för en blob med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

## <a name="about-account-type-and-sku-name"></a>Om kontotyp och SKU-namn

**Kontotyp**: giltiga konto typer är,,,, `BlobStorage` `BlockBlobStorage` `FileStorage` `Storage` och `StorageV2` . [Översikt över Azure Storage-kontot](storage-account-overview.md) innehåller mer information, inklusive beskrivningar av de olika lagrings kontona.

**SKU-namn**: giltiga SKU-namn är,,,,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` och `Standard_ZRS` . SKU-namn är Skift läges känsliga och är sträng fält i [klassen SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Hämta konto information

I följande kod exempel hämtas och visas de skrivskyddade konto egenskaperna.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill hämta lagrings konto typen och det SKU-namn som är associerat med en BLOB anropar du metoden [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) eller [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill hämta lagrings konto typen och det SKU-namn som är associerat med en BLOB anropar du metoden [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) eller [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) .

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra åtgärder som du kan utföra på ett lagrings konto via [Azure Portal](https://portal.azure.com) och Azure-REST API.

- [Hämta konto informations åtgärd (REST)](/rest/api/storageservices/get-account-information)
