---
title: Hantera Media Services entiteter med REST | Microsoft Docs
description: Den här artikeln visar hur du hanterar Media Services entiteter med REST API.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015564"
---
# <a name="managing-media-services-entities-with-rest"></a>Hantera Media Services entiteter med REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services är en REST-baserad tjänst som bygger på OData v3. Du kan lägga till, fråga, uppdatera och ta bort entiteter på ungefär samma sätt som du kan på alla andra OData-tjänster. Undantag kommer att anropas när det är tillämpligt. Mer information om OData finns i [dokumentationen för Open data Protocol](https://www.odata.org/documentation/).

Det här avsnittet visar hur du hanterar Azure Media Services entiteter med REST.

>[!NOTE]
> Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Den 1 april 2017 tas till exempel alla jobb poster i ditt konto som är äldre än 31 december 2016 bort automatiskt. Om du behöver arkivera jobb-/uppgifts informationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="considerations"></a>Överväganden  

När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Lägger till entiteter
Varje entitet i Media Services läggs till i en enhets uppsättning, till exempel till gångar, via en POST-HTTP-begäran.

I följande exempel visas hur du skapar en Access policy.

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>Fråga entiteter
Att fråga och lista entiteter är enkelt och inbegriper bara en GET HTTP-begäran och valfria OData-åtgärder.
I följande exempel hämtas en lista över alla MediaProcessor-entiteter.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Du kan också hämta en speciell entitet eller alla enhets uppsättningar som är associerade med en speciell entitet, till exempel i följande exempel:

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

I följande exempel returneras endast egenskapen state för alla jobb.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

I följande exempel returneras alla JobTemplates med namnet "SampleTemplate".

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> $Expand åtgärden stöds inte i Media Services och de LINQ-metoder som inte stöds beskrivs i LINQ-överväganden (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Räkna upp genom stora samlingar av entiteter
När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras vid en tidpunkt eftersom offentliga REST v2 begränsar frågeresultat till 1000-resultat. Använd **Skip** och **Top** för att räkna upp genom den stora mängden av entiteter. 

I följande exempel visas hur du använder **Skip** och **Top** för att hoppa över de första 2000 jobben och hämta nästa 1000-jobb.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Uppdaterar entiteter
Beroende på entitetstypen och det tillstånd som den finns i, kan du uppdatera egenskaperna för den entiteten via en korrigerings fil, skicka eller slå samman HTTP-begäranden. Mer information om de här åtgärderna finns i [korrigering/Lägg/slå samman](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

I följande kod exempel visas hur du uppdaterar namn-egenskapen på en till gångs enhet.

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>Tar bort entiteter
Entiteter kan tas bort i Media Services med en HTTP-begäran borttagning. Beroende på entiteten kan det vara viktigt i vilken ordning du tar bort entiteter. Till exempel kräver entiteter som till gångar att du återkallar (eller tar bort) alla positionerare som refererar till en viss till gång innan du tar bort till gången.

I följande exempel visas hur du tar bort en positionerare som användes för att överföra en fil till Blob Storage.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
