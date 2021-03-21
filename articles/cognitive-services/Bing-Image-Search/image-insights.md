---
title: Hämta bild insikter – API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API för bildsökning i Bing för att få mer information om en avbildning.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 9dffc16a1d1171343fa10fce10fddba2a12c2167
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348253"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Hämta bild insikter med API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

> [!IMPORTANT]
> I stället för att använda/images/details-slutpunkten för att hämta Image Insights bör du använda [visuell sökning](../bing-visual-search/overview.md) eftersom det ger mer omfattande insikter.


Varje avbildning innehåller en Insights-token som du kan använda för att hämta information om avbildningen. Du kan till exempel hämta en samling relaterade bilder, webb sidor som innehåller bilden eller en lista över butiker där du kan köpa den produkt som visas i avbildningen.  

För att få insikter om en bild fångar du avbildningens [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) -token i svaret.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Anropa sedan avbildnings informationens slut punkt och ange parametern [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) till token i `imageInsightsToken` .  

Ange Frågeparametern för att ange de insikter som du vill hämta `modules` . Om du vill hämta alla insikter ställer `modules` du in på `All` . Om du bara vill hämta beskrivningar och insikter för samlingen anger `modules` du till `Caption%2CCollection` . En fullständig lista över möjliga insikter finns i [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Alla insikter är inte tillgängliga för alla avbildningar. Svaret innehåller alla insikter som du har begärt, om det är tillgängligt.

I följande exempel begärs alla tillgängliga insikter för föregående bild.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Hämta insikter om en känd bild

Om du har URL: en till en bild som du vill hämta insikter om använder du Frågeparametern [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) i stället för parametern [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) för att ange avbildningen. Eller, om du har avbildnings filen kan du skicka den binära avbildningen i bröd texten i en POST-begäran. Om du använder en POST-begäran `Content-Type` måste rubriken anges till `multipart/data-form` . Med båda alternativen får bildens storlek inte överstiga 1 MB.  

Om du har en URL till bilden visar följande exempel hur du begär insikter på en bild.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Hämta alla bild insikter  

Om du vill begära alla insikter för en bild ställer du in fråga-parametern för [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) `All` . För att få relaterade sökningar måste begäran innehålla användarens frågesträng. I det här exemplet visas hur du anger avbildningen med hjälp av [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) .  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Objektet på den översta nivån är ett [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) -objekt i stället för ett [bild](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objekt.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Identifiera entiteter i en bild  

Funktionen för enhets igenkänning identifierar entiteter i en bild, för närvarande endast personer. Om du vill identifiera entiteter i en avbildning anger du parametern för [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) till `RecognizedEntities` .  

> [!NOTE]
> Du kan inte ange den här modulen med någon annan modul. Om du anger den här modulen med andra moduler inkluderar inte svaret identifierade entiteter.  

Nedan visas hur du anger avbildningen med hjälp av parametern [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) . Kom ihåg att URL: en kodar frågeparametrar.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Nedan visas svaret på den tidigare begäran. Eftersom avbildningen innehåller två personer, identifierar svaret en region för varje person. I det här fallet har personerna identifierats i CelebrityAnnotations-och CelebRecognitionAnnotations-grupperna. Bing visar en lista över personerna i varje grupp baserat på sannolikheten att de matchar personen i den ursprungliga avbildningen. Listan är i fallande ordning. CelebRecognitionAnnotations-gruppen ger den högsta förtroende nivån som matchningen är korrekt.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

`region`Fältet identifierar det område i bilden där Bing känner igen entiteten. För människor representerar regionen personens ansikte.  

Värdena i rektangeln är relativa till den ursprungliga bildens bredd och höjd och ligger inom intervallet 0,0 till 1,0. Om bilden till exempel är 300x200 och regionens övre vänstra hörn är vid punkt (10, 20) och det nedersta högra hörnet är i punkt (290, 150), är den normaliserade rektangeln:  

-   Vänster: 10/300 = 0,03333...  
-   Överkant: 20/200 = 0,1  
-   Höger: 290/300 = 0,9667...  
-   Nederkant: 150/200 = 0,75  

Du kan använda den region som Bing returnerar i efterföljande insikter-anrop. Till exempel för att hämta visuellt likartade bilder av den identifierade entiteten. Mer information finns i beskära bilder som ska användas med visuellt likartade moduler och entitets igenkännings moduler. Nedan visas mappningen mellan fälten region och de frågeparametrar som du använder för att beskära bilder.  

-   Vänster Maps till [Cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Översta mappar till [katt](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Höger Maps till [bil](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Längst ned mappar till [CAB](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Hitta visuellt likartade bilder  

Om du vill hitta bilder som liknar den ursprungliga bilden, ställer du [in frågans frågeparameter till](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) SimilarImages.  

Följande begäran visar hur du kan hämta visuellt likartade bilder. Förfrågan använder [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) -Frågeparametern för att identifiera den ursprungliga avbildningen. För att förbättra relevansen bör du inkludera användarens frågesträng.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Nedan visas svaret på den tidigare begäran.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Beskär bilder som ska användas med visuellt likartade moduler och entiteter för entitets igenkänning  

Om du vill ange den region i bilden som Bing använder för att avgöra om bilderna är visuellt likartade eller om du vill utföra enhets igenkänning använder du parametrarna [Cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [CAB, CAB](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)och [bil](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) . Som standard använder Bing hela bilden.  

Parametrarna anger det övre, vänstra hörnet och det nedre högra hörnet i den region som Bing använder för jämförelse. Ange värdena som bråk delar av original bildens bredd och höjd. Bråk värden börjar med (0,0, 0,0) överst, vänstra hörnet och slutar med (1,0, 1,0) i det nedre högra hörnet. Om du till exempel vill ange att det övre, vänstra hörnet ska starta en fjärdedel av vägen från det översta och ett kvartal på vägen från vänster sida, anger `cal` du 0,25 och `cat` 0,25.  

Följande sekvenser av anrop visar hur du anger beskärnings regionen. Det första anropet omfattar inte beskärning och Bing känner igen två personer som står sida vid sida i bildens mitt.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Svaret visar två identifierade entiteter.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Det andra anropet Beskär bilden lodrätt nedåt i mitten och Bing identifierade en enskild person på höger sida av bilden.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Svaret visar en identifierad entitet.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Hitta visuellt likartade produkter  

Om du vill hitta bilder som innehåller produkter som liknar de produkter som finns i den ursprungliga bilden ställer du in SimilarProducts för [moduler](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) .  

Följande begäran visar hur du hämtar bilder av visuellt likartade produkter. Begäran använder [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) -Frågeparametern för att identifiera den ursprungliga avbildningen som returnerades i en tidigare begäran. För att förbättra relevansen bör du inkludera användarens frågesträng.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Nedan visas svaret på den tidigare begäran. Svaret innehåller en bild av en liknande produkt och anger hur många butiker som erbjuder produkten online, om det finns produkt värderingar och det lägsta priset som påträffades (se `aggregateOffer` fältet).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Om du vill hämta en lista över de handlare som erbjuder produkten online (se fältet [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) ) anropar du API: et igen och anger `modules` till ShoppingSources. Ange sedan `insightsToken` Frågeparametern till den token som finns i produkt sammanfattnings bilden.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Följande är svaret på föregående begäran.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```