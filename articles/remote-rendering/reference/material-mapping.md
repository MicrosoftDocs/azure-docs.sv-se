---
title: Materialmappning för modellformat
description: Beskriver standard konvertering från modell käll format till PBR-material
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024052"
---
# <a name="material-mapping-for-model-formats"></a>Materialmappning för modellformat

När en käll till gång [konverteras som en modell](../how-tos/conversion/model-conversion.md), skapar konverteraren [material](../concepts/materials.md) för varje [nät](../concepts/meshes.md). Hur material skapas kan [åsidosättas](../how-tos/conversion/override-materials.md). Konverteringen kommer dock att skapa [PBR-material](../overview/features/pbr-materials.md)som standard. Eftersom varje käll fil format, t. ex. FBX, använder sina egna konventioner för att definiera material, måste dessa konventioner mappas till PBR-materialets parametrar för Azure Remote rendering. 

Den här artikeln innehåller exakta mappningar som används för att konvertera material från käll till gångar till körnings material.

## <a name="gltf"></a>glTF

Nästan allt från glTF 2,0-specifikationen stöds i Azure Remote rendering, förutom *EmissiveFactor* och *EmissiveTexture*.

Följande tabell visar mappningen:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metall                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   grovhet                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   ocklusion                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode. OGENOMSKINLIG  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode. MASK    |   alphaClipEnabled = True, isTransparent = false  |
|   alphaMode. BLEND   |   isTransparent = True     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Varje textur i glTF kan ha ett `texCoord` värde som också stöds i Azure-Fjärråter givnings material.

### <a name="embedded-textures"></a>Inbäddade texturer

Texturer som är inbäddade i *\* . bin* -eller *\* . GLB* -filer stöds.

### <a name="supported-gltf-extension"></a>GlTF-tillägg som stöds

Förutom bas funktions uppsättningen stöder Azure-fjärråter givning följande glTF-tillägg:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): motsvarar [färg material](../overview/features/color-materials.md). För *emissive* material rekommenderar vi att du använder det här tillägget.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): i stället för metallisk – tuffa strukturer kan du tillhandahålla diffuse-spegel-glossiness texturer. Implementeringen av Azure-fjärrrenderingen följer direkt konverterings formlerna från tillägget.

## <a name="fbx"></a>FBX

FBX-formatet är stängd-källa och FBX material är inte kompatibla med PBR-material i allmänhet. FBX använder en komplex Beskrivning av ytor med många unika parametrar och egenskaper och **inte alla används av pipeline för Azure-Fjärrrendering**.

> [!IMPORTANT]
> Konverterings pipelinen för Azure Remote rendering Model stöder endast **FBX 2011 och högre**.

FBX-formatet definierar ett försiktigt tillvägagångs sätt för material, det finns bara två typer i den officiella FBX-specifikationen:

* *Lambert* – används vanligt vis inte för lite tid, men stöds fortfarande av konvertering till Phong vid konverterings tiden.
* *Phong* – nästan allt material och de flesta innehålls verktyg använder den här typen.

Phong-modellen är mer korrekt och används som den *enda* modellen för FBX material. Under den kallas det *FBX materialet*.

> Maya använder två anpassade tillägg för FBX genom att definiera anpassade egenskaper för ett materials PBR-och Stingray-typer. Den här informationen ingår inte i FBX-specifikationen, så den stöds inte av Azure Remote rendering för närvarande.

FBX material använder begreppet diffuse-spegel-SpecularLevel, så att du kan konvertera från en diffus textur till en albedo-karta som vi behöver för att beräkna de andra parametrarna för att subtrahera dem från diffuse.

> Alla färger och texturer i FBX är i sRGB-området (även kallat gamma avstånd), men Azure-fjärrrendering fungerar med linjärt utrymme under visualiseringen och i slutet av ramen, vilket konverterar allt tillbaka till sRGB-utrymmet. I pipeline för Azure Remote rendering-till gångar konverteras allt till linjärt utrymme för att skicka det som för beredd data till åter givningen.

Den här tabellen visar hur texturer mappas från FBX material till Azure Remote rendering-material. En del av dem används inte direkt utan i kombination med andra texturer som ingår i formlerna (till exempel diffus textur):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Ocklusion-karta   |
| DiffuseColor | *används för albedo, Metaly* |
| TransparentColor | *används för alfa Channel av albedo* |
| TransparencyFactor | *används för alfa Channel av albedo* |
| Ogenomskinlighet | *används för alfa Channel av albedo* |
| SpecularColor | *används för albedo, Metallhet, grovhet* |
| SpecularFactor| *används för albedo, Metallhet, grovhet* |
| ShininessExponent | *används för albedo, Metallhet, grovhet* |
| NormalMap | NormalMap |
| Öka | *konverterat till NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Mappningen ovan är den mest komplexa delen av material omvandlingen, på grund av många antaganden som måste göras. Vi diskuterar dessa antaganden nedan.

