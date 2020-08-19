---
title: Renderingsmodeller
description: Beskriver de olika åter givnings lägena på Server Sidan
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 385086bd46145be717ee5a14a10f889ec811e17e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505659"
---
# <a name="rendering-modes"></a>Renderingsmodeller

Fjärrrendering erbjuder två huvudsakliga drift lägen, **TileBasedComposition** läge och **DepthBasedComposition** läge. Dessa lägen avgör hur arbets belastningen distribueras över flera GPU: er på servern. Läget måste anges vid anslutnings tillfället och kan inte ändras under körning.

Båda lägena har fördelar, men även med funktioner för inbyggd funktion, så att du väljer det mest lämpliga läget är användnings fall.

## <a name="modes"></a>Lägen

De två lägena diskuteras nu i detalj.

### <a name="tilebasedcomposition-mode"></a>"TileBasedComposition"-läge

I **TileBasedComposition** -läge återger varje involverad GPU vissa underrektanglar (paneler) på skärmen. Huvud-GPU: n skapar den slutliga bilden från panelerna innan den skickas som en video bild ruta till klienten. Därför kräver alla GPU: er samma uppsättning resurser för åter givning, så de inlästa till gångarna måste anpassas till en enda GPU-minne.

Åter givnings kvaliteten i det här läget är något bättre än i **DepthBasedComposition** -läge eftersom MSAA kan arbeta med en fullständig uppsättning geometri för varje GPU. Följande skärm bild visar att kant utjämning fungerar korrekt för båda kanterna på samma sätt:

![MSAA i TileBasedComposition](./media/service-render-mode-quality.png)

Dessutom kan varje del i det här läget växlas till ett genomskinligt material eller växlas för att **se igenom** läget via [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>"DepthBasedComposition"-läge

I **DepthBasedComposition** -läge återges alla berörda GPU: n i full skärmupplösning, men endast en delmängd av nät. Den slutliga bild kompositionen på den primära GPU: n tar hänsyn till att delar är korrekt sammanfogade utifrån deras djup information. Naturlig minnes nytto Last fördelas över GPU: er, vilket möjliggör åter givning av modeller som inte passar in i en enda GPU-minne.

Varje enskild GPU använder MSAA för lokalt innehåll i ett alias. Det kan dock finnas inbyggda alias mellan kanter från distinkta GPU: er. Den här åtgärden begränsas av postprocessing den slutliga bilden, men MSAA-kvaliteten är fortfarande sämre än i **TileBasedComposition** -läge.

MSAA-artefakter illustreras i följande bild: ![ MSAA i DepthBasedComposition](./media/service-render-mode-balanced.png)

Kant utjämning fungerar korrekt mellan Sculpture och kulisserna eftersom båda delarna återges på samma GPU. Å andra sidan visar kanten mellan kulisserna och väggen vissa alias eftersom dessa två delar består av olika GPU: er.

Den största begränsningen i det här läget är, att geometri delar inte kan växlas till ett transparent material dynamiskt eller att det inte går att använda läget **Se** till för [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Andra egenskaper för tillstånds åsidosättning (kontur, färg nyanser,...) fungerar, men. Material som marker ATS som transparent vid konverteringen fungerar korrekt i det här läget.

### <a name="performance"></a>Prestanda

Prestanda egenskaperna för båda lägena varierar beroende på användnings fallet och det är svårt att motivera eller tillhandahålla allmänna rekommendationer. Om du inte är begränsad av de begränsningar som anges ovan (minne eller genomskinlighet/se) rekommenderar vi att du provar båda lägena och övervakar prestandan med olika kamera positioner.

## <a name="setting-the-render-mode"></a>Ange åter givnings läge

Åter givnings läget som används på en server för fjärrrendering anges under `AzureSession.ConnectToRuntime` via `ConnectToRuntimeParams` .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Nästa steg

* [Sessioner](../concepts/sessions.md)
* [Åsidosättande komponent för hierarkiskt tillstånd](../overview/features/override-hierarchical-state.md)
