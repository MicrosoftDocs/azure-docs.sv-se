---
title: Koda en till gång genom att använda Media Encoder Standard i Azure Portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att koda en till gång genom att använda Media Encoder Standard i Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010515"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Koda en tillgång med hjälp av Media Encoder Standard i Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ett av de vanligaste scenarierna när du arbetar med Azure Media Services levererar direkt uppspelning med anpassningsbar bit hastighet till dina klienter. Media Services stöder följande strömmande tekniker med anpassningsbar bit hastighet: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och dynamisk anpassad strömning via HTTP (tank streck, även kallat MPEG-streck). För att förbereda dina videor för strömning med anpassningsbar bit hastighet måste du först koda din käll video som filer med flera bit hastigheter. Du kan använda Media Encoder Standard för att koda dina videor.  

Media Services ger dig dynamisk paketering. Med dynamisk paketering kan du leverera Multibits-hastigheter i HLS, Smooth Streaming och MPEG-streck, utan att packa om dessa strömmande format. När du använder dynamisk paketering kan du lagra och betala för filerna i ett enda lagrings format. Media Services skapar och hanterar lämpligt svar baserat på en klients begäran.

Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. Kodnings stegen visas längre fram i den här artikeln.

Information om hur du skalar medie bearbetning finns i [skala medie bearbetning med hjälp av Azure Portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koda i Azure Portal

För att koda ditt innehåll med hjälp av Media Encoder Standard:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **till gångar**. Välj den tillgång som du vill koda.
3. Välj knappen **Koda**.
4. I fönstret **Koda en tillgång** väljer du processorn **Media Encoder Standard** och en förinställning. Information om förinställningar finns i [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Autogenerera en bithastighetsstege) och [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Uppgiftsförinställningar för Media Encoder Standard). Det är viktigt att välja den förinställning som fungerar bäst för indatavideon. Om du till exempel vet att indatavideon har en upplösning på 1 920 &#215; 1 080 bildpunkter kan du välja förinställningen **H264 multibithastighet 1080p**. Om du har en video med låg upplösning (640 &#215; 360) ska du inte använda förinställningen **H264 multibithastighet 1080p**.
   
   Du kan underlätta hanteringen av resurserna genom att redigera namnet på utdatatillgången och namnet på jobbet.
   
   ![Koda tillgångar](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Välj **Skapa**.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Övervaka förloppet för kodnings jobbet](media-services-portal-check-job-progress.md) i Azure Portal.  

