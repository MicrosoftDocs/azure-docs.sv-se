---
title: Utrymme för steg
description: Beskriver steg utrymmes inställningar och användnings fall
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594018"
---
# <a name="stage-space"></a>Utrymme för steg

När du kör ARR på en enhet som tillhandahåller huvud spårnings data, t. ex. HoloLens 2, skickas Head-attityden till både användar programmet och servern. Utrymmet där Head-transformeringen definieras i kallas för *scen utrymmet*.

Om du vill justera lokalt och fjärrinnehållet förutsätts det att det mellanliggande utrymmet och hela utrymmet är identiska på både klienten och servern. Om användaren bestämmer sig för att lägga till en ytterligare omvandling ovanpå kameran måste den skickas till-servern och kunna justera lokalt och fjärrinnehållet på rätt sätt.

Vanliga orsaker till att flytta mellanlagrings utrymmet är [världs lås verktyg](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) eller andra verkliga funktioner för världs omslutning samt att flytta ett virtuellt tecken i VR.

## <a name="stage-space-settings"></a>Steg utrymmes inställningar

> [!CAUTION]
> EXPERIMENT: den här funktionen är experimentell och kommer att ändras med tiden. Därför kan det krävas ytterligare arbete för att uppgradera koden om du uppdaterar till nyare klient-SDK-versioner. Den aktuella implementeringen avbryter en kort stund för lokal/fjärrstyrd innehålls justering när du ändrar scen utrymmets ursprung.
Därför är den för närvarande endast avsedd att användas i världs låsnings syfte som ankare som bara visar mycket små förändringar över tid.

För att informera servern om att en ytterligare transformering används för scen utrymmet måste dess ursprung som definieras av en position och en rotation i mitt utrymme skickas. Den här inställningen kan nås via *inställningen för scen utrymme*.

> [!IMPORTANT]
> I [simuleringen av Skriv bordet](../../concepts/graphics-bindings.md) tillhandahålls enhetens lagrings plats i användar programmet. I det här fallet måste du hoppa över det mellanliggande lagrings utrymmet innan det redan har multiplicerats med kamera omvandlingen.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Skript för Unity Stage Space

Union-integreringen innehåller ett skript med namnet **StageSpace** som kan läggas till i kamerans överordnade GameObject. När det här skriptet är tillgängligt tar det hand om att ange det mellanliggande utrymmet.

## <a name="next-steps"></a>Nästa steg

* [Grafik bindning](../../concepts/graphics-bindings.md)
* [Omprojektion av sena steg](late-stage-reprojection.md)
