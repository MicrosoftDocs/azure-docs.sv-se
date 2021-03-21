---
title: Så här skapar du en medie processor med Azure Media Services SDK för .NET | Microsoft Docs
description: Lär dig hur du skapar en medie processor komponent för att koda, konvertera format, kryptera eller dekryptera medie innehåll för Azure Media Services. Kod exempel skrivs i C# och använder Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013354"
---
# <a name="how-to-get-a-media-processor-instance"></a>Gör så här: Hämta en Media processor instans

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Översikt

I Media Services en medie processor en komponent som hanterar en speciell bearbetnings aktivitet, till exempel kodning, format konvertering, kryptering eller dekryptering av medie innehåll. Du skapar vanligt vis en medie processor när du skapar en aktivitet för att koda, kryptera eller konvertera formatet för medie innehåll.

## <a name="azure-media-processors"></a>Azure Media-processorer

Följande avsnitt innehåller listor över medie processorer:

* [Mediebearbetare för kodning](scenarios-and-availability.md)
* [Mediebearbetare för analys](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Hämta medie processor

Följande metod visar hur du hämtar en medie processor instans. I kod exemplet antas användningen av en variabel på modulnivå med namnet **_context** referera till Server kontexten enligt beskrivningen i avsnittet [så här: Anslut till Media Services program mässigt](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du hämtar en medie processor instans går du till avsnittet [så här kodar du ett till gångs](media-services-dotnet-encode-with-media-encoder-standard.md) ämne som visar hur du använder Media Encoder Standard för att koda en till gång.