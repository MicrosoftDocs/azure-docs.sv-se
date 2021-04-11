---
title: Publicera innehåll i Azure Portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att publicera ditt innehåll i Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c22570153200b9daeae44701c814faa1a28916c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009818"
---
# <a name="publish-content-in-the-azure-portal"></a>Publicera innehåll i Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Översikt
> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

För att ge användaren en URL som kan användas för att strömma eller hämta innehållet måste du först publicera tillgången genom att skapa en lokaliserare. Lokaliserare ger åtkomst till till gångs filer. Azure Media Services stöder två typer av lokaliserare: 

* **Strömningslokaliserare (OnDemandOrigin)**. Strömningslokaliserare används för anpassad strömning. Exempel på anpassningsbar direkt uppspelning inkluderar Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och dynamisk adaptiv strömning via HTTP (streck, kallas även MPEG-streck). Om du vill skapa en strömningslokaliserare måste tillgången innehålla en .ism-fil. Till exempel `http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest`.
* **Progressiv lokaliserare (signatur för delad åtkomst)**. Progressiva lokaliserare används för att leverera video via progressiv nedladdning.

Om du vill bygga en HLS streaming-URL lägger du till *(format = M3U8-AAPL)* till URL: en:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Om du vill skapa en strömnings-URL som ska spela upp Smooth Streaming-tillgångar använder du följande URL-format:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest`

Om du vill skapa en strömnings-URL för MPEG-DASH lägger du till *(format=mpd-time-csf)* i URL:en:

`{streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

En URL för signatur för delad åtkomst har följande format:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

Mer information finns i [Översikt över att leverera innehåll](media-services-deliver-content-overview.md).

> [!NOTE]
> Lokaliserare som har skapats i Azure-portalen före mars 2015 har ett utgångsdatum på två år.  
> 
> 

Om du vill uppdatera ett förfallo datum på en positionerare kan du använda en [REST API](/rest/api/media/operations/locator#update_a_locator) eller ett [.NET-API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator). 

> [!NOTE]
> URL:en ändras när du uppdaterar utgångsdatumet för en SAS-lokaliserare.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Använda portalen för att publicera en tillgång
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **till gångar**. Välj den tillgång som du vill publicera.
3. Välj sedan knappen **Publicera**.
4. Välj typ av lokaliserare.
5. Välj **Lägg till**.
   
    ![Publicera videon](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL:en läggs till i listan över **publicerade URL:er**.

## <a name="play-content-in-the-portal"></a>Spela upp innehåll i portalen
Du kan testa videon på en innehållsspelare i Azure-portalen.

Välj videon och välj sedan knappen **Spela upp**.

![Spela upp videon i Azure-portalen](./media/media-services-portal-vod-get-started/media-services-play.png)

Vissa förutsättningar gäller:

* Kontrollera att videon har publicerats.
* Azure-portalens mediaspelare spelar upp från den slutpunkt för direktuppspelning som är standard. Klicka för att kopiera URL:en och klistra in den i en annan spelare om du vill spela upp från en slutpunkt för direktuppspelning som inte är standard. Du kan till exempel testa videon i [Azure Media Player](https://aka.ms/azuremediaplayer).
* Den strömnings slut punkt som du strömmar från måste köras.  

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]