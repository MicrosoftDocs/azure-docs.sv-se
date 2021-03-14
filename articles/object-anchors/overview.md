---
title: Översikt över Azure Object ankare
description: Lär dig hur Azure Object ankare hjälper dig att identifiera objekt i den fysiska världen.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: b4284411fbe09a0d0ce8412e6e5a5df464f35564
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462872"
---
# <a name="azure-object-anchors-overview"></a>Översikt över Azure Object ankare

Objekt ankare i Azure gör det möjligt för ett program att identifiera ett objekt i den fysiska världen med en 3D-modell och uppskatta dess 6DoF-attityd. Attityden 6DoF (6 frihets grader) definieras som en rotation och en översättning mellan en 3D-modell och dess fysiska motsvarighet, det faktiska objektet.

Objekt ankare i Azure består av en tjänst för modell konvertering och en runtime-klient-SDK för HoloLens. Tjänsten accepterar en 3D-objektmodellen och matar ut en modell för objekt ankare i Azure. Modell för objekt ankare i Azure används tillsammans med Runtime SDK för att göra det möjligt för ett HoloLens-program att läsa in en objekt modell, identifiera och spåra instanser av den modellen i den fysiska världen.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Azure objekt ankare i praktiken":::

## <a name="examples"></a>Exempel

Exempel på användnings områden som aktive ras av Azure Object ankare är:

- **Träning**. Skapa utbildnings upplevelser i Mixad verklighet för dina anställda, utan att behöva placera markörer eller ägna tid åt att justera hologram justeringen manuellt. Om du vill utöka inlärnings upplevelsen för blandade verklighet med automatiserad identifiering och spårning kan du mata in din modell i Azure Object Ankares-tjänsten och du får ett steg närmare en markör lös upplevelse.

- **Uppgifts vägledning**. Att flytta anställda genom en uppsättning aktiviteter kan vara avsevärt förenklade när du använder Mixad verklighet. Att täcka digitala instruktioner och bästa praxis, ovanpå det fysiska objektet – bli en del av maskinerna på en fabriks våning eller en kaffe tillverkare i teamets kök – kan avsevärt minska svårigheterna med att slutföra uppsättningen uppgifter. Att utlösa dessa upplevelser kräver vanligt vis någon form av markör eller manuell justering, men med Azures objekt ankare kan du skapa en upplevelse som automatiskt identifierar objektet som är relaterat till den aktuella aktiviteten. Sedan kan du sömlöst flöda genom vägledning för blandad verklighet utan markörer eller manuell justering.

- **Hitta till gång**. Om du redan har en 3D-modell för ett objekt i det fysiska utrymmet kan du med Azures objekt ankare hitta och spåra instanser av objektet i din fysiska miljö.

## <a name="next-steps"></a>Nästa steg

I följande avsnitt finns information om hur du kommer igång med att använda och skapa appar med Azures objekt ankare.

> [!div class="nextstepaction"]
> [Modell inmatning](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Uniting HoloLens med MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
