---
title: Använda Azure Machine Learning Studio (klassisk) slut punkter i Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder användardefinierade dator språk funktioner i Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 1dc85cb10a9e4300c57ad03900d8c8924988c6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588128"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics"></a>Azure Machine Learning Studio (klassisk) integrering i Stream Analytics
Stream Analytics stöder användardefinierade funktioner som anropar till Azure Machine Learning Studio (klassiska) slut punkter. REST API stöd för den här funktionen beskrivs i [Stream Analytics rest Apis biblioteket](/rest/api/streamanalytics/). Den här artikeln innehåller kompletterande information som krävs för lyckad implementering av den här funktionen i Stream Analytics. En själv studie kurs har också publicerats och är tillgänglig [här](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Översikt: Azure Machine Learning Studio (klassisk) terminologi
Microsoft Azure Machine Learning Studio (klassisk) tillhandahåller ett samarbete, dra och släpp-verktyg som du kan använda för att bygga, testa och distribuera förutsägelse analys lösningar på dina data. Det här verktyget kallas *Azure Machine Learning Studio (klassisk)*. Studio (klassisk) används för att interagera med Machine Learning-resurser och enkelt bygga, testa och iterera på din design. Dessa resurser och deras definitioner visas nedan.

* **Arbets yta**: *arbets ytan* är en behållare som innehåller alla andra maskin inlärnings resurser tillsammans i en behållare för hantering och kontroll.
* **Experiment**: *experiment* skapas av data forskare för att använda data uppsättningar och träna en maskin inlärnings modell.
* **Slut punkt**: *slut punkter* är det Studio-objekt (klassisk) som används för att ta med funktioner som indata, tillämpa en angiven Machine Learning-modell och returnera Poäng resultat.
* **Bedömnings WebService**: en *bedömnings WebService* är en samling slut punkter som nämnts ovan.

Varje slut punkt har API: er för batch-körning och synkron körning. Stream Analytics använder synkron körning. Den aktuella tjänsten kallas för en [begäran/svar-tjänst](../machine-learning/classic/consume-web-services.md) i Azure Machine Learning Studio (klassisk).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Studio (klassiska) resurser som krävs för Stream Analytics jobb
För att Stream Analytics jobb bearbetning, krävs en begäran/svars slut punkt, en [apiKey](../machine-learning/classic/consume-web-services.md)och en Swagger-definition för lyckad körning. Stream Analytics har en ytterligare slut punkt som konstruerar URL: en för Swagger-slutpunkten, letar upp gränssnittet och returnerar en fördefinierad UDF-definition för användaren.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Konfigurera en Stream Analytics och Studio (klassisk) UDF via REST API
Med hjälp av REST API: er kan du konfigurera jobbet så att det anropar Studio (klassiska) funktioner. Stegen är följande:

1. Skapa ett Stream Analytics-jobb
2. Definiera inmatade värden
3. Definiera utdata
4. Skapa en användardefinierad funktion (UDF)
5. Skriv en Stream Analytics-omvandling som anropar UDF
6. Starta jobbet

## <a name="creating-a-udf-with-basic-properties"></a>Skapa en UDF med grundläggande egenskaper
Följande exempel kod skapar exempelvis en skalär UDF med namnet *newudf* som binder till en Azure Machine Learning Studio (klassisk) slut punkt. Observera att *slut punkten* (tjänst-URI) finns på API-hjälp sidan för den valda tjänsten och att *apiKey* finns på huvud sidan för tjänster.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Exempel på begär ande text:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Anropa RetrieveDefaultDefinition-slutpunkt för standard UDF
När Skeleton UDF-filen har skapats behövs en fullständig definition av UDF. RetrieveDefaultDefinition-slutpunkten hjälper dig att hämta standard definitionen för en skalär funktion som är kopplad till en Azure Machine Learning Studio (klassisk) slut punkt. I nytto lasten nedan måste du hämta den UDF-definition som är standard för en skalär funktion som är kopplad till en Studio-slutpunkt (klassisk). Den faktiska slut punkten anges inte eftersom den redan har angetts under begäran om placering. Stream Analytics anropar slut punkten som anges i begäran om den anges explicit. Annars används den som ursprungligen refererade till. Här är UDF-filen en enskild sträng parameter (en mening) och returnerar en enda utmatning av typen sträng som anger etiketten "sentiment" för den meningen.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Exempel på begär ande text:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Exempel på utdata ser ut ungefär som nedan.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Korrigering av UDF med svaret
Nu måste UDF-filen korrigeras med föregående svar, som du ser nedan.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Begär ande text (utdata från RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementera Stream Analytics-transformering för att anropa UDF
Fråga sedan UDF (här heter scoreTweet) för varje indata-händelse och skriv ett svar för händelsen till utdata.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)
