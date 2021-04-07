---
title: Komponenter
description: Definition av komponenter i omfånget för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592202"
---
# <a name="components"></a>Komponenter

Azure Remote rendering använder [enhets komponentens system](https://en.wikipedia.org/wiki/Entity_component_system) mönster. Medan [entiteter](entities.md) representerar positionen och den hierarkiska sammansättningen av objekt, är komponenter ansvariga för att implementera beteendet.

De vanligaste typerna av komponenter är [:::no-loc text="mesh components":::](meshes.md) , som lägger till nät i åter givnings pipelinen. På samma sätt används [ljusa komponenter](../overview/features/lights.md) för att lägga till belysnings-och [klipp Plans komponenter](../overview/features/cut-planes.md) som används för att klippa ut öppna maskor.

Alla dessa komponenter använder transformeringen (position, rotation, skala) för den entitet som de är kopplade till, som referens punkt.

## <a name="working-with-components"></a>Arbeta med komponenter

Du kan enkelt lägga till, ta bort och manipulera komponenter program mässigt:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

En komponent är kopplad till en entitet vid skapande tillfället. Den kan inte flyttas till en annan entitet efteråt. Komponenter tas uttryckligen bort med `Component.Destroy()` eller automatiskt när komponentens ägarnod förstörs.

Endast en instans av varje komponent typ kan läggas till i en entitet i taget.

## <a name="unity-specific"></a>Unity Specific

Union-integreringen har ytterligare utöknings bara funktioner för att interagera med komponenter. Se [Unity Game Objects and Components](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API-dokumentation

* [C#-ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# entitet. FindComponentOfType ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++-ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: CreateComponent ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++-entitet:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Nästa steg

* [Objektgränser](object-bounds.md)
* [Maskor](meshes.md)