---
title: Publicera Azure Media Services innehåll med REST
description: Lär dig hur du skapar en positionerare som används för att skapa en strömnings-URL. Koden använder REST API.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: b8733d499b2396160a73906f16a69291cf0b9d71
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015428"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publicera Azure Media Services innehåll med REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Du kan strömma en MP4-uppsättning med anpassad bit hastighet genom att skapa en OnDemand streaming-positionerare och skapa en strömnings-URL. I [encoding en till gångs](media-services-rest-encode-asset.md) artikel visas hur du kodar i en MP4-uppsättning med anpassad bit hastighet. Om ditt innehåll är krypterat konfigurerar du till gångs leverans princip (enligt beskrivningen i [den här](media-services-rest-configure-asset-delivery-policy.md) artikeln) innan du skapar en positionerare. 

Du kan också använda en OnDemand streaming-positionerare för att bygga URL: er som pekar på MP4-filer som kan laddas ned progressivt.  

Den här artikeln visar hur du skapar en lokaliserare för OnDemand-direktuppspelning för att publicera din till gång och bygga ett smidigt, MPEG-streck och HLS strömnings-URL: er. Det visar också hur du skapar Progressive nedladdnings-URL: er.

I [följande](#types) avsnitt visas de uppräknings typer vars värden används i rest-anropen.   

> [!NOTE]
> När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>När du har anslutit till visas https://media.windows.net en 301-omdirigering som anger en annan Media Services-URI. Du måste göra efterföljande anrop till den nya URI: n.

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en lokaliserare för OnDemand-direktuppspelning
Om du vill skapa en OnDemand streaming-positionerare och hämta URL: er måste du göra följande:

1. Om innehållet är krypterat definierar du en åtkomst princip.
2. Skapa en lokaliserare för OnDemand-direktuppspelning.
3. Om du planerar att strömma hämtar du den strömmande manifest filen (. ISM) i till gången. 
   
   Om du planerar att ladda ned progressivt hämtar du namnen på MP4-filer i till gången. 
4. Bygg webb adresser till manifest filen eller MP4-filerna. 
5. Du kan inte skapa en strömmande positionerare med hjälp av en Access policy som innehåller behörigheter för Skriv eller ta bort.

### <a name="create-an-access-policy"></a>Skapa en åtkomst princip

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomst behörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (principer som inte uppladdas). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Begäran:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 311
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
Server: Microsoft-IIS/8.5
request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:52:09 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

### <a name="create-an-ondemand-streaming-locator"></a>Skapa en lokaliserare för OnDemand-direktuppspelning
Skapa lokaliseraren för den angivna till gången och till gångs principen.

Begäran:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 637
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
Server: Microsoft-IIS/8.5
request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:58:37 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}
```

### <a name="build-streaming-urls"></a>Bygga direkt uppspelnings-URL: er
Använd det **Sök vägs** värde som returnerades efter att lokaliseraren har skapats för att skapa de URL: er för utjämna, HLS och MPEG-streck som returneras. 

Smooth Streaming: **sökväg** + manifest fil namn + "/manifest"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **sökväg** + manifest fil namn + "/manifest (format = M3U8-AAPL)"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


BINDESTRECK: **sökväg** + manifest fil namn + "/manifest (format = mpd-Time-CSF)"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Bygg URL: er för progressiv nedladdning
Använd det **Sök vägs** värde som returnerades efter att lokaliseraren har skapats för att skapa en URL för progressiv nedladdning.   

URL: **sökväg** + namn på till gångs fil MP4

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Uppräknings typer

```console
[Flags]
public enum AccessPermissions
{
    None = 0,
    Read = 1,
    Write = 2,
    Delete = 4,
    List = 8,
}

public enum LocatorType
{
    None = 0,
    Sas = 1,
    OnDemandOrigin = 2,
}
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över Media Services åtgärder REST API](media-services-rest-how-to-use.md)

[Konfigurera till gångs leverans princip](media-services-rest-configure-asset-delivery-policy.md)

