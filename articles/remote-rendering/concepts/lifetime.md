---
title: Objekt- och resurslivslängd
description: Förklarar livs längds hantering för olika typer
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593899"
---
# <a name="object-and-resource-lifetime"></a>Objekt- och resurslivslängd

Azure-fjärrrendering skiljer sig mellan två typer: **objekt** och **resurser**.

## <a name="object-lifetime"></a>Objektets livs längd

*Objekt* anses vara saker som användaren kan skapa, ändra och ta bort efter eget gottfinnande. Objekt kan dupliceras fritt och varje instans kan överligga över tid. Därför är [entiteter](entities.md) och [komponenter](components.md) objekt.

Livs längden för objekt är helt under användar kontroll. Den är inte relaterad till livs längden för åter givningen på klient sidan, men. Klasser som `Entity` och `Component` har en `Destroy` funktion som måste anropas för att frigöra objektet på värddatorn för fjärrrendering. Dessutom `Entity.Destroy()` kommer att förstöra entiteten, dess underordnade objekt och alla komponenter i den hierarkin.

## <a name="resource-lifetime"></a>Resurs livs längd

*Resurser* är saker vars livs längd hanteras helt av värddatorn för fjärrrendering. Resurser är referenser som räknas internt. De avallokeras när inga referenser till dem längre.

De flesta resurser kan bara skapas indirekt, vanligt vis genom att läsa in dem från en fil. När samma fil läses in flera gånger returnerar Azure Remote rendering samma referens och läser inte in data igen.

Många resurser är oföränderliga, till exempel [nät](meshes.md) och [texturer](textures.md). Vissa resurser är föränderligt, men till exempel [material](materials.md). Eftersom resurserna ofta delas kan det påverka flera objekt om du ändrar en resurs. Om du till exempel ändrar färg på ett material ändras färgen på alla objekt som använder maskor, vilket i sin tur refererar till materialet.

### <a name="built-in-resources"></a>Inbyggda resurser

Azure fjärrrendering innehåller några inbyggda resurser som kan läsas in genom att vänta på respektive identifierare med `builtin://` under anropet till `RenderingSession.Connection.LoadXYZAsync()` . De tillgängliga inbyggda resurserna visas i dokumentationen för respektive funktion. I det här [kapitlet](../overview/features/sky.md) visas till exempel den inbyggda himmelsblå-texturen.

## <a name="general-lifetime"></a>Allmän livs längd

Livs längden för alla objekt och resurser är kopplad till anslutningen. Vid från koppling tas alla bort. När du återansluter till samma session kommer scen diagrammet vara tomt och alla resurser rensas.

I praktiken är inläsning av samma resurs i en session, efter en från koppling, vanligt vis snabbare än första gången. Detta är fallet eftersom de flesta resurser måste hämtas från Azure Storage första gången, vilket inte är nödvändigt den andra gången, vilket sparar mycket tid.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Komponenter](components.md)
* [Modeller](models.md)
