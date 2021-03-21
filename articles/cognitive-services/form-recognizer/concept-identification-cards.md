---
title: 'ID: formulär identifierare'
titleSuffix: Azure Cognitive Services
description: 'Lär dig begrepp som rör extrahering av data från identitets dokument med hjälp av ID: n för förskapade ID i formulär tolken.'
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467904"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Modell för fördefinierat identifierings kort (ID) för formulär tolken

Azure formulär tolken kan analysera och extrahera information från myndighets identifierings kort (ID) med hjälp av en fördefinierad ID-modell. Den kombinerar vår kraftfulla [OCR-kapacitet (optisk tecken läsning)](../computer-vision/concept-recognizing-text.md) med ID igenkännings funktioner för att extrahera viktig information från Worldwide Passport och amerikanska driv rutins licenser (alla 50-stater och D.C.). ID-API: n extraherar viktig information från dessa identitets dokument, till exempel förnamn, efter namn, födelse datum, dokument nummer och mycket annat. Detta API är tillgängligt i för hands versionen av Forms igenkänning v 2.1 som en moln tjänst och som en lokal behållare.

## <a name="what-does-the-id-service-do"></a>Vad gör ID-tjänsten? 

De förinställda ID-tjänsterna extraherar nyckel värden från Worldwide Passport och amerikanska driv Rutins licenser och returnerar dem i ett organiserat strukturerat JSON-svar. 

![Exempel på driv Rutins licens](./media/id-example-drivers-license.JPG)

![Exempel på Passport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Extraherade fält

|Namn| Typ | Beskrivning | Värde | 
|:-----|:----|:----|:----|
|  Land | land | Landskod som är kompatibel med ISO 3166 standard | Förenta | 
|  DateOfBirth | date | DOB i ÅÅÅÅ-MM-DD-format | "1980-01-01" | 
|  DateOfExpiration | date | Utgångs datum i ÅÅÅÅ-MM-DD-format | "2019-05-05" |  
|  DocumentNumber | sträng | Relevant Passport-nummer, driv Rutinens licens nummer osv. | "340020013" |  
|  FirstName | sträng | Extraherat tilldelat namn och initial om det är tillämpligt | Jennifer | 
|  LastName | sträng | Extraherat efter namn | "BROOKS" |   
|  Fartyg | land | Landskod som är kompatibel med ISO 3166 standard | Förenta |
|  Sex | kön | Möjliga extraherade värden är "M", "F" och "X" | B | 
|  MachineReadableZone | objekt | Extraherade Passport-MRZ inklusive två rader på 44 tecken | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | sträng | Dokument typ, till exempel Passport, driv Rutinens licens | hanteringsobjektet |  
|  Adress | sträng | Extraherad adress (endast driv Rutinens licens) | "123-GATUADRESS DIN STAD, WA 99999-1234"|
|  Region | sträng | Extraherad region, delstat, provins osv. (endast driv Rutinens licens) | Region | 

### <a name="additional-features"></a>Ytterligare funktioner

API: n returnerar även följande information:

* Fält konfidensnivå (varje fält returnerar ett associerat konfidens värde)
* OCR RAW-text (OCR-extraherade textutdata för hela kvittot)
* Avgränsnings ruta för varje extraherat fält i amerikanska driv Rutins licenser
* Avgränsnings ruta för MRZ (Machine läslig Zone) på Passport

  > [!NOTE]
  > Förbyggda ID: n identifierar inte ID-äkthet
  >
  > Formulär tolken i förväg skapade ID: n extraherar nyckel data från ID-data. Det kommer dock inte att identifiera giltigheten eller äktheten hos det ursprungliga identitets dokumentet. 

## <a name="try-it-out"></a>Prova

Om du vill testa formulär identifierarens ID-tjänst går du till verktyget online-exempel UI:

> [!div class="nextstepaction"]
> [Prova färdiga modeller](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Krav för indatamängd

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>ID-typer som stöds  

* **Färdiga ID v 2.1 – för hands version. 3** Extraherar nyckel värden från Worldwide Passport och amerikanska driv Rutins licenser. 

  > [!NOTE]
  > Stöd för ID-typ 
  >
  > ID-typer som stöds för närvarande är världs omspännande och amerikanska driv Rutins licenser. Vi söker aktivt för att utöka vårt ID-stöd till andra identitets dokument runtom i världen.

## <a name="post-analyze-id-document"></a>PUBLICERA dokument med analys-ID

Åtgärden [analysera ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) tar en bild eller PDF av ett ID som indata och extraherar värdenas värden. Anropet returnerar ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Hämta dokument resultat för analysera ID

<!---
Need to update this with updated APIM links when available
-->

Det andra steget är att anropa [**resultat åtgärden Hämta analysera idDocument**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Den här åtgärden tar in det resultat-ID som skapades av åtgärden analysera ID. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: analys åtgärden har inte startats. |
| |  | körs: analys åtgärden pågår. |
| |  | misslyckades: det gick inte att utföra analysen. |
| |  | lyckades: analys åtgärden har slutförts. |

När fältet **status** har värdet **lyckades** inkluderar JSON-svaret mottagnings förståelse och text igenkännings resultat. ID-resultatet är ordnat som en ord lista med namngivna fält värden där varje värde innehåller den extraherade texten, normaliserat värde, avgränsnings ruta, konfidens och motsvarande Word-element. Resultatet av text igenkänningen är ordnat som en hierarki med rader och ord, med text, avgränsnings ram och information om säkerhet.

![exempel på mottagnings resultat](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Se följande exempel på ett lyckat JSON-svar: `readResults` noden innehåller all identifierad text. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. `documentResults`Noden innehåller de ID-värden som modellen identifierade. Den här noden är också där du hittar användbara nyckel/värde-par som förnamn, efter namn, dokument nummer och mycket annat.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Nästa steg

- Testa dina egna ID: n och exempel i [formulär tolkens exempel gränssnitt](https://fott-preview.azurewebsites.net/).
- Slutför en [snabb start för formulär igenkänning](quickstarts/client-library.md) för att komma igång med att skriva en app för ID-bearbetning med formulär tolken i valfritt utvecklings språk.

## <a name="see-also"></a>Se även

* [**Vad är formigenkänning?**](./overview.md)
* [**REST API referens dokument**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
