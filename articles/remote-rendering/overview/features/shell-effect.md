---
title: Rendering av gränssnitt
description: Förklarar hur du använder skal åter givnings effekter
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594171"
---
# <a name="shell-rendering"></a>Rendering av gränssnitt

Shell-statusen för [komponenten för åsidosättning av hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md) är en genomskinlighets påverkan. I motsats till att [Visa genom](../../overview/features/override-hierarchical-state.md) åter givning, är det bara det översta skiktet av objekt som är synligt, via till täckande åter givning. Dessutom kan objekts normala utseende ändras när de återges som gränssnitt. Den här funktionen är avsedd för användnings fall där användaren bör förvaras visuellt från icke-viktiga delar samtidigt som det fortfarande upprätthåller känslig medvetenhet för hela scenen.

Du kan konfigurera utseendet på objekt som återges av gränssnittet via det `ShellRenderingSettings` globala läget. Alla objekt som använder Shell-rendering kommer att använda samma inställning. Det finns inga parametrar per objekt.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings-parametrar

Klassen `ShellRenderingSettings` innehåller inställningar som rör globala skal åter givnings egenskaper:

| Parameter      | Typ    | Beskrivning                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | flyt   | Mängden demättnad som ska användas för den vanliga slutgiltiga objekt färgen, i intervall 0 (ingen demättnad) till 1 (fullständig avmättnad) |
| `Opacity`      | flyt   | Opaciteten för Shell-renderade objekt, i intervall 0 (osynliga) till 1 (helt täckande) |

Se även följande tabell för exempel på Parameters-effekter när de tillämpas på en hel scen:

|                | 0 | 0,25 | 0,5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Frimättnad** | ![Avmättnad – 0,0](./media/shell-desaturation-00.png) | ![Avmättnad – 0,25](./media/shell-desaturation-025.png) | ![Avmättnad – 0,5](./media/shell-desaturation-05.png) | ![Avmättnad – 0,75](./media/shell-desaturation-075.png) | ![Avmättnad – 1,0](./media/shell-desaturation-10.png) |
| **Ogenomskinlighet**      | ![Opacitet – 0,0](./media/shell-opacity-00.png) | ![Opacitet – 0,25](./media/shell-opacity-025.png) | ![Opacitet – 0,5](./media/shell-opacity-05.png) | ![Opacitet – 0,75](./media/shell-opacity-075.png) | ![Opacitet – 1,0](./media/shell-opacity-10.png) |

Shell-effekt tillämpas på den slutliga täckande färgen som motivet återges med annars. Som innehåller [åsidosättning av hierarkiskt läge för toner](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Exempel

Följande kod visar ett exempel på användningen av `ShellRenderingSettings` tillstånd via API: et:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Prestanda

Funktionen Shell-åter givning har en liten konstant överbelastning i jämförelse med vanlig ogenomskinlig åter givning. Det går betydligt snabbare än att använda transparent material på objekt eller [se genom](../../overview/features/override-hierarchical-state.md) åter givning. Prestandan kan försämras mer starkt om bara delar av scenen växlar till skal åter givning. Den här försämringen kan uppstå på grund av att det även visas objekt som kräver åter givning. I detta hänseende fungerar prestanda på samma sätt som funktionen [klipp plan](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Nästa steg

* [Åsidosättande komponent för hierarkiskt tillstånd](../../overview/features/override-hierarchical-state.md)