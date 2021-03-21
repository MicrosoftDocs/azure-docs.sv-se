---
title: Så här frågar du efter förutsägelser med en webb läsar LUIS
description: I den här artikeln använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text i en webbläsare.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a3bad4ab69f6950f83db9cf1f49cfa4cb7c7b5f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040132"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Så här frågar du förutsägelse körning med användar text

Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna skicka frågor till en offentlig app:

* Din Language Understanding (LUIS) resurs information:
    * **Förutsägelse nyckel** – som kan hämtas från [Luis-portalen](https://www.luis.ai/). Om du inte redan har en prenumeration för att skapa en nyckel kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/cognitive-services).
    * Under **domän för förutsägelse slut punkt** – under domänen är även **namnet** på din Luis-resurs.
* Ett LUIS app-ID – Använd ID: t för den offentliga IoT-appen `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="use-the-browser-to-see-predictions"></a>Använd webbläsaren för att se förutsägelser

1. Öppna en webbläsare.
1. Använd de fullständiga URL: erna nedan och Ersätt `YOUR-KEY` med din egen Luis-förutsägelse nyckel. Begär Anden är GET-begäranden och inkluderar auktoriseringen med LUIS-förutsägelse nyckeln som en frågesträngparametern.

    #### <a name="v3-prediction-request"></a>[V3 förutsägelse förfrågan](#tab/V3-1-1)


    Formatet på v3-URL: en för en begäran om **hämtning** av slut punkt (per fack) är:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 förutsägelse förfrågan](#tab/V2-1-2)

    Formatet på v2-URL: en för en begäran om att **Hämta** slut punkter är:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Du ser ett JSON-resultat i webbläsaren som indikerar att LUIS har identifierat avsikten `HomeAutomation.TurnOn` som troligaste avsikt och entiteten `HomeAutomation.Operation` med värdet `on`.

    #### <a name="v3-prediction-response"></a>[V3 förutsägelse svar](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 förutsägelse svar](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Om du vill se alla avsikter lägger du till lämplig frågesträngparametern.

    #### <a name="v3-prediction-endpoint"></a>[V3 förutsägelse slut punkt](#tab/V3-3-1)

    Lägg till `show-all-intents=true` i slutet av QueryString för att **Visa alla avsikter**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2-förutsägelse slut punkt](#tab/V2)

    Lägg till `verbose=true` i slutet av QueryString för att **Visa alla avsikter**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

Läs mer om:
* [V3 förutsägelse slut punkt](luis-migration-api-v3.md)
* [Anpassade under domäner](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Skapa en app i LUIS-portalen](get-started-portal-build-app.md)
