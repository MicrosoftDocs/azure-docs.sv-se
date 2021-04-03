---
title: Få åtkomst till offentliga behållare och blobbar anonymt med .NET
titleSuffix: Azure Storage
description: Använd Azure Storage klient bibliotek för .NET för att få åtkomst till offentliga behållare och blobbar anonymt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088824"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Få åtkomst till offentliga behållare och blobbar anonymt med .NET

Azure Storage stöder valfri offentlig Läs behörighet för behållare och blobbar. Klienter kan komma åt offentliga behållare och blobbar anonymt med hjälp av Azure Storage klient bibliotek, samt med hjälp av andra verktyg och verktyg som har stöd för data åtkomst till Azure Storage.

Den här artikeln visar hur du kommer åt en offentlig behållare eller BLOB från .NET. Information om hur du konfigurerar anonym Läs åtkomst på en behållare finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md). Information om hur du förhindrar anonym åtkomst till ett lagrings konto finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

En klient som har åtkomst till behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. I följande exempel visas några olika sätt att referera till behållare och blobbar anonymt.

## <a name="create-an-anonymous-client-object"></a>Skapa ett anonymt klient objekt

Du kan skapa ett nytt tjänst klient objekt för anonym åtkomst genom att tillhandahålla slut punkten för Blob Storage för kontot. Du måste dock också känna till namnet på en behållare i kontot som är tillgängligt för anonym åtkomst.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Referera till en behållare anonymt

Om du har URL: en till en behållare som är anonymt tillgänglig kan du använda den för att referera till behållaren direkt.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Referera till en BLOB anonymt

Om du har en URL till en blob som är tillgänglig för anonym åtkomst kan du referera till blobben direkt med den URL: en:

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md)
- [Förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md)
- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
