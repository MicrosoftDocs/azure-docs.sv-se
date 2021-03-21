---
title: Klippa ut planer
description: Förklarar vad klipp ut plan är och hur du använder dem
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594380"
---
# <a name="cut-planes"></a>Klippa ut planer

Ett *klipp plan* är en visuell funktion som klipper ut pixlar på ena sidan av ett virtuellt plan, och som visar insidan av [maskor](../../concepts/meshes.md).
Bilden nedan visar resultatet. Den vänstra delen visar det ursprungliga nätet, till höger kan se ut i nätet:

![Klipp ut plan](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Du lägger till ett klipp plan till scenen genom att skapa en *CutPlaneComponent*. Planens plats och orientering bestäms av komponentens ägar [enhet](../../concepts/entities.md).

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Egenskaper för CutPlaneComponent

Följande egenskaper visas i en klipp Plans komponent:

* `Enabled`: Du kan tillfälligt stänga av klipp plan genom att inaktivera komponenten. Inaktiverade klipp plan medför inte åter givning av kostnader och räknas inte mot den globala gränsen för klipp ut plan.

* `Normal`: Anger vilken riktning (+ X,-X, + Y,-Y, + Z,-Z) som används som vanlig plan. Den här riktningen är relativ till ägar entitetens orientering. Flytta och rotera entiteten ägare för exakt placering.

* `FadeColor` och `FadeLength`:

  Om alpha-värdet för *FadeColor* är skilt från noll, kommer pixlarna nära det avskurna planet att tonas mot RGB-delen av FadeColor. Styrkan i alfa kanalen avgör om den ska tonas helt mot tonings färgen eller bara delvis. *FadeLength* definierar över vilket avstånd som den här toningen ska äga rum.

* `ObjectFilterMask`: En filter bit mask som avgör vilken geometri som påverkas av det avskurna planet. Se nästa stycke för detaljerad information.

### <a name="selective-cut-planes"></a>Selektiva klipp ut plan

Det är möjligt att konfigurera enskilda stycknings plan så att de bara påverkar specifika geometrier. Följande bild visar hur den här installationen kan se ut i praktiken:

![Selektiva klipp ut plan](./media/selective-cut-planes.png)

Filtreringen fungerar via **logisk bitmask** mellan en bitmask på den klippta planet sidan och en andra Bitmask som är inställd på geometrin. Om resultatet av en logisk `AND` åtgärd mellan maskarna inte är noll, kommer det avskurna planet att påverka geometrin.

* Bit masken på komponenten klipp ut plan anges via dess `ObjectFilterMask` egenskap
* Bitmask i en underordnad hierarki har angetts via [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)

Exempel:

| Klipp ut plan filter mask | Geometri filter mask  | Resultat av logisk `AND` | Klipp ut-planet påverkar geometrin?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Ja |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Ja |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | Inga |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | Inga |

>[!TIP]
> Om du anger ett klipp ut planet `ObjectFilterMask` till 0 så påverkas inte geometrin eftersom resultatet av logiska objekt `AND` aldrig får vara null. Åter givnings systemet tar inte hänsyn till dessa plan på den första platsen, så det är en lättaste metod för att inaktivera enskilda stycknings plan. Dessa stycknings plan räknas inte heller mot gränsen på åtta aktiva plan.

## <a name="limitations"></a>Begränsningar

* Azure-fjärrrendering stöder **maximalt åtta aktiva klipp plan**. Du kan skapa fler klipp komponenter, men om du försöker aktivera fler samtidigt kommer den att ignorera aktiveringen. Inaktivera andra plan först om du vill växla vilka komponenter som ska påverka scenen.
* Klipp ut plan är en helt visuell funktion, de påverkar inte resultatet av [spatiala frågor](spatial-queries.md). Om du vill använda Ray i ett uppdelat nät kan du justera start punkten för Ray-ytan så att den är i det avskurna planet. På så sätt kan Ray bara trycka på synliga delar.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Varje aktivt klipp plan kostar en låg kostnad under åter givningen. Inaktivera eller ta bort klipp plan när de inte behövs.

## <a name="api-documentation"></a>API-dokumentation

* [C# CutPlaneComponent-klass](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent-klass](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Nästa steg

* [Enkels idig rendering](single-sided-rendering.md)
* [Rumsliga frågor](spatial-queries.md)