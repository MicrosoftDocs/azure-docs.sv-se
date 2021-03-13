---
title: Vanliga frågor och svar om Azure Media Services
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013388"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Vanliga frågor och svar om Media Services v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln behandlar vanliga frågor som skapats av AMS-communityn (Azure Media Services).

## <a name="general-ams-faqs"></a>Vanliga frågor och svar om vanliga AMS

F: hur strömmar du till Apple iOS-enheter?

A: Lägg till "(format = M3U8-AAPL)"-sökvägen till "/manifest"-delen av URL: en för att tala om att den strömmande ursprungs servern returnerar HLS innehåll för användning på Apple iOS-enheter (mer information finns i [leverera innehåll](media-services-deliver-content-overview.md))

F: hur skalar du indexeringen?

A: de reserverade enheterna är desamma för kodnings-och indexerings uppgifter. Följ anvisningarna för [hur du skalar kodnings reserverade enheter](media-services-scale-media-processing-overview.md). **Observera** att indexerings prestanda inte påverkas av en reserverad enhets typ.

F: Jag har laddat upp, kodat och publicerat en video. Vad skulle vara orsaken till att videon inte spelas upp när jag försöker strömma den?

A: en av de vanligaste orsakerna är att du inte har den slut punkt för direkt uppspelning som du försöker spela upp i **körnings** läge.  

F: kan jag göra en sammansättning i en Live-dataström?

A: sammansättning på Live-dataströmmar finns för närvarande inte i Azure Media Services, vilket innebär att du måste skriva på datorn i förväg.

F: kan jag använda Azure CDN med direkt uppspelning?

A: Media Services stöder integrering med Azure CDN (mer information finns i [Hantera strömnings slut punkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)).  Du kan använda Direktsänd strömning med CDN. Azure Media Services tillhandahåller Smooth Streaming, HLS och MPEG-streck-utdata. Alla dessa format använder HTTP för att överföra data och få fördelar med HTTP-cachelagring. I Live streaming är faktiska video-och ljuddata indelade i fragment och de enskilda fragmenten får cachelagras i CDN. Endast data måste uppdateras är manifest data. CDN uppdaterar regelbundet manifest data.

F: stöder Azure Media Services lagring av avbildningar?

A: om du bara vill lagra JPEG-eller PNG-bilder bör du behålla dem i Azure Blob Storage. Det finns ingen förmån att placera dem i ditt Media Services konto om du inte vill att de ska vara kopplade till dina video-eller ljud till gångar. Eller om du kanske behöver använda bilderna som överlägg i video kodaren. Media Encoder Standard har stöd för att täcka bilder ovanpå videor, och det är det som visar JPEG och PNG som stödda indataformat. Mer information finns i [skapa överlägg](media-services-advanced-encoding-with-mes.md#overlay).

F: Hur kan jag kopiera till gångar från ett Media Services konto till ett annat?

S: om du vill kopiera till gångar från ett Media Services konto till ett annat med hjälp av .NET använder du tillägget [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) som finns i lagrings platsen [Azure Media Services för .NET SDK-tillägg](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Mer information finns i [den här](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum tråden.

F: vilka tecken som stöds för namngivning av filer när du arbetar med AMS?

A: Media Services använder värdet för egenskapen IAssetFile.Name när URL: er för strömmande innehåll skapas (till exempel http://{AMSAccount}. ORIGIN. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Därför är procent kodning inte tillåtet. Värdet för **namn** egenskapen får inte ha något av följande [%-encoding-reserverade tecken](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Det kan också finnas en "." för fil namns tillägget.

F: ansluter du med REST?

A: information om hur du ansluter till AMS-API: et finns i [få åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

F: Hur kan jag rotera en video under kodnings processen?

A: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation efter vinkel på 90/180/270. Standard beteendet är "Auto", där det försöker att identifiera rotations-metadata i den inkommande MP4/MOV-filen och kompensera för det. Ta med följande **käll** element till en av de JSON-för inställningar som definieras [här](media-services-mes-presets-overview.md):

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
