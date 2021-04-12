---
title: Skala medie bearbetning med Azure Portal | Microsoft Docs
description: I den här självstudien får du stegvisa anvisningar om hur du skalar medie bearbetning med hjälp av Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 49c3899b912a88605e9269cdb1c34c7e18ed5247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009733"
---
# <a name="change-the-reserved-unit-type"></a>Ändra den reserverade enhetstypen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Översikt

Ett Media Services-konto är kopplat till en typ av reserverad enhet som bestämmer hur snabbt mediebearbetningsuppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**.

Förutom att ange typ av reserverad enhet kan du ange att ditt konto ska etableras med **reserverade enheter** (ru: er). Antalet etablerade RU:er anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

>[!NOTE]
>RU:er fungerar för parallellisera all bearbetning av media, inklusive indexeringsjobb med hjälp av Azure Media Indexer. Men till skillnad från kodning bearbetas inte indexeringsjobb snabbare med snabbare reserverade enheter.

> [!IMPORTANT]
> Läs igenom [översikten](media-services-scale-media-processing-overview.md) om du vill ha mer information om hur du skalar medie bearbetnings ämne.
> 
> 

## <a name="scale-media-processing"></a>Skala medie bearbetning
Om du vill ändra typen av reserverad enhet och antalet reserverade enheter gör du följande:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** väljer du **Media reserverade enheter**.
   
    Om du vill ändra antalet reserverade enheter för den valda reserverade enhets typen använder du skjutreglaget **medie enheter** överst på skärmen.
   
    Om du vill ändra **typen av reserverad enhet** klickar du på **hastigheten för reserverat bearbetnings enhets** fält. Välj sedan den pris nivå som du behöver: S1, S2 eller S3.
   
3. Tryck på knappen SPARA för att spara ändringarna.
   
    De nya reserverade enheterna tilldelas när du trycker på Spara.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
