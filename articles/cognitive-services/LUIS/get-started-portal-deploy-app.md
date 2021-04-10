---
title: 'Snabb start: Distribuera en app med LUIS-portalen'
description: Den här snabb starten visar hur du distribuerar en app genom att skapa en förutsägelse slut punkts resurs, tilldela resursen, utbildningen och publicera appen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: f7d5d473b8fe24d8dd5018a718ea96f286b1c030
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275846"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snabb start: Distribuera en app i LUIS-portalen

När din LUIS-app är redo att returnera uttryck förutsägelser till ett klient program (till exempel en chatt-robot) måste du distribuera appen till förutsägelse slut punkten.

I den här snabb starten lär du dig att distribuera ett program. Du skapar en förutsägelse slut punkt resurs, tilldelar resursen till appen, tränar appen och publicerar appen.

## <a name="prerequisites"></a>Förutsättningar

* Skaffa en [Azure-prenumeration](https://azure.microsoft.com/free).
* Slutför den [föregående Portal snabb](get-started-portal-build-app.md) starten eller [Ladda ned och importera appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* [Migrera till en Azure-resurs](luis-migration-authoring.md)om du har appar som är i förväg inaktuella Azure-resurs-autentisering. Vissa Portal sidor ser annorlunda ut när e-postautentiseringen är aktiv.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Tilldela resurs nyckeln till LUIS-appen i LUIS-portalen

Varje gång du skapar en ny redigerare eller en fråga förutsägelse resurs för LUIS, måste du tilldela resursen till LUIS-appen. När du har tilldelat dig behöver du inte göra detta steg igen om du inte skapar en ny resurs. Du kan skapa en ny resurs för att expandera regionerna i appen eller för att stödja ett större antal förutsägelse frågor.

1. Logga in på [Luis-portalen](https://www.luis.ai) och välj **myEnglishApp** -appen från listan appar.

1. Välj **Hantera** på menyn längst upp till höger och välj sedan **Azure-resurser**.

1. Om du vill lägga till LUIS väljer du **Lägg till förutsägelse resurs**.

    ![Om du vill lägga till LUIS förutsägelse resurs väljer du Lägg till förutsägelse resurs](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Välj klient, prenumeration och resurs namn. Välj **tilldela resurs**.

   > [!div class="mx-imgBorder"]
   > ![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Slutför samma steg för att lägga till redigerings nyckeln till din app.

1. Hitta den nya raden i tabellen för den nya förutsägelse resursen och kopiera slut punkts-URL: en. Den är korrekt konstruerad för att göra en `HTTP GET` begäran till Luis API-slutpunkten för en förutsägelse.

> [!TIP]
> Om du tänker använda aktiv inlärning för att förbättra LUIS-appen väljer du **ändra** frågeparametrar och väljer **Spara loggar**. Den här åtgärden ändrar exempel-URL genom att lägga till `log=true` parametern QueryString. Kopiera och Använd fråge-URL: en för den ändrade exemplet när du gör förutsägelse frågor till körnings slut punkten.

## <a name="train-the-app"></a>Träna appen

Om du har ändrat appen sedan du tränade den sist, [tränar](get-started-portal-build-app.md) du appen.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publicera appen till förutsägelse slut punkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Förutsägelse slut punkts förfrågan

I-portalen är det i `query=` slutet av URL: en där användarens uttryck läggs till i get-begäran. Efter `query=` anger du samma användar-uttryck som användes i slutet av föregående snabb start:

```Is there a form named hrf-234098```

Se till att frågesträngen innehåller följande par:

* `show-all-intents=true`
* `verbose=true`

Webbläsaren visar svaret:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

Om du vill se samma informations nivå i test fönstret måste du publicera appen. När appen har publicerats väljer du **Jämför med publicerad** i test fönstret. Använd **Visa JSON-vy** i det publicerade test fönstret för att se samma JSON som i föregående steg. På så sätt kan du jämföra ändringar i den aktuella appen som du arbetar med med en app som publiceras till slut punkten.

[![Jämför den aktuella redigeringen jämfört med den publicerade versionen av appen](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starten väljer du **Mina appar** på den översta navigerings menyn. Markera kryss rutan för appen i listan och välj sedan **ta bort** från verktygsfältet kontext ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Iterativ app-utveckling för LUIS](./luis-concept-app-iteration.md)