---
title: Fresnel-påverkan
description: Funktions förklarings sida för Fresnel material-effekter
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594120"
---
# <a name="fresnel-effect"></a>Fresnel-påverkan

Fresnel Effect-funktionen är en icke-fysiskt korrekt, ad hoc-påverkan. Funktionen baseras på den fysiska observationen av objekt som blir mer reflekterade i dessa vinklar. Fresnel-reflektionen är redan fysiskt inbyggd i [PBR-materialets modell](../../overview/features/pbr-materials.md) som används i Azure Remote rendering. Fresnel Effect-funktionen är däremot bara en tillsats av färg effekter utan beroende på [ljus](../../overview/features/lights.md) -eller [luft rummets miljö](../../overview/features/sky.md).

Fresnel-effekter ger påverkade objekt en färgad Skin runt deras kanter. Information om anpassning av effekter och exempel på åter givnings resultaten finns i följande avsnitt.

## <a name="enabling-the-fresnel-effect"></a>Aktivera Fresnel-påverkan

Om du vill använda Fresnel-funktionen måste den vara aktive rad på det aktuella materialet. Du kan aktivera det genom att ange FresnelEffect-biten för [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) på [PBR-materialet](../../overview/features/pbr-materials.md). Samma mönster gäller för [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) och [färg materialet](../../overview/features/color-materials.md). I avsnittet kod exempel finns en demonstration av användningen.

När aktiveringen är aktive rad visas Fresnel-effekter direkt. Som standard är skinen vit (1, 1, 1, 1) och har en exponent på 1. Du kan anpassa dessa inställningar med hjälp av parametern setter nedan.

## <a name="customizing-the-effect-appearance"></a>Anpassa utseendet på effekten

För närvarande kan Fresnel-påverkan anpassas per material med följande egenskaper:

| Material egenskap | Typ | Förklaring |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Den färg som läggs till högst Fresnel Skin. Alfa kanalen ignoreras för närvarande. |
| FresnelEffectExponent | flyt | Spridningen av Fresnel Skin. Sträcker sig från 0,01 (sprids över hela objektet) till 10 (endast de mest gracinga vinklarna). |

I praktiken ser olika färg-och exponent inställningar ut så här:

![Exempel på Fresnel-effekter](./media/fresnel-effect-examples.png)

Fresnel-exponentens exponent ökar progressivt från 1 till 10 för varje färg rad. Om du gör det hämtas Fresnel Skin till kanterna för de visade objekten. Fresnel-effekten påverkas också av transparens, som du kan se i följande exempel:

![Genomskinlighets exempel för Fresnel-effekter](./media/fresnel-effect-transparent-examples.png)

Som visas är objekten på diagonalen helt transparenta, men Fresnel Skin är kvar. -Effekterna efterliknar fysiskt baserade Fresnel i detta hänseende, som också finns i dessa skärm bilder.

## <a name="code-samples"></a>Kodexempel

Följande kod exempel visar hur du aktiverar och anpassar Fresnel-effekter för både ett [PBR-material](../../overview/features/pbr-materials.md) och ett [färg material](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API-dokumentation

* [C#-PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++-PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C#-ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++-ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Nästa steg

* [Material](../../concepts/materials.md)
* [PBR-material](../../overview/features/pbr-materials.md)
* [Färg material](../../overview/features/color-materials.md)