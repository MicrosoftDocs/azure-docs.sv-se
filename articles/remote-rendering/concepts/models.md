---
title: Modeller
description: Beskriver vad en modell är i Azure Remote rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593882"
---
# <a name="models"></a>Modeller

En *modell* i Azure Remote rendering refererar till en fullständig objekt representation, som består av [entiteter](entities.md) och [komponenter](components.md). Modeller är det huvudsakliga sättet att hämta anpassade data till tjänsten Remote rendering.

## <a name="model-structure"></a>Modellstruktur

En modell har exakt en [entitet](entities.md) som dess rotnod. Nedan kan det finnas en godtycklig hierarki med underordnade entiteter. När du läser in en modell returneras en referens till den här rot enheten.

Varje entitet kan ha [komponenter](components.md) kopplade. I det vanligaste fallet har entiteterna *MeshComponents*, som refererar till [nät resurserna](meshes.md).

## <a name="creating-models"></a>Skapa modeller

Att skapa modeller för körning uppnås genom att [konvertera inmatade modeller](../how-tos/conversion/model-conversion.md) från fil format som FBX och GLTF. Konverterings processen extraherar alla resurser, till exempel texturer, material och maskor, och konverterar dem till optimerade körnings format. Den kommer också att extrahera struktur information och omvandla den till ARR: s entitet/komponent diagram struktur.

> [!IMPORTANT]
> [Modell konvertering](../how-tos/conversion/model-conversion.md) är det enda sättet att skapa [nät](meshes.md). Även om maskor kan delas mellan entiteter vid körning, finns det inte något annat sätt att få ett nät i körningen, förutom att läsa in en modell.

## <a name="loading-models"></a>Läser in modeller

När en modell har konverterats kan den läsas in från Azure Blob Storage till körnings miljön.

Det finns två distinkta inläsnings funktioner som skiljer sig åt av sättet till gången i Blob Storage:

* Modellen kan åtgärdas av Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det här fallet är `LoadModelAsync` med-parameter `LoadModelOptions` .
* Modellen kan åtgärdas med hjälp av SAS-URI: n. Relevant inläsnings funktion är `LoadModelFromSasAsync` med-parameter `LoadModelFromSasOptions` . Använd även den här varianten vid inläsning av [inbyggda modeller](../samples/sample-model.md).

Följande kodfragment visar hur du läser in modeller med någon av funktionerna. Om du vill läsa in en modell med dess Blob Storage-parametrar använder du kod som den nedan:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Om du vill läsa in en modell med hjälp av en SAS-token använder du kod som liknar följande kodfragment:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Sedan kan du bläddra i organisationshierarkin och ändra entiteter och komponenter. Att läsa in samma modell flera gånger skapar flera instanser, var och en med en egen kopia av entiteten/komponent strukturen. Eftersom nät, material och texturer är [delade resurser](../concepts/lifetime.md)kommer deras data inte att läsas in igen, trots. Därför instansierar en modell mer än en gång, vilket innebär relativt lite minnes belastning.

## <a name="api-documentation"></a>API-dokumentation

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Maskor](meshes.md)