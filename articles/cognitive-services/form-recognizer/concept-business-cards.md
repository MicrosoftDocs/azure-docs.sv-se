---
title: Visitkort – formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som är relaterade till visitkorts analys med formatet igenkännings-API-användning och begränsningar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5211c1263af599eb5fd09ad276545c725ce5c867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467006"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Fördefinierad visitkorts modell för formulär igenkänning 

Azure formulär tolken kan analysera och extrahera kontakt information från visitkort med hjälp av en fördefinierad visitkorts modell. Den kombinerar kraftfulla OCR-funktioner (optisk tecken läsning) med vårt visitkort för att förstå modell för att extrahera viktig information från visitkort på engelska. Den extraherar personlig kontakt information, företags namn, befattning med mera. Det inbyggda API: t för visitkortet är offentligt tillgängligt i för hands versionen av formulär igenkänning v 2.1. 

## <a name="what-does-the-business-card-service-do"></a>Vad gör Business Card-tjänsten?

Det inbyggda visitkorts-API: n extraherar nyckel fält från visitkort och returnerar dem i ett organiserat JSON-svar.

![Contoso-specificerad bild från FOTT + JSON-utdata](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Extraherade fält:

|Namn| Typ | Beskrivning | Text | 
|:-----|:----|:----|:----|
| ContactNames | objekt mat ris | Kontakt namn extraherat från visitkort | [{"FirstName": "John", "LastName": "berg"}] |
| FirstName | sträng | Första (angivet) namn på kontakt | Anders | 
| LastName | sträng | Kontaktens senaste (familj) namn |     Andersson | 
| CompanyNames | matris med strängar | Företags namn extraherat från visitkort | ["Contoso"] | 
| Avdelningar | matris med strängar | Kontakt personens avdelning eller organisation | ["R&D"] | 
| JobTitles | matris med strängar | Listad kontakt persons titel | ["Program varu tekniker"] | 
| E-postmeddelanden | matris med strängar | Kontakta e-post som extraherats från visitkort | ["johndoe@contoso.com"] | 
| Websites | matris med strängar | Webbplatsen har extraherats från visitkortet | ["https://www.contoso.com"] | 
| Adresser | matris med strängar | Adress extraherad från visitkort | ["123 huvud gatan, Redmond, WA 98052"] | 
| MobilePhones | matris med telefonnummer | Mobiltelefon nummer extraherat från visitkort | ["+ 19876543210"] |
| Fax | matris med telefonnummer | Fax nummer extraherat från visitkort | ["+ 19876543211"] |
| WorkPhones | matris med telefonnummer | Telefonnummer för arbets telefonnummer som extraherats från visitkort | ["+ 19876543231"] |
| OtherPhones     | matris med telefonnummer | Annat telefonnummer extraherat från visitkort | ["+ 19876543233"] |


Visitkorts-API: et kan också returnera all identifierad text från visitkortet. Denna OCR-utdata ingår i JSON-svaret.  

### <a name="input-requirements"></a>Krav för indatamängd 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Åtgärden analysera visitkort

På [kortet analysera](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync) får du en bild eller en PDF-fil med ett visitkort som indata och utvärderar intressena. Anropet returnerar ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det resultat-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Resultat åtgärden för att analysera affärs kort

Det andra steget är att anropa åtgärden för att [analysera visitkorts resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult) . Den här åtgärden tar sig in i det resultat-ID som skapades av åtgärden analysera visitkort. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 3 till 5 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: analys åtgärden har inte startats.<br /><br />körs: analys åtgärden pågår.<br /><br />misslyckades: det gick inte att utföra analysen.<br /><br />lyckades: analys åtgärden har slutförts.|

När fältet **status** har värdet **lyckades** , inkluderar JSON-svaret affärs korts förståelse och valfria text igenkännings resultat om det behövs. Resultatet av visitkorten är ordnat som en ord lista med namngivna fält värden där varje värde innehåller den extraherade texten, normaliserat värde, avgränsnings Box, relevans och motsvarande Word-element. Resultatet av text igenkänningen är ordnat som en hierarki med rader och ord, med text, avgränsnings ram och information om säkerhet.

![exempel på visitkorts resultat](./media/business-card-results.png)

### <a name="sample-json-output"></a>Exempel på JSON-utdata

Svaret på affärs kortets resultat åtgärd för att analysera visitkort är strukturerad representation av visitkortet med all information som extraheras.  Se här för exempel på en [visitkorts fil](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) och dess strukturerade utdata [exempel visitkorts kort](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

Se följande exempel på ett lyckat JSON-svar:
* `"readResults"`Noden innehåller all den identifierade texten. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. 
* `"documentResults"`Noden innehåller de företagsspecifika värden som modellen identifierade. Här hittar du användbar kontakt information, t. ex. förnamn, efter namn, företags namn med mera.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Följ snabb starten för [snabb](./QuickStarts/client-library.md) start för att implementera data extrahering i visitkort med Python och REST API.

## <a name="customer-scenarios"></a>Kund scenarier  

De data som extraheras med visitkorts-API kan användas för att utföra olika uppgifter. Om du extraherar denna kontakt information sparas tid automatiskt för användare i klientbaserade roller. Här följer några exempel på vad våra kunder har utfört med API: t för visitkort:

* Extrahera kontakt information från visitkort och skapa snabbt telefon kontakter. 
* Integrera med CRM för att automatiskt skapa kontakter med hjälp av visitkorts avbildningar. 
* Håll koll på Sälj leads.  
* Extrahera kontakt information i bulk från befintliga visitkorts avbildningar. 

Visitkorts-API: t ger även [AI Builder-Affärskortets bearbetnings funktion](/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Nästa steg

- Kom igång med att känna igen visitkorten genom att följa [snabb](./quickstarts/client-library.md) starten.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)
* [REST API referens dokument](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
