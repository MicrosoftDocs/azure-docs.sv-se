---
title: Serverstorlekar
description: Beskriver de distinkta Server storlekar som kan tilldelas
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594408"
---
# <a name="server-sizes"></a>Serverstorlekar

Azure Remote rendering är tillgängligt i två serverkonfigurationer: `Standard` och `Premium` .

## <a name="polygon-limits"></a>Polygon-gränser

Fjärrrendering med `Standard` storleks servern har en maximal scen storlek på 20 000 000 polygoner. Fjärrrendering med `Premium` storlek upprätthåller inte en maximal hård gräns, men prestandan kan försämras om innehållet överskrider tjänstens åter givnings funktioner.

När åter givningen på en standard server storlek träffar den här begränsningen växlar den åter givning till en schack brädets bakgrund:

![Skärm bild som visar ett rutnät med svarta och vita fyr kanter med en verktyg-meny.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Ange server storleken

Önskad typ av Server konfiguration måste anges vid initierings tiden för åter givnings sessionen. Det går inte att ändra i en pågående session. I följande kod exempel visas den plats där Server storleken måste anges:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

För [PowerShell-skripten](../samples/powershell-example-scripts.md)måste önskad server storlek anges i `arrconfig.json` filen:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Hur renderaren utvärderar antalet polygoner

Antalet polygoner som beaktas för begränsnings testet är antalet polygoner som faktiskt skickas till åter givningen. Den här geometrin är vanligt vis summan av alla instansierade modeller, men det finns också undantag. Följande geometri **ingår inte**:
* Inlästa modell instanser som är helt utanför vyn Frustum.
* Modeller eller modell delar som har växlats till osynlig, med hjälp av komponenten för att [åsidosätta hierarkiska tillstånd](../overview/features/override-hierarchical-state.md).

Därför är det möjligt att skriva ett program som är riktat mot den `standard` storlek som läser in flera modeller med ett antal polygoner nära gränsen för varje enskild modell. När programmet bara visar en enskild modell i taget utlöses inte schack bräden.

### <a name="how-to-determine-the-number-of-polygons"></a>Så här fastställer du antalet polygoner

Det finns två sätt att fastställa antalet polygoner i en modell eller scen som bidrar till `standard` konfigurations storlekens budget gräns:
* På sidan modell konvertering hämtar du JSON- [filen för konvertering av utdata](../how-tos/conversion/get-information.md)och kontrollerar `numFaces` posten i [avsnittet *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Om ditt program hanterar dynamiskt innehåll kan antalet åter givnings bara polygoner efter frågas dynamiskt under körning. Använd en [utvärderings fråga för prestanda](../overview/features/performance-queries.md#performance-assessment-queries) och kontrol lera `polygonsRendered` medlemmen i `FrameStatistics` struct. `PolygonsRendered`Fältet ställs in på `bad` när åter givningen träffar i polygonens begränsning. Schack brädets bakgrund är alltid blek i en viss fördröjning för att se till att användar åtgärder kan vidtas efter den asynkrona frågan. Användar åtgärd kan till exempel dölja eller ta bort modell instanser.

## <a name="pricing"></a>Priser

En detaljerad beskrivning av prissättningen för varje typ av konfiguration finns på [prissättnings sidan för Fjärråter givning](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Nästa steg
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)
* [Modell konvertering](../how-tos/conversion/model-conversion.md)

