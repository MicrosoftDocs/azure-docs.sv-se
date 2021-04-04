---
title: Åsidosätta hierarkiskt tillstånd
description: Förklarar begreppet hierarkiskt tillstånd för åsidosättning av komponenter.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94445162"
---
# <a name="hierarchical-state-override"></a>Åsidosätta hierarkiskt tillstånd

I många fall är det nödvändigt att ändra utseendet på delar i en [modell](../../concepts/models.md)dynamiskt, till exempel dölja under diagram eller byta delar till genomskinlig åter givning. Att ändra material för varje del som ingår är inte praktiskt eftersom det måste iterera över hela scenen och hantera material kloning och tilldelning på varje nod.

Använd för att göra det här användnings fallet med minsta möjliga belastning `HierarchicalStateOverrideComponent` . Den här komponenten implementerar hierarkiska tillstånds uppdateringar på godtyckliga grenar i scen diagrammet. Det innebär att ett tillstånd kan definieras på vilken nivå som helst i scen diagrammet och det trickles nedåt i hierarkin tills den antingen åsidosätts av ett nytt tillstånd eller tillämpas på ett löv objekt.

Du kan till exempel överväga modellen för en bil och du vill byta hela bilen så att den blir genomskinlig, förutom den inre motor delen. Det här användnings fallet omfattar bara två instanser av komponenten:

* Den första komponenten tilldelas modellens rotnod och aktiverar transparent åter givning för hela bilen.
* Den andra komponenten tilldelas till motorns rotnod och åsidosätter statusen igen genom att uttryckligen stänga av se--läge.

## <a name="features"></a>Funktioner

Den fasta uppsättning tillstånd som kan åsidosättas är:

* **`Hidden`**: Respektive maskor i scen diagrammet är dolda eller visas.
* **`Tint color`**: Ett renderat objekt kan färgtonas med dess enskilda färg och färgton. Bilden nedan visar färg tonens RIM i ett hjul.
  
  ![Färg tons färg som används för att omvandla ett objekt till grönt](./media/color-tint.png)

* **`See-through`**: Geometrin återges som överordnad, till exempel för att visa de inre delarna av ett objekt. Följande bild visar hela bilen som återges i se-läge, förutom den röda bromsen Caliper:

  ![Se läge som används för att göra markerade objekt transparenta](./media/see-through.png)

  > [!IMPORTANT]
  > Alternativet för att se igenom fungerar bara när *TileBasedComposition* [åter givnings läge](../../concepts/rendering-modes.md) används.

* **`Shell`**: Geometrin återges som ett transparent, inmätt gränssnitt. Det här läget gör det möjligt att tona bort icke-viktiga delar av en scen och samtidigt behålla en uppfattning om form och relativ placering. Om du vill ändra skal åter givnings utseendet använder du [ShellRenderingSettings](shell-effect.md) -läget. Se följande bild för bilens modell som helt återges, förutom de blå fjädrarna:

  ![Shell-läge som används för att tona ut vissa objekt](./media/shell.png)

  > [!IMPORTANT]
  > Gränssnitts effekterna fungerar endast när *TileBasedComposition* [åter givnings läge](../../concepts/rendering-modes.md) används.

* **`Selected`**: Geometrin återges med en [markerings disposition](outlines.md).

  ![Alternativet disposition som används för att markera en markerad del](./media/selection-outline.png)

* **`DisableCollision`**: Geometrin är undantagen från [rums frågor](spatial-queries.md). **`Hidden`** Flaggan påverkar inte flaggan kollisioner, så dessa två flaggor anges ofta tillsammans.

* **`UseCutPlaneFilterMask`**: Använd en enskild filter bit mask för att kontrol lera det avskurna planet valet. Den här flaggan avgör om den enskilda filter masken ska användas eller ärvas från den överordnade. Själva filtrets bitmask anges via `CutPlaneFilterMask` egenskapen. Detaljerad information om hur filtreringen fungerar finns i [stycket selektivt avskurna plan](cut-planes.md#selective-cut-planes). Se följande exempel där endast Tire och RIM klipps ut medan resten av scenen förblir opåverkade.
![Selektiva klipp ut plan](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Som ett alternativ till att inaktivera Synlighets-och rums frågor för ett fullständigt under diagram `enabled` kan status för ett spel objekt växlas. Om en hierarki är inaktive rad har detta företräde `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Hierarkiska åsidosättningar

`HierarchicalStateOverrideComponent`Kan kopplas på flera nivåer i en Object-hierarki. Eftersom det bara kan finnas en komponent av varje typ på en entitet, hanterar var och en av `HierarchicalStateOverrideComponent` de olika tillstånden för dolda, se-genom, valda, färg nyanser och kollisioner.

Varje tillstånd kan därför ställas in på något av följande:

* `ForceOn` -tillstånd är aktiverat för alla nät på och under den här noden
* `ForceOff` -statusen är inaktive rad för alla nät på och under den här noden
* `InheritFromParent` -statusen påverkas inte av den här åsidosättning-komponenten

Du kan ändra tillstånd direkt eller via `SetState` funktionen:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Färgton

`tint color`Åsidosättningen är något speciellt i att det finns både ett på/av/på/av/Ärv-tillstånd och en färgad egenskap. Alfa delen av färg tons färgen definierar viktningen för tonings effekterna: om värdet är 0,0 visas inte färg tonen och om värdet är 1,0 kommer objektet att återges med en ren färgton-färg. För in-mellan-värden kommer den slutliga färgen att blandas med färg tonen. Färg tons färgen kan ändras per bild punkt för att uppnå en färganimering.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

En instans av `HierarchicalStateOverrideComponent` sig lägger inte till mycket körnings kostnader. Men det är alltid bra att hålla antalet aktiva komponenter låga. Till exempel, när du implementerar ett urvals system som markerar det valda objektet, rekommenderar vi att du tar bort komponenten när markeringen tas bort. Att hålla komponenterna runt med neutrala funktioner kan snabbt lägga till.

Transparent åter givning placerar mer arbets belastning på serverns GPU än standard åter givning. Om stora delar av scen diagrammet är växlat för att *Visa genom* att många lager av geometrin är synliga, kan det bli en Flask hals i prestandan. Samma sak gäller för objekt med [markerings kon tur](../../overview/features/outlines.md#performance) och för [skal åter givning](../../overview/features/shell-effect.md#performance) . 

## <a name="api-documentation"></a>API-dokumentation

* [C# HierarchicalStateOverrideComponent-klass](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ HierarchicalStateOverrideComponent-klass](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Nästa steg

* [Beskrivs](../../overview/features/outlines.md)
* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Spatiala frågor](../../overview/features/spatial-queries.md)