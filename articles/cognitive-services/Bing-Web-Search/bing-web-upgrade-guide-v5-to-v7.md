---
title: Uppgradera från API V5 till v7-API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: 'Ta reda på vilka delar av programmet som kräver att uppdateringar använder Webbsökning i Bing v7-API: er.'
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349528"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uppgradera från API för webbsökning i Bing V5 till v7

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här uppgraderings guiden identifierar ändringarna mellan version 5 och version 7 av API för webbsökning i Bing. Använd den här guiden för att hjälpa dig att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slut punktens versions nummer har ändrats från V5 till v7. Till exempel https: \/ \/ API.Cognitive.Microsoft.com/Bing/**v 7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Fel svars objekt och felkoder

- Alla misslyckade förfrågningar bör nu innehålla ett `ErrorResponse` objekt i svars texten.

- Följande fält har lagts till i `Error` objektet.  
  - `subCode`&mdash;Partitioner felkod i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Ytterligare information om det fel som beskrivs i `message` fältet


- Ersatt felkoderna för v5 med följande möjliga `code` `subCode` värden.

|Kod|Under kod|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av under kod villkoren inträffar. Svaret kommer att inkludera dessa fel om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran är ogiltig. Till exempel saknas en obligatorisk parameter eller också är ett parameter värde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-status koden 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskod 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded varje gång du överskrider dina frågor per sekund (frågor per sekund) eller frågor per månad (QPM)-kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit frågor per sekund och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel `Ocp-Apim-Subscription-Key` saknas rubriken eller så är prenumerations nyckeln inte giltig.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-status koden 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Det här felet kan inträffa om prenumerations nyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-status koden 403.

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


## <a name="non-breaking-changes"></a>Icke-brytande ändringar  

### <a name="headers"></a>Sidhuvuden

- Den valfria rubriken för [pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) -begäran har lagts till. Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).

### <a name="query-parameters"></a>Frågeparametrar

- Frågeparametern för [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) har lagts till. Använd den här parametern för att ange det antal svar som du vill att svaret ska innehålla. Svaren väljs utifrån rangordning. Om du till exempel anger den här parametern till tre (3), innehåller svaret de tre främsta rankade svaren.  

- Parametern [befordra](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) frågeparameter har lagts till. Använd den här parametern tillsammans med `answerCount` för att uttryckligen inkludera en eller flera svars typer, oavsett rangordning. Om du till exempel vill flytta videor och bilder till svaret ställer du in Höj till *videor, bilder*. Listan med svar som du vill befordra räknas inte mot `answerCount` gränsen. Om är till exempel `answerCount` 2 och `promote` är inställt på *videor, bilder*, kan svaret omfatta webb sidor, nyheter, videor och bilder.

### <a name="object-changes"></a>Objekt ändringar

- Fältet har lagts `someResultsRemoved` till i [webbsvars](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) -objektet. Fältet innehåller ett booleskt värde som anger om svaret exkluderade vissa resultat från webb svaret.