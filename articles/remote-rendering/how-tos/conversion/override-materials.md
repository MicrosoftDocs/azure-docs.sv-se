---
title: Åsidosätta material under modellkonverteringen
description: Förklarar det material som åsidosätter arbets flödet vid konverterings tillfället
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576650"
---
# <a name="override-materials-during-model-conversion"></a>Åsidosätta material under modellkonverteringen

Material inställningarna i käll modellen används för att definiera det PBR- [material](../../overview/features/pbr-materials.md) som används av åter givningen.
Ibland ger [standard konverteringen](../../reference/material-mapping.md) inga önskade resultat och du måste göra ändringar.
När en modell konverteras för användning i Azure-fjärrrendering kan du ange en fil för åsidosättning av material för att anpassa hur material konvertering sker per material.
Om en fil `<modelName>.MaterialOverrides.json` som heter finns i behållaren indata i indata `<modelName>.<ext>` -modellen, används den som filen för att åsidosätta materialet.

## <a name="the-override-file-used-during-conversion"></a>Den åsidosättande fil som används under konverteringen

Ett enkelt exempel är att anta att en box-modell har ett enda material, som kallas "default".
Låt oss säga att dess albedo-färg måste justeras för användning i ARR.
I det här fallet `box.MaterialOverrides.json` kan en fil skapas på följande sätt:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json`Filen placeras i behållaren indata bredvid `box.fbx` , vilket instruerar konverterings tjänsten att tillämpa de nya inställningarna.

### <a name="color-materials"></a>Färgmaterial

[Färg Materials](../../overview/features/color-materials.md) modellen beskriver en konstant skuggad yta som är oberoende av belysningen.
Färg material är användbart för till gångar som görs av Photogrammetry-algoritmer, till exempel.
Vid åsidosättning av materialiserade filer kan ett material deklareras som ett färg material genom att ställa in `unlit` på `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorera vissa textur kartor

Ibland kanske du vill att konverterings processen ska ignorera vissa textur kartor. Detta kan vara fallet när modellen genererades av ett verktyg som genererar särskilda Maps som inte tolkas korrekt av åter givningen. Till exempel en "OpacityMap" som används för att definiera något annat än ogenomskinlighet, eller en modell där "NormalMap" lagras som "BumpMap". (I det senare fallet vill du ignorera "NormalMap", vilket innebär att konverteraren använder "BumpMap" som "NormalMap".)

Principen är enkel. Lägg bara till en egenskap som heter `ignoreTextureMaps` och Lägg till eventuella textur scheman som du vill ignorera:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

En fullständig lista över textur mappningar som du kan ignorera finns i JSON-schemat nedan.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Tillämpa samma åsidosättningar på flera material

Som standard gäller en post i filen för material åsidosättning när namnet matchar material namnet exakt.
Eftersom samma åsidosättning bör gälla för flera material kan du ange ett reguljärt uttryck som postnamn.
Fältet `nameMatching` har ett standardvärde `exact` , men det kan anges till att ange att `regex` posten ska gälla för alla matchande material.
Syntaxen som används är densamma som används för Java Script. I följande exempel visas en åsidosättning som gäller för material med namn som "Material2", "Material01" och "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Högst en post i en fil för åsidosättning av material gäller för ett enskilt material.
Om det finns en exakt matchning (d.v.s. saknas `nameMatching` eller är lika med `exact` ) för material namnet väljs den posten.
Annars väljs den första regex-posten i filen som matchar material namnet.

### <a name="getting-information-about-which-entries-applied"></a>Hämta information om vilka poster som har tillämpats

[Informations filen](get-information.md#information-about-a-converted-model-the-info-file) som skrivs till behållaren utdata innehåller information om antalet åsidosättningar som tillhandahålls och hur många material som åsidosatts.

## <a name="json-schema"></a>JSON-schema

Det fullständiga JSON-schemat för material-filer anges här. Med undantag för `unlit` och `ignoreTextureMaps` , är tillgängliga egenskaper en delmängd av egenskaperna som beskrivs i avsnitten om [färg material](../../overview/features/color-materials.md) -och PBR- [materialets](../../overview/features/pbr-materials.md) modeller.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Nästa steg

* [Färgmaterial](../../overview/features/color-materials.md)
* [PBR-material](../../overview/features/pbr-materials.md)
