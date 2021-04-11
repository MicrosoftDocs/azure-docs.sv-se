---
title: Så här häftar du två eller fler videofiler med .NET | Microsoft Docs
description: Den här artikeln visar hur du häftar två eller fler videofiler.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111485"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Så här häftar du två eller fler videofiler med .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Häfta ihop två eller fler videofiler

I följande exempel visas hur du kan generera en för inställning för att häfta ihop två eller fler videofiler. Det vanligaste scenariot är när du vill lägga till ett sidhuvud eller en släpvagn i huvud videon.

> [!NOTE]
> Videofiler som redige ras ska dela egenskaper (video upplösning, bild Rute frekvens, antal ljud spår osv.). Du bör inte vara försiktig med att blanda videor med olika bild Rute hastigheter eller med olika antal ljud spår.

## <a name="prerequisites"></a>Förutsättningar

Klona eller hämta [Media Services .net-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Den kod som refereras nedan finns i [mappen EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>.NET-kod

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
