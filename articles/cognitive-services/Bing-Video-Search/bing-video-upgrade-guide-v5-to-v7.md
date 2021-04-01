---
title: Uppgradera API för videosökning i Bing V5 till v7
titleSuffix: Azure Cognitive Services
description: Identifierar de delar av ditt Videosökning i Bing-program som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: e47691c82d43a35a7235eb5c3fd3cf7bca081aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351920"
---
# <a name="video-search-api-upgrade-guide"></a>Videosökning API-uppgraderings guide

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här uppgraderings guiden identifierar ändringarna mellan version 5 och version 7 av API för videosökning i Bing. Använd den här guiden för att hjälpa dig att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slut punktens versions nummer har ändrats från V5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Fel svars objekt och felkoder

- Alla misslyckade förfrågningar bör nu innehålla ett `ErrorResponse` objekt i svars texten.

- Följande fält har lagts till i `Error` objektet.  
  - `subCode`&mdash;Partitioner felkod i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Ytterligare information om det fel som beskrivs i `message` fältet
   

- Ersatt felkoderna för v5 med följande möjliga `code` `subCode` värden.

|Kod|Under kod|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av under kods villkoren inträffar. Svaret innehåller dessa fel om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran är ogiltig. Till exempel saknas en obligatorisk parameter eller också är ett parameter värde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-status koden 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskod 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded varje gång du överskrider dina frågor per sekund (frågor per sekund) eller frågor per månad (QPM)-kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit frågor per sekund och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel `Ocp-Apim-Subscription-Key` saknas rubriken eller så är prenumerations nyckeln inte giltig.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-status koden 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerations nyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-status koden 403.

- Följande mappar de tidigare fel koderna till de nya koderna. Om du har tagit ett beroende på V5-felkoder, uppdaterar du koden enligt detta.

|Version 5 kod|Version 7 kod. subcode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Inaktiverad|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError. NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blockerad|InvalidRequest. blockerad

### <a name="query-parameters"></a>Frågeparametrar

- Byter namn på `modulesRequested` Frågeparametern till [moduler](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Objekt ändringar

- Bytt namn på `nextOffsetAddCount` [video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) fältet till `nextOffset` . Hur du använder förskjutningen har också ändrats. Tidigare skulle du ange parametern [förskjutnings](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) fråga till `nextOffset` värdet plus föregående förskjutnings värde plus antalet videor i resultatet. Nu anger du bara `offset` Frågeparametern till `nextOffset` värdet.  
  
- Data typen för fältet har ändrats `relatedVideos` från `Video[]` till [VideosModule](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (se [VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).