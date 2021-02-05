---
title: Rendering av felsökning
description: Översikt över åter givnings effekter på Server Sidan
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591743"
---
# <a name="debug-rendering"></a>Rendering av felsökning

API för fel söknings åter givning innehåller en uppsättning globala alternativ för att ändra åter givning på Server sidan med olika fel söknings effekter.

## <a name="available-debug-rendering-effects"></a>Tillgängliga fel söknings åter givnings effekter

|Inställning                          | Effekt                               |
|---------------------------------|:-------------------------------------|
|Bild Rute räknare                    | Återger ett text överlägg i det övre vänstra hörnet av ramen. Texten visar det aktuella ram-ID: t för Server sidan, som kontinuerligt ökar när åter givningen fortsätter. |
|Antal polygoner                    | Återger ett text överlägg i det övre vänstra hörnet av ramen. Texten visar den aktuella åter givnings mängden av polygoner, samma värde som efter frågas av [prestanda frågor på Server sidan](performance-queries.md)| 
|Tråd block                        | Om aktive rad kommer all objekt geometri som läses in på servern att återges i tråd Rams läge. Endast kanterna på polygoner rastreras i det här läget. |

Följande kod aktiverar dessa fel söknings effekter:

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Fel söknings åter givning](./media/debug-rendering.png)

> [!NOTE]
> Alla effekter för fel söknings åter givning är globala inställningar som påverkar hela ramen.

## <a name="use-cases"></a>Användningsfall

API för fel söknings åter givning är avsett för enkla fel söknings åtgärder, som att verifiera att tjänst anslutningen faktiskt är igång och körs korrekt. Alternativen för text åter givning påverkar de nedströms video bild rutorna direkt. Om du aktiverar dem verifieras om nya ramar tas emot och video-avkodas korrekt.

De tillhandahållna effekterna ger dock ingen detaljerad introspektionsfunktionerna till tjänstens hälsa. [Prestanda frågorna på Server sidan](performance-queries.md) rekommenderas för det här användnings fallet.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

* Att aktivera text överlägg innebär lite till ingen prestanda.
* Aktivering av tråd Rams läge medför en icke-trivial prestanda, men det kan variera beroende på scenen. För komplexa scener kan det här läget orsaka att bild frekvensen släpps under 60-Hz-målet.

## <a name="api-documentation"></a>API-dokumentation

* [C++ RenderingConnection::D ebugRenderingSettings ()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Nästa steg

* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Prestandafrågor på serversidan](performance-queries.md)