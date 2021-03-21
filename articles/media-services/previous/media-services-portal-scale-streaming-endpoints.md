---
title: Skala direkt uppspelnings slut punkter med Azure Portal | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att skala slut punkter för direkt uppspelning med Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009767"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skala direktuppspelande slutpunkter med Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

**Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Kunder som har en **Premium**-slutpunkt för direktuppspelning får som standard en direktuppspelande enhet (SU). Du kan skala slutpunkten för direktuppspelning genom att lägga till direktuppspelande enheter. Varje direktuppspelande enhet ger ytterligare bandbreddskapacitet till programmet. Mer information om strömnings slut punkts typer och CDN-konfiguration finns i avsnittet [Översikt över strömnings slut punkt](media-services-streaming-endpoints-overview.md) .
 
I det här avsnittet visas hur du skalar en slut punkt för direkt uppspelning.

Mer information om priser finns i [Prisuppgifter för Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Skala slut punkter för direkt uppspelning

Om du vill ändra antalet enheter för strömning gör du följande:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** väljer du **strömnings slut punkter**.
3. Klicka på den slut punkt för direkt uppspelning som du vill skala. 

    > [!NOTE] 
    > Du kan bara skala slut punkter för direkt uppspelning av **Premium** .

4. Flytta skjutreglaget för att ange antalet enheter för strömning.

    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

