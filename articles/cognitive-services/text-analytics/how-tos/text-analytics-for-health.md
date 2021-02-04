---
title: Använda Textanalys för hälso tillstånd
titleSuffix: Azure Cognitive Services
description: Lär dig att extrahera och märka medicinsk information från ostrukturerad klinisk text med Textanalys för hälso tillstånd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: f7ba6363ec3a38d37ea3df0f76409289069638e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537804"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Gör så här: använda Textanalys för hälsa (för hands version)

> [!IMPORTANT] 
> Textanalys för hälsa är en förhands gransknings funktion som tillhandahålls "i befintligt skick" och "med alla fel". Därför **bör textanalys för hälsa (för hands version) inte implementeras eller distribueras i produktions användningen.** Textanalys för hälsa är inte avsedd eller görs tillgänglig för användning som medicinsk enhet, klinisk support, diagnostikverktyg eller annan teknik som är avsedd att användas i diagnosen, härdning, minskning, behandling eller förebyggande av sjukdom eller andra villkor, och ingen licens eller rättighet beviljas av Microsoft för att använda den här funktionen i sådana syften. Den här funktionen är inte utformad eller avsedd att implementeras eller distribueras som en ersättning för yrkes rådgivning eller hälso besked, diagnos, behandling eller klinisk dom från en sjukvårds expert och bör inte användas som sådan. Kunden är ensam ansvarig för all användning av Textanalys för hälsa. Microsoft garanterar inte att Textanalys för hälsa eller material som tillhandahålls i samband med funktionen kommer att vara tillräckligt för medicinska skäl eller på annat sätt uppfylla hälso tillståndet eller hälso tillståndet för en person. 


Textanalys för hälsan är en funktion i API för textanalyss tjänsten som extraherar och namnger relevant medicinsk information från ostrukturerade texter som läkares anteckningar, Utskicks sammanfattning, kliniska dokument och elektroniska hälso poster.  Det finns två sätt att använda den här tjänsten: 

