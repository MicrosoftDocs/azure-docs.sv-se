---
title: 'Snabb start: skapa app-LUIS'
description: Den här snabb starten visar hur du skapar en LUIS-app som använder den färdiga domänen `HomeAutomation` för att aktivera och inaktivera lampor och apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277036"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda en färdig hemautomatiseringsapp

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**.

### <a name="create-an-application"></a>Skapa ett program

Om du vill skapa ett program klickar du på  **+ ny app**. 

I fönstret som visas anger du följande information:

|Name  |Beskrivning  |
|---------|---------|
|AName     | Ett namn för din app. Till exempel "Start automatisering".        |
|Kultur     | Det språk som din app förstår och pratar med.   |
|Beskrivning | En beskrivning av din app.
|Förutsägelse resurs | Den förutsägelse resurs som tar emot frågor. |

Välj **Klar**.

>[!NOTE]
>Kulturen kan inte ändras när appen har skapats.

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

1. I det vänstra navigerings fältet väljer du **fördefinierade domäner**.
1. Sök efter **HomeAutomation**.
1. Välj **Lägg till domän** på HomeAutomation-kortet.

    > [!div class="mx-imgBorder"]
    > ![Välj "förinställda domäner" och Sök efter "HomeAutomation". Välj Lägg till domän på HomeAutomation-kortet.](media/luis-quickstart-new-app/home-automation.png)

    När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

## <a name="intents-and-entities"></a>Avsikter och entiteter

1. Välj **avsikter** i den vänstra navigerings menyn för att se HomeAutomation domän avsikter. Den har exempel yttranden, till exempel `HomeAutomation.QueryState` och     `HomeAutomation.SetDevice` .

    > [!NOTE]
    > **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app.

1. Välj avsikten **HomeAutomation.TurnOff** (stänga av). Avsikten innehåller en lista över exempel-yttranden som är märkta med entiteter.

    > [!div class="mx-imgBorder"]
    > [![Skärmbild av avsikten HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Om du vill visa entiteternas entiteter väljer du **entiteter**. Om du klickar på en av entiteterna, till exempel **HomeAutomation. enhets** namn, visas en lista över värden som är kopplade till den. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Alternativ text för bild" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testa appen
När du har tränat appen kan du testa den.

1. Välj **test** i det övre högra navigerings fönstret.

1. Skriv ett test-uttryck som `Turn off the lights` i det interaktiva test fönstret och tryck på RETUR. Stäng till exempel *ljuset*.

    I det här exemplet `Turn off the lights` identifieras korrekt som det främsta bedömnings syftet för **HomeAutomation. TurnOff**.

    ![Skärmbild av testpanel med yttranden markerade](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Välj **kontrol lera** om du vill visa mer information om förutsägelsen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av test panelen med gransknings information](media/luis-quickstart-new-app/test.png)

1. Stäng test fönstret.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

För att kunna ta emot en LUIS förutsägelse i en chatt-robot eller något annat klient program måste du publicera appen till förutsägelse slut punkten.

1. Välj **publicera** i det övre högra hörnet i fönstret.

1. Välj **produktions** plats och välj sedan **Slutför**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av LUIS publicera till slut punkt](media/howto-publish/publish-app-popup.png)

1. Välj länken **åtkomst till slut punkts-URL: er** i meddelandet för att gå till sidan **Azure-resurser** . Slut punkts-URL: erna visas som **exempel fråga**.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Fråga v3 API förutsägelse-slutpunkten

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. I adress fältet i webbläsaren kontrollerar du att följande namn och värde fält finns i URL: en för frågesträngen. Om de inte finns i frågesträngen lägger du till dem:

    |Namn/värde-par|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. I webbläsarens Adress fält går du till slutet av URL: en och anger `turn off the living room light` värdet för _fråga_ och trycker på RETUR.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Iterativ app-utveckling för LUIS](./luis-concept-app-iteration.md)