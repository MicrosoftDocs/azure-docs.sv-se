---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386631"
---
Denna Postmanbaserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas.

## <a name="prerequisites"></a>Förutsättningar

* Senaste [**Postman**](https://www.getpostman.com/).
* Du måste ha
    * En [QNA Maker tjänst](../How-To/set-up-qnamaker-service-azure.md)
    * En utbildad och publicerad [kunskaps bas med frågor och svar](../Quickstarts/add-question-metadata-portal.md) som skapats från snabb starten har kon figurer ATS med metadata och en CHI2TEST-chatt.

> [!NOTE]
> När du är redo att generera ett svar på en fråga från kunskaps basen måste du [träna](../Quickstarts/create-publish-knowledge-base.md#save-and-train) och [publicera](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) din kunskaps bas. När din kunskapsbas publiceras visar sidan **publicera** inställningar för HTTP-begäran för att generera ett svar. Fliken **Postman** visar de inställningar som krävs för att generera ett svar.

## <a name="set-up-postman-for-requests"></a>Konfigurera PostMan för förfrågningar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

I den här snabb starten används samma inställningar för Postman **post** -begäran och konfigureras sedan för att publicera text-JSON som skickas till tjänsten baserat på vad du försöker fråga efter.

Använd den här proceduren för att konfigurera Postman och Läs sedan vart och ett av följande avsnitt för att konfigurera inlägget för inläggs texten JSON.

1. På sidan **Inställningar** för kunskaps basen väljer du fliken **Postman** för att se konfigurationen som används för att generera ett svar från kunskaps basen. Kopiera följande information som ska användas i Postman.

    |Name|Inställning|Syfte och värde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Detta är HTTP-metoden och-vägen för URL: en.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Detta är värd för URL: en. Sammanfoga värden och publicera värden för att hämta den fullständiga generateAnswer-URL: en.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Huvud-värdet för för att ge din begäran till Azure. |
    |`Content-type`|`application/json`|Rubrik svärdet för ditt innehåll.|
    ||`{"question":"<Your question>"}`|Bröd texten i POST-begäran som ett JSON-objekt. Det här värdet kommer att ändras i följande avsnitt beroende på vad frågan är tänkt att göra.|

1. Öppna Postman och skapa en ny grundläggande **post** -begäran med dina publicerade kunskaps bas inställningar. I följande avsnitt ändrar du JSON för INLÄGGs texten till att ändra frågan till din kunskaps bas.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I den här snabb starten används samma inställningar för Postman **post** -begäran och konfigureras sedan för att publicera text-JSON som skickas till tjänsten baserat på vad du försöker fråga efter.

Använd den här proceduren för att konfigurera Postman och Läs sedan vart och ett av följande avsnitt för att konfigurera inlägget för inläggs texten JSON.

1. På sidan **Inställningar** för kunskaps basen väljer du fliken **Postman** för att se konfigurationen som används för att generera ett svar från kunskaps basen. Kopiera följande information som ska användas i Postman.

    |Name|Inställning|Syfte och värde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Detta är HTTP-metoden och-vägen för URL: en.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Detta är värd för URL: en. Sammanfoga värden och publicera värden för att hämta den fullständiga generateAnswer-URL: en.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Huvud-värdet för för att auktorisera din begäran. |
    |`Content-type`|`application/json`|Rubrik svärdet för ditt innehåll.|
    ||`{"question":"<Your question>"}`|Bröd texten i POST-begäran som ett JSON-objekt. Det här värdet kommer att ändras i följande avsnitt beroende på vad frågan är tänkt att göra.|

1. Öppna Postman och skapa en ny grundläggande **post** -begäran med dina publicerade kunskaps bas inställningar. I följande avsnitt ändrar du JSON för INLÄGGs texten till att ändra frågan till din kunskaps bas.
---

## <a name="use-metadata-to-filter-answer"></a>Använd metadata för att filtrera svar

I en tidigare snabb start lades metadata till i två QnA-par för att skilja mellan två olika frågor. Lägg till metadata i frågan för att begränsa filtret till bara det relevanta QnA-paret.

1. I Postman ändrar du bara frågans JSON genom att lägga till `strictFilters` egenskapen med namnet/värdet-paret `service:qna_maker` . Body-JSON ska vara:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Frågan är bara ett enstaka ord, `size` som kan returnera något av de två frågorna och svars paren. `strictFilters`Matrisen visar svaret för att minska till bara `qna_maker` svaren.

1. Svaret innehåller bara det svar som uppfyller filter kriterierna.

    Följande svar har formaterats för läsbarhet:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Om det finns ett fråge-och svars par som inte uppfyllde Sök termen men som uppfyller filtret, returneras det inte. I stället returneras det allmänna svaret `No good match found in KB.` .

## <a name="use-debug-query-property"></a>Använd egenskapen Felsök fråga

> [!NOTE]
>Vi rekommenderar inte att du använder egenskapen debug för något beroende. Den här egenskapen har lagts till för att hjälpa produkt teamet vid fel sökning.

Felsöknings information hjälper dig att förstå hur det returnerade svaret har fastställts. Även om det är användbart är det inte nödvändigt. Om du vill generera ett svar med felsöknings information lägger du till `debug` egenskapen:

1. I Postman ändrar du endast Body-JSON genom att lägga till `debug` egenskapen. JSON ska vara:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Svaret innehåller relevant information om svaret. I följande JSON-utdata har en del fel söknings information ersatts med tre punkter.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Använd test kunskaps bas

Om du vill få ett svar från test kunskaps basen använder du `isTest` egenskapen Body.

I Postman ändrar du endast Body-JSON genom att lägga till `isTest` egenskapen. JSON ska vara:

```json
{
    'question':'size',
    'isTest': true
}
```

JSON-svaret använder samma schema som den publicerade kunskaps bas frågan.

> [!NOTE]
> Om test-och publicerade kunskaps baser är identiska, kan det fortfarande finnas viss variation eftersom test indexet delas av alla kunskaps baser i resursen.

## <a name="query-for-a-chit-chat-answer"></a>Fråga efter ett "Chi2test"-svar

1. I Postman ändrar du endast Body JSON till ett konversations slut uttryck från användaren. JSON ska vara:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Svaret innehåller Poäng och svar.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Eftersom frågan `Thank you` exakt matchar en kommunikationsfråga är QnA Maker helt säker med poängen 100. QnA Maker också returnerade alla relaterade frågor, samt egenskapen metadata som innehåller information om metadata tag gen för Chi2test.

## <a name="use-threshold-and-default-answer"></a>Använd tröskel och standard svar

Du kan begära ett minimi tröskelvärde för svaret. Om tröskelvärdet inte uppfylls returneras standard svaret.

1. I Postman ändrar du endast Body JSON till ett konversations slut uttryck från användaren. JSON ska vara:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Kunskaps basen bör inte hitta det svaret eftersom frågans Poäng är 71%. i stället returneras standard svaret som du angav när du skapade kunskaps basen.

    Det returnerade JSON-svaret, inklusive poängen och svaret är:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker returnerade ett resultat av `0` , vilket innebär ingen exakthet. Den returnerade även standard svaret.

1. Ändra tröskelvärdet till 60% och begär frågan igen:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Den returnerade JSON-filen hittade svaret.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```