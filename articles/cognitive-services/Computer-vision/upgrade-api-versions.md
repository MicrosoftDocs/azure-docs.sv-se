---
title: Uppgradera till Read v3.0 av Visuellt innehåll-API:et
titleSuffix: Azure Cognitive Services
description: Lär dig hur du uppgraderar till Visuellt innehåll v3.0 Read API från v2.0/v2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364098"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Uppgradera från Read v2.x till Read v3.x

Den här guiden visar hur du uppgraderar din befintliga container eller moln-API-kod från Read v2.x till Read v3.x.

## <a name="determine-your-api-path"></a>Fastställa din API-sökväg
Använd följande tabell för  att fastställa versionssträngen i API-sökvägen baserat på den Read 3.x-version som du migrerar till.

|Produkttyp| Version | Versionssträng i 3.x API-sökväg |
|:-----|:----|:----|
|Tjänst | Läs 3.0 eller 3.1 | **v3.0** **eller v3.1** |
|Tjänst | Read 3.2 preview (Läs 3.2– förhandsversion) | **v3.2-preview.1** |
|Container | Förhandsversion av Read 3.0 eller Read 3.1 | **v3.0** **eller v3.1-preview.2** |


Använd sedan följande avsnitt för att begränsa dina åtgärder och ersätta **versionssträngen** i din API-sökväg med värdet från tabellen. För Read **v3.2** preview cloud and container versions (Läsa v3.2-förhandsversion av moln- och containerversioner) uppdaterar du till exempel API-sökvägen till **https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]**.

## <a name="servicecontainer"></a>Tjänst/container

### `Batch Read File`

|Läs 2.x |Läs 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**versionssträng**>/read/analyze[?language]|
    
En ny valfri _språkparameter_ är tillgänglig. Om du inte kan språket i dokumentet, eller om det kan vara flerspråkigt, ska du inte ta med det. 

### `Get Read Results`

|Läsa 2.x |Läsa 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**versionssträng**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` statusflagga

När anropet `Get Read Operation Result` till lyckas returneras ett statussträngfält i JSON-brödtexten.
 
|Läsa 2.x |Läsa 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API-svar (JSON) 

Observera följande ändringar i json:
* I v2.x `Get Read Operation Result` returnerar OCR-igenkännings-json när statusen är `Succeeded"` . I v3.0 är det här fältet `succeeded` .
* Om du vill hämta roten för sidmatrisen ändrar du json-hierarkin `recognitionResults` från till `analyzeResult` / `readResults` . JSON-hierarkin per sida och orden förblir oförändrade, så inga kodändringar krävs.
* Sidvinkeln har bytt namn till och intervallet har ändrats från `clockwiseOrientation` `angle` 0 till 360 grader till -180 till 180 grader. Beroende på din kod kan du behöva göra ändringar eftersom de flesta matematikfunktioner kan hantera endera intervallet.

V3.0-API:et introducerar också följande förbättringar som du kan använda om du vill:
* `createdDateTime` och `lastUpdatedDateTime` läggs till så att du kan spåra bearbetningens varaktighet. Mer information finns i dokumentationen. 
* `version` anger vilken version av API:et som används för att generera resultat
* Ett per ord `confidence` har lagts till. Det här värdet kalibreras så att värdet 0,95 innebär att igenkänningen är korrekt med 95 %. Förtroendepoängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
    
    
I 2.X är utdataformatet följande: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
I v3.0 har den justerats:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Endast tjänst

### `Recognize Text`
`Recognize Text`är  en förhandsgranskningsåtgärd som är *inaktuell i alla versioner Visuellt innehåll API.* Du måste migrera från `Recognize Text` `Read` till (v3.0) eller `Batch Read File` (v2.0, v2.1). v3.0 `Read` av innehåller nyare, bättre modeller för textigenkänning och ytterligare funktioner, så det rekommenderas. Uppgradera från `Recognize Text` till `Read` :

|Identifiera text 2.x |Läs 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**versionssträng**>/read/analyze[?language]|
    
Lägesparametern stöds inte i  `Read` . Både handskriven och tryckt text stöds automatiskt.
    
En ny valfri _språkparameter_ är tillgänglig i v3.0. Om du inte kan språket i dokumentet, eller om det kan vara flerspråkigt, ska du inte ta med det. 

### `Get Recognize Text Operation Result`

|Identifiera text 2.x |Läsa 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/**{operationId}|https://{endpoint}/vision/<**versionssträng**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` statusflaggor
När anropet `Get Recognize Text Operation Result` till lyckas returneras ett statussträngfält i JSON-brödtexten. 
 
|Identifiera text 2.x |Läsa 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API-svar (JSON)

Observera följande ändringar i json:    
* I v2.x `Get Read Operation Result` returnerar OCR-igenkännings-json när statusen är `Succeeded` . I v3.x är det här fältet `succeeded` .
* Om du vill hämta roten för sidmatrisen ändrar du json-hierarkin `recognitionResult` från till `analyzeResult` / `readResults` . JSON-hierarkin per sida och orden förblir oförändrade, så inga kodändringar krävs.

V3.0-API:et introducerar också följande förbättringar som du kan använda. Mer information finns i API-referensen:
* `createdDateTime` och `lastUpdatedDateTime` läggs till så att du kan spåra bearbetningens varaktighet. Mer information finns i dokumentationen. 
* `version` anger vilken version av API:et som används för att generera resultat
* Ett per ord `confidence` har lagts till. Det här värdet kalibreras så att ett värde 0,95 innebär att det är 95 % risk att igenkänningen är korrekt. Förtroendepoängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
* `angle` allmän orientering av texten i medurs riktning, mätt i grader mellan (-180, 180].
* `width` och ger dig måtten i dokumentet och tillhandahåller enheten för dessa `"height"` `"unit"` dimensioner (bildpunkter eller tum, beroende på dokumenttyp.)
* `page` dokument med flera sidor stöds
* `language`dokumentets indataspråk (från den valfria _språkparametern.)_


I 2.X är utdataformatet följande: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
I v3.x har den justerats:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Endast container

### `Synchronous Read`

|Läs 2.0 |Läs 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<-versionssträng >/read/syncAnalyze[?language]|
