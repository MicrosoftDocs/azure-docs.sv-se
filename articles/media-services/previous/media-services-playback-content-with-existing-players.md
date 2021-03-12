---
title: Använd befintliga spelare för att spela upp innehållet – Azure | Microsoft Docs
description: Den här artikeln innehåller befintliga spelare som du kan använda för att spela upp ditt innehåll.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce773adace1baf6db8f1b747643ef0ffdc56a97c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008302"
---
# <a name="playing-your-content-with-existing-players"></a>Spela upp ditt innehåll med befintliga spelare

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services stöder många populära strömnings format, till exempel Smooth Streaming, HTTP Live Streaming och MPEG-streck. Det här avsnittet pekar på befintliga spelare som du kan använda för att testa dina data strömmar.

## <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services innehålls spelare

**Azure** portal tillhandahåller en innehålls spelare som du kan använda för att testa videon.

Klicka på önskad video (se till att den har [publicerats](media-services-portal-publish.md)) och klicka på **uppspelnings** knappen längst ned i portalen.

Vissa förutsättningar gäller:

* **MEDIA SERVICES-INNEHÅLLSSPELAREN** spelar upp från strömningsslutpunkten som är standard. Använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Player](https://aka.ms/azuremediaplayer).

### <a name="azure-media-player"></a>Azure Media Player

Använd [Azure Media Player](https://aka.ms/azuremediaplayer) för att spela upp innehållet (tydligt eller skyddat) i något av följande format:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressiv MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady med token

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>STRECK motspelare

[strecks pelare](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Annat

För att testa HLS-URL: er kan du också använda:

* **Safari** på en iOS-enhet eller
* **3IVX HLS Player** i Windows.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
