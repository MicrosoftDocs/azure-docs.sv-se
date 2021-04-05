---
title: Uppgradera från API för bildsökning i Bing V5 till v7
titleSuffix: Azure Cognitive Services
description: Den här uppgraderings guiden beskriver ändringar mellan version 5 och version 7 av API för bildsökning i Bing. Använd den här guiden för att hjälpa dig att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342183"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Uppgraderings guide för API för bildsökning i Bing v7

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Den här uppgraderings guiden identifierar ändringarna mellan version 5 och version 7 av API för bildsökning i Bing. Använd den här guiden för att hjälpa dig att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slut punktens versions nummer har ändrats från V5 till v7. Till exempel https: \/ /API.Cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/search.

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

- Byter namn på `modulesRequested` Frågeparametern till [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Har bytt namn till taggar. Visa en frågeparameter för [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) till taggar.  

- Ändrade listan över stödda marknader i ShoppingSources-filtervärdet till endast en-US. Se [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Ändringar i Image Insights

- Bytt namn på `annotations` fältet för [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) till `imageTags` .  

- Objektet har döpts `AnnotationModule` om till [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Ett nytt namn på `Annotation` objektet att [tagga](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)och borttaget `confidence` fält.  

- Bytt namn på `insightsSourcesSummary` fältet för objektet [bild](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) till `insightsMetadata` .  

- Objektet har döpts `InsightsSourcesSummary` om till [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Slut punkten lades till `https://api.cognitive.microsoft.com/bing/v7.0/images/details` . Använd den här slut punkten för att begära bild insikter i stället för/images/search-slutpunkten. Se [bild insikter](./image-insights.md).

- Följande frågeparametrar är nu endast giltiga med `/images/details` slut punkten.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [föra](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [installera](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [lat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [&](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Objektet har döpts `ImageInsightsResponse` om till [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Data typerna för följande fält har ändrats i [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) -objektet.  

    -   Fält typen har ändrats `relatedCollections` från `ImageGallery[]` till [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Fält typen har ändrats `pagesIncluding` från `Image[]` till [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Fält typen har ändrats `relatedSearches` från `Query[]` till [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Fält typen har ändrats `recipes` från `Recipe[]` till [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Fält typen har ändrats `visuallySimilarImages` från `Image[]` till [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Fält typen har ändrats `visuallySimilarProducts` från `ProductSummaryImage[]` till [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Objektet har tagits bort `ProductSummaryImage` och de produktbaserade fälten har flyttats till objektet [bild](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) . `Image`Objektet inkluderar endast de produktbaserade fälten när avbildningen ingår som en del av visuellt likartade produkter i ett bild insikts svar.  

    -   Fält typen har ändrats `recognizedEntityGroups` från `RecognizedEntityGroup[]` till [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Bytt namn på `categoryClassification` fältet för [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) till `annotations` och ändrade dess typ till `AnnotationsModule` .  

### <a name="images-answer"></a>Svar på bilder

-   Fälten displayShoppingSourcesBadges och displayRecipeSourcesBadges har tagits bort från [avbildningarna](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Bytt namn på `nextOffsetAddCount` fältet med [bilder](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) till `nextOffset` . Hur du använder förskjutningen har också ändrats. Tidigare angav du parametern [förskjutnings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) fråga till `nextOffsetAddCount` värdet plus föregående förskjutnings värde plus antalet bilder i resultatet. Nu ställer du in `offset` till `nextOffset` värdet.  


## <a name="non-breaking-changes"></a>Icke-brytande ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Transparent som ett möjligt [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) -filter värde har lagts till. Det transparenta filtret returnerar bara bilder med en genomskinlig bakgrund.

- Lade till ett eventuellt [licens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) filter värde. Filtret returnerar bara bilder som är under licens.

- Frågeparametrar för parametrarna [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) och [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) har lagts till. Använd dessa filter för att returnera bilder inom ett intervall med fil storlekar.  

- Har lagt till parametrarna [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [MinHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [MinWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) . Använd dessa filter för att returnera bilder inom ett intervall med höjd och bredd.  

### <a name="object-changes"></a>Objekt ändringar

- `description`Fälten och har lagts `lastUpdated` till i objektet [erbjudande](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) .  

- Fältet har lagts `name` till i [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) -objektet.  

- Har lagts `similarTerms` till i objektet [bilder](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Det här fältet innehåller en lista över termer som liknar användarens frågesträng.