Vissa definitioner som används nedan:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Röd ∗ 0,2125 +  `Specular` . Grönt ∗ 0,7154 + `Specular` . Blå ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Röd<sup>2</sup> + 0,587 * `Diffuse` . Grön<sup>2</sup> + 0,114 * `Diffuse` . Blå<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Röd<sup>2</sup> + 0,587 * `Specular` . Grön<sup>2</sup> + 0,114 * `Specular` . Blå<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Röd, `Specular` . Grönt, `Specular` . Blåskärm

SpecularIntensity-formeln hämtas [härifrån](https://en.wikipedia.org/wiki/Luma_(video)).
Formeln för ljus styrka beskrivs i den här [specifikationen](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Grovhet

`Roughness` beräknas från `Specular` och `ShininessExponent` med [den här formeln](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). Formeln är en uppskattning av grovhet från Phong spegel exponent:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metall

`Metalness` beräknas från `Diffuse` och `Specular` med den här [formeln från glTF-specifikationen](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Tanken här är att vi löser ekvationen: AX<sup>2</sup> + BX + C = 0.
Dielectric ytor återspeglar i princip ungefär 4% av ljus på ett spegel sätt och resten är diffus. Metallisk ytor reflekterar inget ljus på ett diffust sätt, men allt på ett spegel sätt.
Den här formeln har några nack delar, eftersom det inte finns något sätt att skilja mellan blank plast och glansiga metallisk ytor. Vi antar att ytan har metallisk egenskaper, och därför kanske det inte ser ut som förväntat.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` beräknas från `Diffuse` , `Specular` och `Metalness` .

Som det beskrivs i avsnittet Metallhet, dielectric ytorna runt 4% av ljuset.  
Idén här är att du linjärt interpolerar mellan `Dielectric` och `Metal` färger med `Metalness` värde som en faktor. Om det är något som är `0.0` så är det, beroende på speglad kvalitet, antingen en mörk färg (om spegeln är hög) eller om diffusionen inte ändras (om det inte finns någon spegel). Om metal är ett stort värde försvinner den diffusa färgen för att spegla färg.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` har beräknats av formeln ovan, men alfa kanalen kräver ytterligare beräkningar. FBX-formatet är vagt om genomskinlighet och har många sätt att definiera det. Olika innehålls verktyg använder olika metoder. Tanken här är att förena dem till en formel. Det gör vissa till gångar felaktigt visade som transparenta, men om de inte skapas på ett vanligt sätt.

Detta beräknas från `TransparentColor` , `TransparencyFactor` , `Opacity` :

Om `Opacity` är definierat använder du den direkt: `AlbedoAlpha`  =  `Opacity` annars  
Om `TransparencyColor` är definierat `AlbedoAlpha` = 1,0-(( `TransparentColor` . Röd + `TransparentColor` . Grön + `TransparentColor` . Blå)/3,0) Else  
Om `TransparencyFactor` , sedan `AlbedoAlpha` = 1,0- `TransparencyFactor`

Den slutliga `Albedo` färgen har fyra kanaler som kombinerar `AlbedoRGB` med `AlbedoAlpha` .

### <a name="summary"></a>Sammanfattning

För att kunna sammanfatta här är `Albedo` det mycket nära originalet `Diffuse` , om `Specular` det är nära noll. Annars kommer ytan att se ut som en metallisk yta och förlora den diffusa färgen. Ytan kommer att se mer snyggare ut och reflekteras om den `ShininessExponent` är tillräckligt stor och `Specular` är ljus. I annat fall ser ytan grovt ut och knappt reflekterar miljön.

### <a name="known-issues"></a>Kända problem

* Den aktuella formeln fungerar inte bra för enkel färg geometri. Om `Specular` är tillräckligt ljus blir alla Geometries reflekterande metallisk ytor utan färg. Lösningen här är att sänka `Specular` till 30% från originalet eller att använda konverterings inställningen [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* PBR-material har nyligen lagts till i `Maya` och `3DS Max` skapa innehålls verktyg. De använder anpassade användardefinierade egenskaper för svart ruta för att skicka den till FBX. Azure Remote rendering läser inte dessa ytterligare egenskaper eftersom de inte är dokumenterade och formatet är stängd-källa.

## <a name="next-steps"></a>Nästa steg

* [Modell konvertering](../how-tos/conversion/model-conversion.md)
* [Åsidosätter material under modell konverteringen](../how-tos/conversion/override-materials.md)
