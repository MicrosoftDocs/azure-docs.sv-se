---
title: Översikt över Azure Spatial Anchors
description: Lär dig hur Azure Spatial Anchors hjälper dig att utveckla plattformsoberoende upplevelser med mixad verklighet.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0b4c3b281f2eac6263ef578dee5419bd9f2eb8a7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058569"
---
# <a name="azure-spatial-anchors-overview"></a>Översikt över Azure Spatial Anchors

Välkommen till Azure Spatial Anchors. Azure Spatial Anchors ger utvecklare viktiga funktioner för att skapa spatialt medvetna program med mixad verklighet. De här programmen kan ha stöd för Microsoft HoloLens, iOS-baserade enheter med stöd för ARKit och Android-baserade enheter med stöd för ARCore. Azure Spatial Anchors gör det möjligt för utvecklare att arbeta med plattformar för mixad verklighet för att uppfatta utrymmen, ange exakta platser av intresse och återkalla de platserna av intresse från enheter som stöds.
De här exakta platserna av intresse kallas spatiala fästpunkter.

![Plattformsoberoende](./media/cross-platform.png)

## <a name="examples"></a>Exempel

Här är några exempel på användningsfall som möjliggörs av Spatial Anchors:

- [Upplevelser för flera användare](tutorials/tutorial-share-anchors-across-devices.md). Med avstånds ankare för Azure är det enkelt för personer på samma plats att delta i program med mixad Reality i flera användare. Två personer kan till exempel börja spela schack med mixad verklighet genom att placera ett virtuellt schackbräde på ett bord. Genom att rikta sin enhet mot bordet kan de sedan se och interagera med det virtuella schackbrädet tillsammans.

- [Vägledning](concepts/anchor-relationships-way-finding.md). Utvecklare kan också koppla samman spatiala fästpunkter och skapa relationer mellan dem. En app kan exempelvis innehålla en upplevelse med minst två platser av intresse som en användare måste interagera med för att slutföra en uppgift. De platserna av intresse kan skapas med kopplingar. Senare, när användaren genomför uppgiften i flera steg, kan appen be om fästpunkter som finns i närheten av den aktuella för att dirigera användaren till nästa steg i uppgiften.

- [Bevara virtuellt innehåll i verkligheten](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Med en app kan en användare placera en virtuell kalender på väggen i ett konferensrum, som andra kan se med hjälp av en mobilapp eller en HoloLens-enhet. I en industrimiljö kan en användare få sammanhangsbaserad information om en maskin genom att rikta en enhetskamera som stöds mot den.

Azure Spatial Anchors består av en hanterad tjänst och klient-SDK:er för enhetsplattformar som stöds. Följande avsnitt innehåller information om hur du kommer igång med att skapa appar med hjälp av Azure Spatial Anchors.

## <a name="next-steps"></a>Nästa steg

Skapa din första app med de spatiala Ankarena i Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
