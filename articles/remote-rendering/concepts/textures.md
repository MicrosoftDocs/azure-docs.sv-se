---
title: Bakgrunder
description: Arbets flöde för textur resurs
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594425"
---
# <a name="textures"></a>Bakgrunder

Texturer är en oåterkallelig [delad resurs](../concepts/lifetime.md). Texturer kan läsas in från [Blob Storage](../how-tos/conversion/blob-storage.md) och tillämpas på modeller direkt, vilket visas i [Självstudier: ändra miljö och material](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Oftast kommer texturer att ingå i en [konverterad modell](../how-tos/conversion/model-conversion.md)där de refereras till av sitt [material](materials.md).

## <a name="texture-types"></a>Textur typer

Olika struktur typer har olika användnings fall:

* **2D-texturer** används huvudsakligen i [material](materials.md).
* **Cubemaps** kan användas för [luft rummet](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Struktur format som stöds

Alla texturer som har angetts för ARR måste vara i [DDS-format](https://en.wikipedia.org/wiki/DirectDraw_Surface). Helst med mipmaps och textur komprimering.

## <a name="loading-textures"></a>Läser in texturer

När du läser in en textur måste du ange dess förväntade typ. Om typen stämmer, Miss lyckas textur inläsningen.
Att läsa in en struktur med samma URI två gånger returnerar samma textur objekt, eftersom det är en [delad resurs](../concepts/lifetime.md).

Precis som vid inläsning av modeller finns det två varianter av att adressera en struktur till gång i Blob Storage för källa:

* Strukturen kan åtgärdas av Blob Storage-parametrar direkt, om [Blob Storage är länkat till kontot](../how-tos/create-an-account.md#link-storage-accounts). Relevant inläsnings funktion i det här fallet är `LoadTextureAsync` med-parameter `LoadTextureOptions` .
* Textur till gången kan åtgärdas med hjälp av SAS-URI: n. Relevant inläsnings funktion är `LoadTextureFromSasAsync` med-parameter `LoadTextureFromSasOptions` . Använd även den här varianten vid inläsning av [inbyggda texturer](../overview/features/sky.md#built-in-environment-maps).

Följande exempel kod visar hur du läser in en struktur:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Observera att om du använder sin SAS-variant är det bara inläsnings funktionen/parametern som skiljer sig åt.

Beroende på vad texturen ska användas för kan det finnas begränsningar för textur typ och innehåll. Till exempel måste en ojämnhets karta för ett [PBR-material](../overview/features/pbr-materials.md) vara gråskala.

## <a name="api-documentation"></a>API-dokumentation

* [C# textur klass](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C++ textur klass](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Nästa steg

* [Material](materials.md)
* [Himmel](../overview/features/sky.md)