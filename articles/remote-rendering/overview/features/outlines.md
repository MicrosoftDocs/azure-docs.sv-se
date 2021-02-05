---
title: Konturrendering
description: Förklarar hur du gör markerings kon tur åter givning
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592015"
---
# <a name="outline-rendering"></a>Konturrendering

De valda objekten kan markeras visuellt genom att lägga till dispositions åter givning via [komponenten för åsidosättning av hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md). I det här kapitlet beskrivs hur globala parametrar för dispositions åter givning ändras via klient-API: et.

Dispositions egenskaper är en global inställning. Alla objekt som använder dispositions åter givning använder samma inställning – det går inte att använda en linje färg per objekt.

## <a name="parameters-for-outlinesettings"></a>Parametrar för `OutlineSettings`

Klassen `OutlineSettings` innehåller inställningar som rör globala dispositions egenskaper. Den exponerar följande medlemmar:

| Parameter      | Typ    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Färgen som används för att rita konturen. Alfa delen ignoreras.         |
| `PulseRateHz`    | flyt   | Den hastighet med vilken kon tur variationer per sekund|
| `PulseIntensity` | flyt   | Intensiteten för kon tur pulsen. Måste vara mellan 0,0 för ingen pulsning och 1,0 för hel pulsning. Intensitet anger den minsta opaciteten för konturen som `MinOpacity = 1.0 - PulseIntensity` . |

![Ett objekt som återges tre gånger med olika dispositions parametrar som ](./media/outlines.png) används för att ändra `color` parametern från gult (vänster) till Magenta (mitt) och `pulseIntensity` från 0 till 0,8 (höger).

## <a name="example"></a>Exempel

Följande kod visar ett exempel på hur du ställer in dispositions parametrar via API: et:

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Prestanda

Kon tur åter givning kan ha en betydande inverkan på åter givnings prestanda. Den här effekten varierar beroende på förhållandet mellan valda och icke-valda objekt för en viss ram.

## <a name="api-documentation"></a>API-dokumentation

* [C# RenderingConnection. OutlineSettings-egenskap](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection:: OutlineSettings ()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>Nästa steg

* [Åsidosättande komponent för hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md)