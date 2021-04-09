---
title: Objektgränser
description: Förklarar hur spatial objekts gränser kan frågas
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594492"
---
# <a name="object-bounds"></a>Objektgränser

Objekt gränser representerar den volym som en [entitet](entities.md) och dess underordnade upptar. I Azure fjärrrendering anges objekt gränser alltid som *axel justerings* ramar (AABB). Objekt gränser kan vara antingen i *lokalt utrymme* eller i *hela utrymmet*. Oavsett sätt är de alltid axelbaserade, vilket innebär att lagrings utrymmet och volymen kan skilja sig åt mellan den lokala och det dagliga utrymmes representationen.

## <a name="querying-object-bounds"></a>Frågar efter objekt gränser

Den lokala axelns begränsade avgränsnings ruta i ett [nät](meshes.md) kan frågas direkt från nät resursen. Dessa gränser kan omvandlas till ett lokalt utrymme eller ett globalt utrymme för en entitet som använder entitetens Transform.

Det är möjligt att beräkna gränserna för en hel Object-hierarki på det här sättet, men det kräver att du går igenom hierarkin, frågar gränserna för varje nät och kombinerar dem manuellt. Den här åtgärden är både omständlig och ineffektiv.

Ett bättre sätt är att anropa `QueryLocalBoundsAsync` eller `QueryWorldBoundsAsync` på en entitet. Beräkningen avlastas sedan till servern och returneras med minimal fördröjning.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API-dokumentation

* [C# entitet. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++-entitet:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Nästa steg

* [Rumsliga frågor](../overview/features/spatial-queries.md)