* [Det webbaserade API: t (asynkront)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [En Docker-behållare (synkron)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funktioner

Textanalys för hälso tillstånd utför namngivna enhets igenkänning (NER), Relations extrahering, avnegationer och entiteter som länkas på engelsk språk text för att få insikter om ostrukturerad klinisk och medicinsk text.

### <a name="named-entity-recognition"></a>[Igenkänning av namngiven enhet](#tab/ner)

Igenkänning av namngivna enheter identifierar ord och fraser som nämns i ostrukturerad text som kan associeras med en eller flera semantiska typer, till exempel diagnos, medicinens namn, symptom/signering eller ålder.

> [!div class="mx-imgBorder"]
> ![Hälso NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Relations extrahering](#tab/relation-extraction)

Relations extrahering identifierar meningsfulla anslutningar mellan begrepp som anges i text. Till exempel hittas en relation för "villkor" för att associera ett villkors namn med en tid. 

> [!div class="mx-imgBorder"]
> ![Hälso vård](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

Entiteten länkar disambiguates distinkta entiteter genom att associera namngivna entiteter som nämns i text till begrepp som finns i en fördefinierad databas med begrepp. Till exempel det enhetliga medicinska språk systemet (UMLS).

> [!div class="mx-imgBorder"]
> ![Hälsa, EL](../media/ta-for-health/health-entity-linking.png)

Textanalys för hälso tillstånd stöder länkning till hälso-och biomedicin-vokabulär som finns i Metathesaurus-kunskaps källan (Unified medicin language system) ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

### <a name="negation-detection"></a>[Negation identifiering](#tab/negation-detection) 

Innebörden av medicinskt innehåll påverkas starkt av modifierare, till exempel negation, som kan ha kritiska indirekt om de feldiagnostiseras. Textanalys för hälso tillstånd stöder negation identifiering för de olika entiteter som anges i texten. 

> [!div class="mx-imgBorder"]
> ![Hälso NEG](../media/ta-for-health/health-negation.png)

---

Se [entitets kategorier](../named-entity-types.md?tabs=health) som returneras av textanalys för hälsan för en fullständig lista över entiteter som stöds. Information om förtroende poäng finns i [textanalys Oh-anteckning](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Språk och regioner som stöds

Textanalys för hälsa stöder endast språk dokument i engelska. 

Textanalys för Health-värdbaserad webb-API är för närvarande endast tillgängligt i dessa regioner: västra USA 2, östra USA 2, centrala USA, norra Europa och Västeuropa.

## <a name="request-access-to-the-public-preview"></a>Begär åtkomst till den offentliga för hands versionen

Fyll i och skicka [formuläret för Cognitive Services begäran](https://aka.ms/csgate) för att begära åtkomst till textanalys för offentlig för hands version av hälso tillstånd. Du debiteras inte för Textanalys för hälso tillstånds användning. 

Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det och skickar ett meddelande till dig med ett beslut.

> [!IMPORTANT]
> * I formuläret måste du använda en e-postadress som är kopplad till ett Azure-prenumerations-ID.
> * Den Azure-resurs som du använder måste ha skapats med det godkända ID: t för Azure-prenumerationen. 
> * Kontrol lera din e-postadress (både Inkorgen och skräppostmappen) för uppdateringar av status för ditt program från Microsoft.

## <a name="using-the-docker-container"></a>Använda Docker-behållaren 

Om du vill köra Textanalys för hälso tillstånds behållare i din egen miljö, följer [du anvisningarna för att ladda ned och installera behållaren](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Använda klientbiblioteket

Med den senaste för hands versionen av Textanalys klient biblioteket kan du anropa Textanalys för hälso tillstånd med hjälp av ett klient objekt. Läs referens dokumentationen och se exemplen på GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran 

### <a name="preparation"></a>Förberedelse

Textanalys för hälsa ger bättre resultat när du ger det mindre mängd text att arbeta med. Detta är motsatt till några av de andra Textanalys funktionerna, till exempel extrahering av nyckel fraser som fungerar bättre på större block med text. För att få bästa möjliga resultat från dessa åtgärder bör du överväga att omstrukturera inmatningarna i enlighet därmed.

Du måste ha JSON-dokument i det här formatet: ID, text och språk. 

Dokument storleken måste vara under 5 120 tecken per dokument. Det maximala antalet dokument som tillåts i en samling finns i artikeln [data begränsningar](../concepts/data-limits.md?tabs=version-3) under begrepp. Samlingen skickas i begäranstexten.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Strukturera API-begäran för den värdbaserade asynkrona webb-API: et

För både behållare och värdbaserade webb-API måste du skapa en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md), ett spiral kommando eller en **API test-konsol** i [TEXTANALYS för hälsovärd-API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) för att snabbt konstruera och skicka en post-begäran till värdbaserade webb-API i önskad region. 

> [!NOTE]
> Både de asynkrona `/analyze` och `/health` slut punkterna är bara tillgängliga i följande regioner: västra USA 2, östra USA 2, centrala USA, norra Europa och Västeuropa.  Kontrol lera att resursen har skapats i någon av dessa regioner för att göra lyckade förfrågningar till dessa slut punkter.

Nedan visas ett exempel på en JSON-fil som är kopplad till Textanalys för hälso-API-begärans INLÄGGs text:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Värdbaserat asynkront webb-API-svar 

Eftersom den här POST-begäran används för att skicka ett jobb för den asynkrona åtgärden finns det ingen text i objektet Response.  Du behöver dock värdet för åtgärds plats nyckeln i svarshuvuden för att göra en GET-begäran för att kontrol lera status för jobbet och utdata.  Nedan visas ett exempel på värdet för åtgärds plats nyckeln i svars huvudet för POST-begäran:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Om du vill kontrol lera jobbets status gör du en GET-begäran till URL: en i värdet för nyckel rubriken åtgärds plats i POST-svaret.  Följande tillstånd används för att visa status för ett jobb:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` och `cancelled` .  

Du kan avbryta ett jobb med en `NotStarted` eller `running` -status med ett Delete http-anrop till samma URL som Get-begäran.  Det finns mer information om BORTTAGNINGs anropet i [textanalys för hälso värdbaserade API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Följande är ett exempel på svaret på en GET-begäran.  Observera att utmatningen är tillgänglig för hämtning tills `expirationDateTime` (24 timmar från den tidpunkt då jobbet skapades) har passerat efter vilken utdata rensas.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Strukturera API-begäran för behållaren

Du kan [använda Postman](text-analytics-how-to-call-api.md) eller exemplet på en spiral förfrågan nedan om du vill skicka en fråga till den behållare som du har distribuerat, och ersätta `serverURL` variabeln med lämpligt värde.  Observera att API-versionen i URL: en för behållaren skiljer sig från den värdbaserade API: n.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Följande JSON är ett exempel på en JSON-fil som är kopplad till Textanalys för hälso-API-begärans INLÄGGs text:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Behållarens svars text

Följande JSON är ett exempel på Textanalys för hälso-API-svars text från det synkrona anropet från behållare:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Identifierings resultat för negation

När du använder negation identifiering kan i vissa fall en enskild negations period hantera flera villkor samtidigt. Negationen av en identifierad entitet representeras i JSON-utdata av flaggans booleska värde `isNegated` , till exempel:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Utmatning för Relations extrahering

Utmatningen för Relations extrahering innehåller URI-referenser till relationens *källa* och dess *mål*. Entiteter med relations roll för `ENTITY` är tilldelade till `target` fältet. Entiteter med relations roll för `ATTRIBUTE` är tilldelade till `source` fältet. Förkortnings relationer innehåller dubbelriktade `source` och `target` fält, och `bidirectional` kommer att anges till `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Namngivna enhets kategorier](../named-entity-types.md)
* [Nyheter](../whats-new.md)
