---
title: Färgmaterial
description: Beskriver färg Materials typen.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593391"
---
# <a name="color-materials"></a>Färgmaterial

*Färg material* är en av de [material typer](../../concepts/materials.md) som stöds i Azure Remote rendering. De används för [nät](../../concepts/meshes.md) som inte får ta emot någon typ av belysning, utan alltid full ljus styrka. Detta kan vara fallet för "glödande" material, t. ex. bil instrument paneler, glöd lampor eller för data som redan har en statisk belysning, till exempel modeller som erhållits via [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Färg material är mer effektivt att rendera än [PBR-material](pbr-materials.md) på grund av sin enklare skuggnings modell. De har också stöd för olika genomskinlighets lägen.

## <a name="common-material-properties"></a>Vanliga material egenskaper

Dessa egenskaper är gemensamma för allt material:

* **albedoColor:** Den här färgen multipliceras med andra färger, till exempel *albedoMap* eller *:::no-loc text="vertex"::: färger*. Om *transparens* är aktiverat på ett material används alfa kanalen för att justera opaciteten, med en `1` helt ogenomskinlig och innebörd som är `0` helt transparent. Standardvärdet är White.

  > [!NOTE]
  > Eftersom färg material inte återspeglar miljön blir ett helt transparent färg material osynligt. Detta skiljer sig för [PBR-material](pbr-materials.md).

* **albedoMap:** En [2D-struktur](../../concepts/textures.md) för albedo-värden per pixel.

* **alphaClipEnabled** och **AlphaClipThreshold:** om *alphaClipEnabled* är true, kommer alla pixlar där albedo alpha-värdet är lägre än *alphaClipThreshold* att ritas. Alfa Urklipp kan användas även utan att aktivera genomskinlighet och är mycket snabbare att återge. Alfa urklippta material är fortfarande långsammare att rendera än helt ogenomskinligt material, även om. Som standard är alpha-Urklipp inaktiverat.

* **textureCoordinateScale** och **textureCoordinateOffset:** skalan multipliceras med UV-texturens koordinater. förskjutningen läggs till i den. Kan användas för att sträcka ut och byta texturer. Standard skalan är (1, 1) och förskjutningen är (0, 0).

* **useVertexColor:** Om nätet innehåller :::no-loc text="vertex"::: färger och det här alternativet är aktiverat :::no-loc text="vertex"::: multipliceras maskens färg med *albedoColor* och *albedoMap*. Som standard är *useVertexColor* inaktive rad.

* **isDoubleSided:** Om Double-sidedness är inställt på Sant återges trianglar med det här materialet även om kameran tittar på bak sidorna. Som standard är det här alternativet inaktiverat. Se även [ :::no-loc text="Single-sided"::: åter givning](single-sided-rendering.md).

* **TransparencyWritesDepth:** Om TransparencyWritesDepth-flaggan är inställd på materialet och materialet är transparent, kommer objekt som använder det här materialet också att bidra till den slutliga djup bufferten. Se egenskapen för färg material *transparencyMode* i nästa avsnitt. Aktivering av den här funktionen rekommenderas om ditt användnings fall behöver en mer plausible av [försenad fas omprojektion](late-stage-reprojection.md) av helt transparenta scener. För blandade ogenomskinliga/transparenta scener kan den här inställningen leda till implausible eller omprojektions-artefakter. Därför är standardinställningen och den rekommenderade inställningen för allmänt användnings fall att inaktivera den här flaggan. De skrivna djupen tas från djup skiktet per bild punkt i objektet som är närmast kameran.

* **FresnelEffect:** Med den här material flaggan kan additiva [Fresnel påverka](../../overview/features/fresnel-effect.md) respektive material. Utseendet på effekten styrs av de andra Fresnel-parametrarna som beskrivs i följande avsnitt. 

* **FresnelEffectColor:** Fresnel färg som används för det här materialet. Endast viktigt när Fresnel Effect-biten har angetts för det här materialet (se ovan). Den här egenskapen styr bas färgen för Fresnel Skin (se [Fresnel-effekter](../../overview/features/fresnel-effect.md) för en fullständig förklaring). För närvarande är RGB-kanalens värden viktiga och alfa värdet ignoreras.

* **FresnelEffectExponent:** Fresnel-exponenten som används för det här materialet. Endast viktigt när Fresnel Effect-biten har angetts för det här materialet (se ovan). Den här egenskapen styr spridningen av Fresnel Skin. Det minsta värdet 0,01 orsakar ett uppslag över hela objektet. Det maximala värdet 10,0 begränsar Skin till de mest gracing kanterna som visas.

## <a name="color-material-properties"></a>Egenskaper för färg material

Följande egenskaper är speciella för färg material:

* **vertexMix:** Det här värdet mellan `0` och `1` anger hur starkt :::no-loc text="vertex"::: färgen i ett [nät](../../concepts/meshes.md) bidrar till den slutliga färgen. Med standardvärdet 1 :::no-loc text="vertex"::: multipliceras färgen med albedo-färgen helt. Med värdet 0 :::no-loc text="vertex"::: ignoreras färgerna helt.

* **transparencyMode:** Till skillnad från [PBR-material](pbr-materials.md)skiljer sig färg material mellan olika genomskinlighets lägen:

  1. **Ogenomskinlig:** Standard läget inaktiverar transparens. Alfa Urklipp är fortfarande möjligt, men bör helst, och bör helst, om det behövs.
  
  1. **AlphaBlended:** Det här läget liknar genomskinlighets läget för PBR-material. Den bör användas för att se på material som glas.

  1. **Tillsats:** Det här läget är det enklaste och mest effektiva genomskinlighets läget. Material bidraget läggs till i den återgivna avbildningen. Det här läget kan användas för att simulera glödande (men fortfarande genomskinliga) objekt, till exempel markörer som används för att markera viktiga objekt.

## <a name="api-documentation"></a>API-dokumentation

* [C# ColorMaterial-klass](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ ColorMaterial-klass](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection:: CreateMaterial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Nästa steg

* [PBR-material](pbr-materials.md)
* [Bakgrunder](../../concepts/textures.md)
* [Maskor](../../concepts/meshes.md)