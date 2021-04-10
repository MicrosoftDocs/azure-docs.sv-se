---
title: Registrera dig för Video Indexer och ladda upp din första video – Azure
titleSuffix: Azure Media Services
description: Lär dig hur du registrerar dig och laddar upp din första video med hjälp av Video Indexer-portalen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 5b38c731db141052e6700472020cd60b6a4d13a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797805"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Snabb start: så här registrerar du dig och laddar upp din första video

Den här snabb starten visar hur du loggar in på Video Indexer webbplats och hur du laddar upp din första video.

När du skapar ett Video Indexer-konto kan du välja ett kostnadsfritt utvärderingskonto (där du får ett visst antal kostnadsfria indexeringsminuter) eller ett betalalternativ (där du inte begränsas av kvoten). Med den kostnadsfria utvärderingen ger Video Indexer upp till 600 minuter kostnadsfri indexering för webbplatsanvändare och upp till 2 400 minuter kostnadsfri indexering för API-användare. Med betalalternativet skapar du ett Video Indexer-konto som är [anslutet till din Azure-prenumeration och ett Azure Media Services-konto](connect-to-azure.md). Du betalar för minuter indexerat, mer information finns i [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-indexer"></a>Registrera dig för Video Indexer

När du vill börja utveckla med Video Indexer går du till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och registrerar dig.

När du börjar använda Video Indexer krypteras alla lagrade data och överfört innehåll i vila med en Microsoft-hanterad nyckel.

> [!NOTE]
> Granska de [planerade video Indexer-webbplatsens authenticatication ändringar](release-notes.md#planned-video-indexer-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-indexer-website"></a>Ladda upp en video med hjälp av Video Indexer-webbplatsen

### <a name="supported-file-formats-for-video-indexer"></a>Fil format som stöds för Video Indexer

I artikeln om [inmatade behållare/fil format](../latest/media-encoder-standard-formats.md#input-containerfile-formats) finns en lista över fil format som du kan använda med video Indexer.

### <a name="upload-a-video"></a>Ladda upp en video

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen.
1. Ladda upp en video genom att trycka på knappen eller länken **Ladda upp**.

    > [!NOTE]
    > Namnet på videon får inte vara större än 80 tecken.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Överför":::
1. När videon har laddats upp påbörjar Video Indexer indexering och analys av videon. Du ser förloppet. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Överförings förlopp":::
1. När Video Indexer har analyser ATS får du ett e-postmeddelande med en länk till din video och en kort beskrivning av vad som hittades i videon. Exempel: personer, talade och skrivna ord, ämnen och namngivna entiteter.
1. Du kan senare hitta din video i biblioteks listan och utföra olika åtgärder. Exempel: Sök, indexera om, redigera.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Överförde överföringen":::

## <a name="supported-browsers"></a>Webbläsare som stöds

Mer information finns i [webbläsare som stöds](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Se även

Mer information finns i [Ladda upp och indexera videor](upload-index-videos.md) .

När du har laddat upp och indexerat en video kan du börja använda [video Indexer webbplats](video-indexer-view-edit.md) eller [video Indexer Developer-portalen](video-indexer-use-apis.md) för att se insikterna för videon. 

[Börja använda API: er](video-indexer-use-apis.md)

## <a name="next-steps"></a>Nästa steg

Detaljerad introduktion finns i [introduktions labbet](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

I slutet av workshopen får du en god förståelse för vilken typ av information som kan extraheras från video-och ljud innehåll, men du är mer beredd på att identifiera affärs möjligheter som rör innehålls intelligens, öka video AI på Azure och demonstrera flera scenarier på Video Indexer.

