---
title: Skapa filter med Azure Media Services REST API | Microsoft Docs
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services REST API skapar dynamiska manifest för att uppnå denna selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: 2b0271edbbd119e1d60f30cc149ba7772fd0cfa9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730226"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Skapa filter med Azure Media Services REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Från och med 2,17-versionen kan du med Media Services definiera filter för dina till gångar. Dessa filter är regler på Server sidan som gör det möjligt för kunderna att välja att göra saker som: endast uppspelning av en del av en video (i stället för att spela upp hela videon) eller ange endast en delmängd av ljud-och video åter givningar som kundens enhet kan hantera (i stället för alla renderingar som är associerade med till gången). Den här filtreringen av dina till gångar arkiveras via **dynamiskt manifest** s som skapas på kundens begäran att strömma en video som baseras på angivna filter.

Mer detaljerad information om filter och dynamiskt manifest finns i [Översikt över dynamiska manifest](media-services-dynamic-manifest-overview.md).

Den här artikeln visar hur du använder REST-API: er för att skapa, uppdatera och ta bort filter. 

## <a name="types-used-to-create-filters"></a>Typer som används för att skapa filter
Följande typer används när du skapar filter:  

* [Filter](/rest/api/media/operations/filter)
* [AssetFilter](/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect och FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Skapa filter
### <a name="create-global-filters"></a>Skapa globala filter
Använd följande HTTP-förfrågningar för att skapa ett globalt filter:  

#### <a name="http-request"></a>HTTP-begäran
Begärandehuvuden

```console
POST https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host:media.windows.net 
```

Begärandetext 

```console
{  
   "Name":"GlobalFilter",
   "PresentationTimeRange":{  
      "StartTimestamp":"0",
      "EndTimestamp":"9223372036854775807",
      "PresentationWindowDuration":"12000000000",
      "LiveBackoffDuration":"0",
      "Timescale":"10000000"
   },
   "Tracks":[  
      {  
         "PropertyConditions":
              [  
            {  
               "Property":"Type",
               "Value":"audio",
               "Operator":"Equal"
            },
            {  
               "Property":"Bitrate",
               "Value":"0-2147483647",
               "Operator":"Equal"
            }
         ]
      }
   ]
}
```



#### <a name="http-response"></a>HTTP-svar

```console
HTTP/1.1 201 Created 
```

### <a name="create-local-assetfilters"></a>Skapa lokal AssetFilters
Använd följande HTTP-förfrågningar om du vill skapa en lokal AssetFilter:  

#### <a name="http-request"></a>HTTP-begäran
Begärandehuvuden

```console
POST https://media.windows.net/API/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net  
```

Begärandetext 

```console
{   
   "Name":"AssetFilter", 
   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
   "PresentationTimeRange":{   
      "StartTimestamp":"0", 
      "EndTimestamp":"9223372036854775807", 
      "PresentationWindowDuration":"12000000000", 
      "LiveBackoffDuration":"0", 
      "Timescale":"10000000" 
   }, 
   "Tracks":[   
      {   
         "PropertyConditions": 
              [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

#### <a name="http-response"></a>HTTP-svar

```console
HTTP/1.1 201 Created 
. . . 
```

## <a name="list-filters"></a>Lista filter
### <a name="get-all-global-filters-in-the-ams-account"></a>Hämta alla globala **filter** i AMS-kontot
Använd följande HTTP-förfrågningar för att lista filter: 

#### <a name="http-request"></a>HTTP-begäran

```console
GET https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="get-assetfilters-associated-with-an-asset"></a>Hämta **AssetFilter** s som är kopplade till en till gång
#### <a name="http-request"></a>HTTP-begäran

```console
GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

### <a name="get-an-assetfilter-based-on-its-id"></a>Hämta en **AssetFilter** baserat på dess ID
#### <a name="http-request"></a>HTTP-begäran

```console
GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000
```

## <a name="update-filters"></a>Uppdatera filter
Använd korrigering, Lägg eller sammanfoga för att uppdatera ett filter med nya egenskaps värden.  Mer information om de här åtgärderna finns i [korrigering, Lägg, slå samman](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Om du uppdaterar ett filter kan det ta upp till två minuter för strömnings slut punkten att uppdatera reglerna. Om innehållet har betjänats med det här filtret (och cachelagrats i proxyservrar och CDN-cacheminnen), kan uppdatering av det här filtret leda till Player-problem. Rensa cacheminnet när du har uppdaterat filtret. Om det här alternativet inte är möjligt bör du överväga att använda ett annat filter.  

### <a name="update-global-filters"></a>Uppdatera globala filter
Om du vill uppdatera ett globalt filter använder du följande HTTP-förfrågningar: 

#### <a name="http-request"></a>HTTP-begäran
Begärandehuvuden: 

```console
MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
Content-Length: 384
```

Begärandetext: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

### <a name="update-local-assetfilters"></a>Uppdatera lokal AssetFilters
Om du vill uppdatera ett lokalt filter använder du följande HTTP-förfrågningar: 

#### <a name="http-request"></a>HTTP-begäran
Begärandehuvuden: 

```console
MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

Begärandetext: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

## <a name="delete-filters"></a>Ta bort filter
### <a name="delete-global-filters"></a>Ta bort globala filter
Om du vill ta bort ett globalt filter använder du följande HTTP-förfrågningar:

#### <a name="http-request"></a>HTTP-begäran

```console
DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN>  
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="delete-local-assetfilters"></a>Ta bort lokal AssetFilters
Om du vill ta bort en lokal AssetFilter använder du följande HTTP-förfrågningar:

#### <a name="http-request"></a>HTTP-begäran

```console
DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

## <a name="build-streaming-urls-that-use-filters"></a>Bygg direkt uppspelnings-URL: er som använder filter
Information om hur du publicerar och levererar till gångar finns i [leverera innehåll till kund översikt](media-services-deliver-content-overview.md).

I följande exempel visas hur du lägger till filter till dina strömmande URL: er.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`

    
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över dynamiska manifest](media-services-dynamic-manifest-overview.md)
