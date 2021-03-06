---
title: Så här skapar du ett överlägg med Media Encoder Standard
description: Lär dig hur du skapar ett överlägg med Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490258"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Så här skapar du ett överlägg med Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Media Encoder Standard kan du täcka över en bild, ljudfil eller en annan video till en annan video. Indata måste ange exakt en fil. Du kan ange en bildfil i JPG-, PNG-, GIF-eller BMP-format eller en ljudfil (till exempel WAV-, MP3-, WMA-eller M4A-fil) eller en videofil.


## <a name="prerequisites"></a>Förutsättningar

* Samla in den konto information som du behöver för att konfigurera *appsettings.jspå* filen i exemplet. Om du inte är säker på hur du gör det, se [snabb start: registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Följande värden förväntas i *appsettings.jsi* filen.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Om du inte redan är bekant med transformationer, rekommenderar vi att du utför följande aktiviteter:

* Läs [kodning av video och ljud med Media Services](encode-concept.md)
* Läs [hur du kodar med en anpassad transformering – .net](transform-custom-presets-how-to.md). Följ anvisningarna i artikeln för att konfigurera .NET som krävs för att arbeta med transformeringar och gå sedan tillbaka hit för att testa ett för inställnings exempel för överlägg.
* Se [transformerings-referens dokumentet](/rest/api/media/transforms).

När du är bekant med transformationer kan du hämta exempel på överlägg.

## <a name="overlays-preset-sample"></a>Överlägg för Förvals exempel

Hämta [exemplet med medie tjänster – överlägg](https://github.com/Azure-Samples/media-services-overlays) för att komma igång med överlägg.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
