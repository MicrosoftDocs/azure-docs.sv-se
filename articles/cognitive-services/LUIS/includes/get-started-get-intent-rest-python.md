---
title: Kom avsikt med REST-anrop i python
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: bc266cba20e72edea54a71028dc98b75dbd77cd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91545732"
---
[Referens dokumentation](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-predict-with-rest/predict.py)

## <a name="prerequisites"></a>Förutsättningar

* [Python 3,6](https://www.python.org/downloads/) eller senare.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-pizza-app"></a>Skapa pizza-app

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Hämta avsikt från förutsägelse slut punkten

Använd python för att fråga efter [förutsägelse slut punkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelse resultat.

1. Kopiera det här kodfragmentet till en fil med namnet `predict.py` :

    [!code-python[Code snippet](~/cognitive-services-quickstart-code/python/LUIS/python-predict-with-rest/predict.py)]

1. Ersätt värdena som börjar med `YOUR-` med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-APP-ID`|Ditt app-ID. Finns på LUIS-portalen, sidan program inställningar för din app.
    |`YOUR-PREDICTION-KEY`|Din nyckel för att förutsäga 32-tecknen. Finns på LUIS-portalen, sidan Azure-resurser för din app.
    |`YOUR-PREDICTION-ENDPOINT`| URL-slutpunkten för förutsägelse. Finns på LUIS-portalen, sidan Azure-resurser för din app.<br>Till exempel `https://westus.api.cognitive.microsoft.com/`.|

1. Installera `requests` beroendet. `requests`Biblioteket används för att göra HTTP-förfrågningar:

    ```console
    pip install requests
    ```

1. Kör skriptet med följande konsol kommando:

    ```console
    python predict.py
    ```

1. Granska det förutsägelse svar som returneras som JSON:

    ```console
    {'query': 'I want two large pepperoni pizzas on thin crust please', 'prediction': {'topIntent': 'ModifyOrder', 'intents': {'ModifyOrder': {'score': 1.0}, 'None': {'score': 8.55e-09}, 'Greetings': {'score': 1.82222226e-09}, 'CancelOrder': {'score': 1.47272727e-09}, 'Confirmation': {'score': 9.8125e-10}}, 'entities': {'Order': [{'FullPizzaWithModifiers': [{'PizzaType': ['pepperoni pizzas'], 'Size': [['Large']], 'Quantity': [2], 'Crust': [['Thin']], '$instance': {'PizzaType': [{'type': 'PizzaType', 'text': 'pepperoni pizzas', 'startIndex': 17, 'length': 16, 'score': 0.9978157, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Size': [{'type': 'SizeList', 'text': 'large', 'startIndex': 11, 'length': 5, 'score': 0.9984481, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Quantity': [{'type': 'builtin.number', 'text': 'two', 'startIndex': 7, 'length': 3, 'score': 0.999770939, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Crust': [{'type': 'CrustList', 'text': 'thin crust', 'startIndex': 37, 'length': 10, 'score': 0.933985531, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], '$instance': {'FullPizzaWithModifiers': [{'type': 'FullPizzaWithModifiers', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.90681237, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], 'ToppingList': [['Pepperoni']], '$instance': {'Order': [{'type': 'Order', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.9047088, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'ToppingList': [{'type': 'ToppingList', 'text': 'pepperoni', 'startIndex': 17, 'length': 9, 'modelTypeId': 5, 'modelType': 'List Entity Extractor', 'recognitionSources': ['model']}]}}}}
    ```

    JSON-svar formaterat för läsbarhet:

    ```JSON
    {
      'query': 'I want two large pepperoni pizzas on thin crust please',
      'prediction': {
        'topIntent': 'ModifyOrder',
        'intents': {
          'ModifyOrder': {
            'score': 1.0
          },
          'None': {
            'score': 8.55e-9
          },
          'Greetings': {
            'score': 1.82222226e-9
          },
          'CancelOrder': {
            'score': 1.47272727e-9
          },
          'Confirmation': {
            'score': 9.8125e-10
          }
        },
        'entities': {
          'Order': [
            {
              'FullPizzaWithModifiers': [
                {
                  'PizzaType': [
                    'pepperoni pizzas'
                  ],
                  'Size': [
                    [
                      'Large'
                    ]
                  ],
                  'Quantity': [
                    2
                  ],
                  'Crust': [
                    [
                      'Thin'
                    ]
                  ],
                  '$instance': {
                    'PizzaType': [
                      {
                        'type': 'PizzaType',
                        'text': 'pepperoni pizzas',
                        'startIndex': 17,
                        'length': 16,
                        'score': 0.9978157,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Size': [
                      {
                        'type': 'SizeList',
                        'text': 'large',
                        'startIndex': 11,
                        'length': 5,
                        'score': 0.9984481,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Quantity': [
                      {
                        'type': 'builtin.number',
                        'text': 'two',
                        'startIndex': 7,
                        'length': 3,
                        'score': 0.999770939,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Crust': [
                      {
                        'type': 'CrustList',
                        'text': 'thin crust',
                        'startIndex': 37,
                        'length': 10,
                        'score': 0.933985531,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ]
                  }
                }
              ],
              '$instance': {
                'FullPizzaWithModifiers': [
                  {
                    'type': 'FullPizzaWithModifiers',
                    'text': 'two large pepperoni pizzas on thin crust',
                    'startIndex': 7,
                    'length': 40,
                    'score': 0.90681237,
                    'modelTypeId': 1,
                    'modelType': 'Entity Extractor',
                    'recognitionSources': [
                      'model'
                    ]
                  }
                ]
              }
            }
          ],
          'ToppingList': [
            [
              'Pepperoni'
            ]
          ],
          '$instance': {
            'Order': [
              {
                'type': 'Order',
                'text': 'two large pepperoni pizzas on thin crust',
                'startIndex': 7,
                'length': 40,
                'score': 0.9047088,
                'modelTypeId': 1,
                'modelType': 'Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ],
            'ToppingList': [
              {
                'type': 'ToppingList',
                'text': 'pepperoni',
                'startIndex': 17,
                'length': 9,
                'modelTypeId': 5,
                'modelType': 'List Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ]
          }
        }
      }
    }
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../get-started-get-model-rest-apis.md)