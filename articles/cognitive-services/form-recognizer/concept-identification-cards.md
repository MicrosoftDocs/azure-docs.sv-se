---
title: -FORMIGENKÄNNING
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör extrahering av data från identitetsdokument med Formigenkänning förbyggda ID:ns API.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514721"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>Formigenkänning förbyggd dokumentmodell för identifiering (ID)

Azure Formigenkänning kan analysera och extrahera information från myndighetsutgivna identifieringsdokument (ID: n) med hjälp av dess fördefinierade ID-modell. Den kombinerar våra kraftfulla [OCR-funktioner (optisk teckenläsning)](../computer-vision/overview-ocr.md) med ID-igenkänningsfunktioner för att extrahera viktig information från Worldwide Passports och AMERIKANSKA drivrutinslicenser (alla 50 delstater och D.C.). ID-API:et extraherar viktig information från dessa identitetsdokument, till exempel förnamn, efternamn, födelsedatum, dokumentnummer med mera. Detta API är tillgängligt i Formigenkänning v2.1-förhandsversion som en molntjänst och som en lokal container.

## <a name="what-does-the-id-service-do"></a>Vad gör ID-tjänsten?

Den fördefinierade ID-tjänsten extraherar nyckelvärdena från världsomfattande pass och amerikanska drivrutinslicenser och returnerar dem i ett ordnat strukturerat JSON-svar.

### <a name="drivers-license-example"></a>**Exempel på drivrutinslicens**

![Exempel på drivrutinslicens](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Passport-exempel**

![Passport-exempel](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Extraherade fält

|Namn| Typ | Description | Värde |
|:-----|:----|:----|:----|
|  Land | land | Landskod som är kompatibel med ISO 3166-standarden | "USA" |
|  DateOfBirth | date | DOB i formatet YYYY-MM-DD | "1980-01-01" |
|  DateOfExpiration | date | Förfallodatum i formatet YYYY-MM-DD | "2019-05-05" |
|  DocumentNumber | sträng | Relevant passnummer, förarlicensnummer osv. | "340020013" |
|  FirstName | sträng | Extraherat förnamn och mellan initial om tillämpligt | "KANT" |
|  LastName | sträng | Extraherat efternamn | "KANT" |
|  Nationalitet | land | Landskod som är kompatibel med ISO 3166-standarden | "USA" |
|  Sex | kön | Möjliga extraherade värden är "M", "F" och "X" | "F" |
|  MachineReadableZone | objekt | Extraherade Passport MRZ inklusive två rader med 44 tecken vardera | "P<USAFARS<<–<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | sträng | Dokumenttyp, till exempel Passport, drivrutinslicens | "passport" |
|  Adress | sträng | Extraherad adress (endast drivrutinslicens) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  Region | sträng | Extraherad region, delstat, provins osv. (endast drivrutinslicens) | "Washington" |

### <a name="additional-features"></a>Ytterligare funktioner

API:et för -API:et returnerar också följande information:

* Konfidensnivå för fält (varje fält returnerar ett associerat konfidensvärde)
* OCR-råtext (OCR-extraherad textutdata för hela kvittot)
* Begränsningsruta för varje extraherat fält i USA-drivrutinslicenser
* Begränsningsruta för datorns läsbara zon (MRZ) på Passport

  > [!NOTE]
  > Förbyggda ID:n identifierar inte ID-äkthet
  >
  > Formigenkänning inbyggda ID:n extraherar nyckeldata från ID-data. Den identifierar dock inte giltigheten eller äktheten för det ursprungliga identitetsdokumentet.

## <a name="try-it-out"></a>Prova

Om du vill prova Formigenkänning IDs-tjänsten går du till exempelgränssnittsverktyget online:

> [!div class="nextstepaction"]
> [Prova fördefinierade modeller](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Indatakrav

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>ID-typer som stöds

* **Färdiga IDs v2.1-preview.3** Extraherar nyckelvärden från världsomfattande pass och amerikanska drivrutinslicenser.

  > [!NOTE]
  > Stöd för ID-typ
  >
  > ID-typer som stöds för närvarande är världsomfattande passport- och amerikanska drivrutinslicenser. Vi vill aktivt utöka vårt ID-stöd till andra identitetsdokument över hela världen.

## <a name="post-analyze-id-document"></a>Dokument för POST-analys-ID

Åtgärden [Analysera ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) tar en bild eller PDF av ett ID som indata och extraherar intressanta värden. Anropet returnerar ett svarshuvudfält med namnet `Operation-Location` . Värdet `Operation-Location` är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svarshuvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Resultat av GET Analyze ID-dokument

<!---
Need to update this with updated APIM links when available
-->

Det andra steget är att anropa [**åtgärden Get Analyze idDocument Result.**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) Den här åtgärden tar som indata det resultat-ID som skapades av åtgärden Analysera ID. Den returnerar ett JSON-svar som innehåller **ett statusfält** med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar med **värdet lyckades.** Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida frekvensen för begäranden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: Analysåtgärden har inte startats. |
| |  | körs: Analysåtgärden pågår. |
| |  | misslyckades: Analysåtgärden misslyckades. |
| |  | lyckades: Analysåtgärden har lyckats. |

När **statusfältet** har värdet **lyckades innehåller** JSON-svaret resultatet för kvittoförståelse och textigenkänning. Resultatet av -ID:erna ordnas som en ordlista med namngivna fältvärden, där varje värde innehåller den extraherade texten, det normaliserade värdet, en begränsningsruta, konfidens och motsvarande ordelement. Textigenkänningsresultatet ordnas som en hierarki med rader och ord, med text, avgränsare och konfidensinformation.

![exempel på kvittoresultat](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Se följande exempel på ett lyckat JSON-svar: `readResults` Noden innehåller all tolkad text. Text ordnas efter sida, sedan efter rad och sedan efter enskilda ord. Noden `documentResults` innehåller DE ID-värden som modellen identifierade. I den här noden hittar du även användbara nyckel/värde-par som förnamn, efternamn, dokumentnummer med mera.

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

* Prova dina egna ID:er och exempel i [Formigenkänning exempelgränssnittet](https://fott-preview.azurewebsites.net/).
* Slutför en [Formigenkänning snabbstart för](quickstarts/client-library.md) att komma igång med att skriva en APP för ID-bearbetning med Formigenkänning på val annat utvecklingsspråk.

## <a name="see-also"></a>Se även

* [**Vad är formigenkänning?**](./overview.md)
* [**REST API referensdokument**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
