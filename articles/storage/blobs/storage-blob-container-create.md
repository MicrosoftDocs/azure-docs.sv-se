---
title: Skapa eller ta bort en BLOB-behållare med .NET-Azure Storage
description: Lär dig hur du skapar eller tar bort en BLOB-behållare i ditt Azure Storage-konto med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576455"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Skapa eller ta bort en behållare i Azure Storage med .NET

Blobbar i Azure Storage ordnas i behållare. Innan du kan ladda upp en BLOB måste du först skapa en behållare. Den här artikeln visar hur du skapar och tar bort behållare med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

## <a name="name-a-container"></a>Namnge en behållare

Ett behållar namn måste vara ett giltigt DNS-namn, som det utgör en del av den unika URI som används för att adressera behållaren eller dess blobbar. Följ dessa regler när du namnger en behållare:

- Behållarens namn kan vara mellan 3 och 63 tecken långt.
- Behållar namn måste börja med en bokstav eller en siffra och får bara innehålla gemena bokstäver, siffror och bindestreck (-).
- Två eller fler bindestreck i följd är inte tillåtna i behållar namn.

URI: n för en behållare har följande format:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Skapa en container

Anropa en av följande metoder för att skapa en behållare:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Dessa metoder genererar ett undantag om det redan finns en behållare med samma namn.

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

- [Skapa](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Metoderna **create** och **CreateAsync** genererar ett undantag om det redan finns en behållare med samma namn.

Metoderna **CreateIfNotExists** och **CreateIfNotExistsAsync** returnerar ett booleskt värde som anger om behållaren har skapats. Om det redan finns en behållare med samma namn, returnerar dessa metoder **falskt** för att ange att en ny behållare inte skapades.

---

Behållare skapas direkt under lagrings kontot. Det går inte att kapsla en behållare under en annan.

I följande exempel skapas en behållare asynkront:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Skapa rot behållaren

En rot behållare fungerar som en standard behållare för ditt lagrings konto. Varje lagrings konto kan ha en rot behållare, som måste ha namnet *$root*. Rot behållaren måste skapas eller tas bort explicit.

Du kan referera till en blob som lagras i rot behållaren utan att inkludera rot behållar namnet. Med rot behållaren kan du referera till en BLOB på den högsta nivån i lagrings kontots hierarki. Du kan till exempel referera till en blob som finns i rot behållaren på följande sätt:

`https://myaccount.blob.core.windows.net/default.html`

I följande exempel skapas en rot behållare synkront:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Ta bort en container

Använd någon av följande metoder för att ta bort en behållare i .NET:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

- [Ta bort](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

- [Ta bort](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

Metoderna **Delete** och **DeleteAsync** genererar ett undantag om behållaren inte finns.

Metoderna **DeleteIfExists** och **DeleteIfExistsAsync** returnerar ett booleskt värde som anger om containern har tagits bort. Om den angivna behållaren inte finns, returnerar dessa metoder **falskt** för att indikera att behållaren inte har tagits bort.

När du har tagit bort en behållare kan du inte skapa en behållare med samma namn i *minst* 30 sekunder. Försök att skapa en behållare med samma namn kommer att Miss lyckas med HTTP-felkoden 409 (konflikt). Andra åtgärder på behållaren eller blobar som den innehåller kommer att Miss Missing med HTTP-felkoden 404 (hittades inte).

I följande exempel tar bort den angivna behållaren och hanterar undantaget om behållaren inte finns:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

I följande exempel visas hur du tar bort alla behållare som börjar med ett angivet prefix.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Skapa container åtgärd](/rest/api/storageservices/create-container)
- [Åtgärd för att ta bort container](/rest/api/storageservices/delete-container)
