---
title: Publicera Azure Media Services innehåll med HJÄLP av REST
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
ms.openlocfilehash: 650c0847942635e2a6a901db40ed0e51e9412057
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600071"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publicera Azure Media Services innehåll med hjälp av REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Du kan strömma en MP4-uppsättning med anpassningsbar bithastighet genom att skapa en positionerare för OnDemand-direktuppspelning och skapa en strömnings-URL. Artikeln [kodning av en](media-services-rest-encode-asset.md) tillgång visar hur du kodar till en MP4-uppsättning med anpassningsbar bithastighet. Om ditt innehåll är krypterat konfigurerar du principen för tillgångsleverans (enligt [beskrivningen](media-services-rest-configure-asset-delivery-policy.md) i den här artikeln) innan du skapar en lokaliserare. 

Du kan också använda en positionerare för ondemand-strömning för att skapa URL:er som pekar på MP4-filer som kan hämtas progressivt.  

Den här artikeln visar hur du skapar en positionerare för OnDemand-strömning för att publicera din tillgång och skapa en Smooth-, MPEG DASH- och HLS-strömnings-URL:er. Den visar också hur du skapar URL:er för progressiv nedladdning.

I [följande](#types) avsnitt visas uppräkningstyperna vars värden används i REST-anropen.   

> [!NOTE]
> När du använder entiteter Media Services måste du ange specifika rubrikfält och värden i dina HTTP-begäranden. Mer information finns i [Installation för Media Services REST API Development](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns i [Access the Azure Media Services API with Azure AD authentication (Komma åt API:Azure Media Services med Azure AD-autentisering).](media-services-use-aad-auth-to-access-ams-api.md) 

>[!NOTE]
>När du har anslutit till `https://media.windows.net` får du en 301-omdirigering som anger en annan Media Services URI. Du måste göra efterföljande anrop till den nya URI:en.

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en positionerare för OnDemand-direktuppspelning
Om du vill skapa positioneraren för OnDemand-strömning och hämta URL:er måste du göra följande:

1. Om innehållet är krypterat definierar du en åtkomstprincip.
2. Skapa en positionerare för OnDemand-strömning.
3. Om du planerar att strömma hämtar du strömningsmanifestfilen (.ism) i tillgången. 
   
   Om du planerar att progressivt ladda ned hämtar du namnen på MP4-filer i tillgången. 
4. Skapa URL:er till manifestfilen eller MP4-filerna. 
5. Du kan inte skapa en positionerare för direktuppspelning med hjälp av en AccessPolicy som innehåller skriv- eller borttagningsbehörigheter.

### <a name="create-an-access-policy"></a>Skapa en åtkomstprincip

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för lokaliserare som är avsedda att finnas kvar under en längre tid (icke-överföringsprinciper). Mer information finns i den [här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

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

### <a name="create-an-ondemand-streaming-locator"></a>Skapa en positionerare för OnDemand-strömning
Skapa lokaliseraren för den angivna tillgången och tillgångsprincipen.

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

### <a name="build-streaming-urls"></a>Skapa strömnings-URL:er
Använd värdet **sökväg** som returnerades när lokaliseraren skapades för att skapa SMOOTH-, HLS- och MPEG DASH-URL:erna. 

Smooth Streaming: **Sökväg** + manifestfilnamn + "/manifest"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **Sökväg** + manifestfilnamn + "/manifest(format=m3u8-aapl)"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **Sökväg** + manifestfilnamn + "/manifest(format=mpd-time-csf)"

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Skapa URL:er för progressiv nedladdning
Använd värdet **sökväg som** returnerades när positioneraren skapades för att skapa url:en för progressiv nedladdning.   

URL: **Sökväg** + namn på tillgångsfil mp4

Exempel:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Uppräkningstyper

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
[översikt Media Services åtgärder REST API åtgärder](media-services-rest-how-to-use.md)

[Konfigurera tillgångsleveransprincip](media-services-rest-configure-asset-delivery-policy.md)

