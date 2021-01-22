---
title: Komma igång med att leverera video på begäran med hjälp av Azure Portal | Microsoft Docs
description: De här självstudierna visar dig stegen för att implementera ett grundläggande leveransprogram för Video-on-Demand-innehåll med Azure Media Services-appen i Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0cc3e26895f6bd11ba4f2e5f0b04e4dd36a4fcab
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695299"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Kom igång med att leverera innehåll på begäran med hjälp av Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

De här självstudierna visar dig stegen för att implementera ett grundläggande leveransprogram för Video-on-Demand-innehåll med Azure Media Services-appen i Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att kunna genomföra vägledningen:

* Ett Azure-konto. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Ett Media Services-konto. Information om hur du skapar ett Media Services-konto finns i [Så här skapar du ett Media Services-konto](media-services-portal-create-account.md).

Vägledningen innehåller följande uppgifter:

1. Starta slutpunkten för direktuppspelning.
2. Överföra en videofil.
3. Kodar källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.
4. Publicera tillgången och få URL:er för strömning och progressiv nedladdning.  
5. Spela upp ditt innehåll.

## <a name="start-the-streaming-endpoint"></a>Starta slutpunkten för direktuppspelning

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera video via direktuppspelning med anpassningsbar bithastighet. Media Services ger dig dynamisk paketering. Med dynamisk paketering kan du leverera MP4-kodat innehåll med anpassad bithastighet i just-in-time-strömningsformat som stöds av Media Services. Exempel är bland annat Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming och Dynamic Adaptive Streaming over HTTP (DASH, kallas även MPEG-DASH). Med hjälp av Media Services-direktuppspelning med anpassningsbar bithastighet kan du leverera videor utan att lagra förpaketerade versioner av vart och ett av dessa strömningsformat.

> [!NOTE]
> När du skapar Media Services-kontot läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill strömma innehåll från ha tillståndet **Körs**. 

Starta slutpunkten för direktuppspelning:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **strömnings slut punkter**. 
3. Välj den slutpunkt för direktuppspelning som är standard. Fönstret **INFORMATION OM DEN SLUTPUNKT FÖR DIREKTUPPSPELNING SOM ÄR STANDARD** visas.
4. Välj ikonen **Start**.
5. Välj knappen **Spara**.

## <a name="upload-files"></a>Ladda upp filer
För att strömma videor med Azure Media Services ska du ladda upp källvideorna, koda dem till flera olika bithastigheter och sedan publicera resultatet. I det här avsnittet beskrivs det första steget. 

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **till gångar**. Välj sedan knappen **Ladda upp**.
   
    ![Ladda upp filer](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Fönstret **Överför en videotillgång** visas.
   
   > [!NOTE]
   > Media Services begränsar inte filstorleken för uppladdning av videor.
   > 
   > 
3. På datorn går du till den video som du vill ladda upp. Välj videon och klicka på **OK**.  
   
    Uppladdningen startar. Du kan följa förloppet under filnamnet.  

När uppladdningen är klar visas den nya tillgången i fönstret **Tillgångar**. 

## <a name="encode-assets"></a>Koda tillgångar
Om du vill använda dynamisk paketering måste du koda källfilen i en uppsättning MP4-filer med flera bithastigheter. Kodningsstegen visas i det här avsnittet.

### <a name="encode-assets-in-the-portal"></a>Koda tillgångar i portalen
Koda innehållet med hjälp av Media Encoder Standard i Azure-portalen:

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **till gångar**. Välj den tillgång som du vill koda.
3. Välj knappen **Koda**.
4. I fönstret **Koda en tillgång** väljer du processorn **Media Encoder Standard** och en förinställning. Information om förinställningar finns i [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Autogenerera en bithastighetsstege) och [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Uppgiftsförinställningar för Media Encoder Standard). Det är viktigt att välja den förinställning som fungerar bäst för indatavideon. Om du till exempel vet att indatavideon har en upplösning på 1 920 &#215; 1 080 bildpunkter kan du välja förinställningen **H264 multibithastighet 1080p**. Om du har en video med låg upplösning (640 &#215; 360) ska du inte använda förinställningen **H264 multibithastighet 1080p**.
   
   Du kan underlätta hanteringen av resurserna genom att redigera namnet på utdatatillgången och namnet på jobbet.
   
   ![Koda tillgångar](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Välj **Skapa**.

### <a name="monitor-encoding-job-progress"></a>Övervaka förloppet för kodningsjobb
Välj **Inställningar** överst på sidan för att övervaka förloppet för kodningsjobbet och välj sedan **Jobb**.

![Jobb](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicera innehåll
För att ge användaren en URL som kan användas för att strömma eller hämta innehållet måste du först publicera tillgången genom att skapa en lokaliserare. Lokaliserare ger åtkomst till filer som finns i tillgången. Azure Media Services stöder två typer av lokaliserare: 

* **Strömningslokaliserare (OnDemandOrigin)**. Strömningslokaliserare används för anpassad strömning. Exempel på anpassad strömning är HLS, Smooth Streaming och MPEG-DASH. Om du vill skapa en strömningslokaliserare måste tillgången innehålla en .ism-fil. 
* **Progressiv lokaliserare (signatur för delad åtkomst)**. Progressiva lokaliserare används för att leverera video via progressiv nedladdning.

Om du vill bygga en HLS streaming-URL lägger du till *(format = M3U8-AAPL)* till URL: en:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Om du vill skapa en strömnings-URL som ska spela upp Smooth Streaming-tillgångar använder du följande URL-format:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest`

Om du vill skapa en strömnings-URL för MPEG-DASH lägger du till *(format=mpd-time-csf)* i URL:en:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

En URL för signatur för delad åtkomst har följande format:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

> [!NOTE]
> Lokaliserare som har skapats i Azure-portalen före mars 2015 har ett utgångsdatum på två år.  
> 
> 

Om du vill uppdatera utgångsdatumet för en lokaliserare kan du använda en [REST API](/rest/api/media/operations/locator#update_a_locator) eller en [.NET API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator). 

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

## <a name="play-content-from-the-portal"></a>Spela upp innehåll från portalen
Du kan testa videon på en innehållsspelare i Azure-portalen.

Välj videon och välj sedan knappen **Spela upp**.

![Spela upp videon i Azure-portalen](./media/media-services-portal-vod-get-started/media-services-play.png)

Vissa förutsättningar gäller:

* Starta direktuppspelningen genom att börja köra standardslutpunkten för direktuppspelning.
* Kontrollera att videon har publicerats.
* Azure-portalens mediaspelare spelar upp från den slutpunkt för direktuppspelning som är standard. Klicka för att kopiera URL:en och klistra in den i en annan spelare om du vill spela upp från en slutpunkt för direktuppspelning som inte är standard. Du kan till exempel testa videon i [Azure Media Player](https://aka.ms/azuremediaplayer).

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]