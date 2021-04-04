---
title: Scenbelysning
description: Beskrivning och egenskaper för ljus källa
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206857"
---
# <a name="scene-lighting"></a>Scenbelysning

Som standard tänds de fjärranslutna objekten med hjälp av en [luft rummets ljus](sky.md). För de flesta program är det redan tillräckligt, men du kan lägga till ytterligare ljus källor till scenen.

> [!IMPORTANT]
> Endast [PBR-material](pbr-materials.md) påverkas av ljusa källor. [Färg material](color-materials.md) visas alltid helt ljust.

> [!NOTE]
> Byte av skuggor stöds inte för närvarande. Azures fjärrrendering är optimerad för att återge enorma mängder geometrier och använda flera GPU: er vid behov. Traditionella metoder för skugg byte fungerar inte bra i sådana scenarier.

## <a name="common-light-component-properties"></a>Egenskaper för gemensam ljus komponent

Alla ljus typer härleds från den abstrakta Bask Lassen `LightComponent` och delar dessa egenskaper:

* **Färg:** Färgen på ljuset i [gamma avstånd](https://en.wikipedia.org/wiki/SRGB). Alfa ignoreras.

* **Intensitet:** Ljus styrka för ljuset. För punkt-och Spotlight-lampor definierar intensiteten även hur långt ljuset skiner.

## <a name="point-light"></a>Punkt ljus

I Azure fjärrrendering `PointLightComponent` kan inte bara dra ljuset från en enda punkt, utan även från en liten sfär eller ett litet rör, för att simulera mjuka ljus källor.

### <a name="pointlightcomponent-properties"></a>Egenskaper för PointLightComponent

* **RADIUS:** Standardvärdet är noll, vilket innebär att ljuset fungerar som ett punkt ljus. Om radien är större än noll fungerar den som sfärisk ljus källa, vilket ändrar utseendet på speglade höjd punkter.

* **Längd:** Om både `Length` och `Radius` är icke-noll fungerar ljuset som ett rör ljus. Detta kan användas för att simulera Neon-rör.

* **AttenuationCutoff:** Om vänster till (0, 0) är dämpningen av ljuset bara beroende av dess `Intensity` . Du kan dock ange egna min-/max avstånd som ljus styrkan skalas linjärt ned till 0. Den här funktionen kan användas för att genomdriva en mindre mängd påverkan av ett speciellt ljus.

* **ProjectedCubemap:** Om värdet är inställt på ett giltigt [cubemap](../../concepts/textures.md), projiceras texturen på ljusets omgivande geometri. Cubemap färg är modulerad med ljusets färg.

## <a name="spot-light"></a>Dekor ljus

`SpotLightComponent`Liknar det `PointLightComponent` men ljuset är begränsat till formen av en kon. Konnas orientering definieras av *ägarens negativa z-axel*.

### <a name="spotlightcomponent-properties"></a>Egenskaper för SpotLightComponent

* **RADIUS:** Samma som för `PointLightComponent` .

* **SpotAngleDeg:** Detta intervall definierar den inre och den yttre vinkeln för kon, mätt i grader. Allt inom den inre vinkeln lyser med full ljus styrka. Ett utfall tillämpas mot den yttre vinkel som genererar en Penumbra effekt.

* **FalloffExponent:** Definierar hur skarpa utfalls över gångarna mellan den inre och den yttre kon vinkeln. Ett högre värde resulterar i en skarpare över gång. Standardvärdet på 1,0 resulterar i linjär över gång.

* **AttenuationCutoff:** Samma som för `PointLightComponent` .

* **Projected2dTexture:** Om den är inställd på en giltig [2D-struktur](../../concepts/textures.md)projiceras bilden på geometrin som ljuset skiner på. Texturens färg är modulerad med ljusets färg.

## <a name="directional-light"></a>Riktat ljus

`DirectionalLightComponent`Simulerar en ljus källa som är oändligt långt bort. Ljuset skiner i riktningen för den *negativa z-axeln i entiteten ägare*. Entitetens position ignoreras.

Det finns inga ytterligare egenskaper.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Ljusa källor har en betydande inverkan på åter givnings prestanda. Använd dem noggrant och endast om det krävs av programmet. Alla statiska globala belysnings villkor, inklusive en statisk riktad komponent, kan uppnås med en [anpassad luft rummets struktur](sky.md), utan ytterligare åter givnings kostnad.

## <a name="api-documentation"></a>API-dokumentation

* [C# LightComponentBase-klass](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent-klass](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent-klass](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent-klass](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase-klass](/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent-klass](/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent-klass](/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent-klass](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Nästa steg

* [Material](../../concepts/materials.md)
* [Himmel](sky.md)