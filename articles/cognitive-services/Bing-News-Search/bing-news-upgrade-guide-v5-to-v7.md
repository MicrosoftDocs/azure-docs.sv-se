---
title: Uppgradera API för nyhetssökning i Bing V5 till v7
titleSuffix: Azure Cognitive Services
description: Identifierar de delar av ditt Nyhetssökning i Bing-program som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: a114cb24d79189f9e370fae1962f60ca97241d90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351375"
---
# <a name="news-search-api-upgrade-guide"></a>Nyhetssökning API-uppgraderings guide

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här uppgraderings guiden identifierar ändringarna mellan version 5 och version 7 av API för nyhetssökning i Bing. Använd den här guiden för att hjälpa dig att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slut punktens versions nummer har ändrats från V5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Objekt ändringar

- Fältet har lagts `contractualRules` till i [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -objektet. `contractualRules`Fältet innehåller en lista över regler som du måste följa (till exempel artikel behörighet). Du måste tillämpa den behörighet som anges i i `contractualRules` stället för att använda `provider` . Artikeln innehåller `contractualRules` bara när [webbsökning API](../bing-web-search/overview.md) -svaret innehåller ett diskussions grupps svar.

## <a name="non-breaking-changes"></a>Icke-brytande ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Du har lagt till produkter som ett möjligt värde som du kan ange [Kategorins](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) frågeparameter till. Se [kategorier efter marknad](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Du har lagt till Frågeparametern [SortBy](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) , som returnerar trender som sorteras efter datum med den senaste först.

- Har lagt till [sedan](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) Frågeparametern, som returnerar trender som identifierades av Bing på eller efter den angivna den angivna UNIX-tidsstämpeln.

### <a name="object-changes"></a>Objekt ändringar

- Fältet har lagts `mentions` till i [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -objektet. `mentions`Fältet innehåller en lista med entiteter (personer eller platser) som hittades i artikeln.

- Fältet har lagts `video` till i [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -objektet. `video`Fältet innehåller en video som är relaterad till nyhets artikeln. Videon är antingen en \<iframe\> som du kan bädda in eller en rörelse miniatyr bild.

- Fältet har lagts `sort` till i [nyhetsobjektet](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) -objektet. I `sort` fältet visas sorterings ordningen för artiklarna. Artiklarna sorteras exempelvis efter relevans (standard) eller datum.

- [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) -objektet har lagts till, vilket definierar en sorterings ordning. `isSelected`Fältet visar om svaret använde sorterings ordningen. Om **värdet är true** används sorterings ordningen i svaret. Om `isSelected` är **falskt** kan du använda URL: en i `url` fältet för att begära en annan sorterings ordning.