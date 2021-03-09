---
title: Skapa klient för modell distribuerad som webb tjänst
titleSuffix: Azure Machine Learning
description: Lär dig hur du anropar en webb tjänst slut punkt som genererades när en modell distribuerades från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: e9fb801fce3e47fc83febeddd6f331ce2af207e6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506981"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Konsumera en Azure Machine Learning-modell som distribuerats som en webbtjänst


Om du distribuerar en Azure Machine Learning-modell som en webbtjänst skapas en REST API-slutpunkt. Du kan skicka data till den här slutpunkten så returnerar modellen förutsägelsen. I det här dokumentet får du lära dig hur du skapar klienter för webb tjänsten med hjälp av C#, go, Java och python.

Du skapar en webb tjänst när du distribuerar en modell till din lokala miljö, Azure Container Instances, Azure Kubernetes-tjänsten eller FPGA (Field-programmerbara grind mat ris). Du hämtar den URI som används för att få åtkomst till webb tjänsten med hjälp av [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro). Om autentisering är aktive rad kan du också använda SDK: n för att hämta nycklar eller tokens för autentisering.

Det allmänna arbets flödet för att skapa en klient som använder en Machine Learning-webbtjänst är:

1. Hämta anslutnings informationen med hjälp av SDK.
1. Bestäm vilken typ av begär ande data som används av modellen.
1. Skapa ett program som anropar webb tjänsten.

> [!TIP]
> Exemplen i det här dokumentet skapas manuellt utan användning av OpenAPI-specifikationer (Swagger). Om du har aktiverat en OpenAPI-specifikation för distributionen kan du använda verktyg som [Swagger-CODEGEN](https://github.com/swagger-api/swagger-codegen) för att skapa klient bibliotek för din tjänst.

## <a name="connection-information"></a>Anslutnings information

> [!NOTE]
> Använd Azure Machine Learning SDK för att hämta information om webb tjänsten. Det här är en python SDK. Du kan använda valfritt språk för att skapa en klient för tjänsten.

[Azureml. Core. WebService-](/python/api/azureml-core/azureml.core.webservice%28class%29) klassen innehåller den information du behöver för att skapa en-klient. Följande `Webservice` egenskaper är användbara när du skapar ett klient program:

* `auth_enabled` – Om Key Authentication har Aktiver ATS, `True` annars, `False` .
* `token_auth_enabled` – Om token-autentisering är aktiverat, `True` annars, `False` .
* `scoring_uri` – REST API-adressen.
* `swagger_uri` – Adressen till OpenAPI-specifikationen. Denna URI är tillgänglig om du har aktiverat automatiskt skapande av schema. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Det finns flera sätt att hämta den här informationen för distribuerade webb tjänster:

# <a name="python"></a>[Python](#tab/python)

* När du distribuerar en modell returneras ett `Webservice` objekt med information om tjänsten:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Du kan använda `Webservice.list` för att hämta en lista över distribuerade webb tjänster för modeller i din arbets yta. Du kan lägga till filter för att begränsa listan med information som returneras. Mer information om vad som kan filtreras finns i referens dokumentationen för [WebService. list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) .

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Om du känner till namnet på den distribuerade tjänsten kan du skapa en ny instans av `Webservice` och ange arbets ytan och tjänstens namn som parametrar. Det nya objektet innehåller information om den distribuerade tjänsten.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du känner till namnet på den distribuerade tjänsten använder du kommandot [AZ ml-tjänst show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) :

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Från Azure Machine Learning Studio väljer du __slut punkter__, __slut punkter i real tid__ och sedan slut punktens namn. I information om slut punkten innehåller __rest-slutpunkts__ fältet poängsättnings-URI. __Swagger-URI__ : n innehåller Swagger-URI: n.

---

Följande tabell visar hur dessa URI: er ser ut så här:

| URI-typ | Exempel |
| ----- | ----- |
| Bedömnings-URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger-URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> IP-adressen kommer att skilja sig åt för din distribution. Varje AKS-kluster kommer att ha den egna IP-adress som delas av distributioner till det klustret.

### <a name="secured-web-service"></a>Skyddad webb tjänst

Om du har skyddat den distribuerade webb tjänsten med ett TLS/SSL-certifikat kan du använda [https](https://en.wikipedia.org/wiki/HTTPS) för att ansluta till tjänsten med hjälp av bedömnings-eller Swagger-URI: n. HTTPS skyddar kommunikationen mellan en klient och en webb tjänst genom att kryptera kommunikationen mellan de två. Kryptering använder [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS kallas ibland även *Secure Sockets Layer* (SSL), som var den föregående aktiviteten TLS.

> [!IMPORTANT]
> Webb tjänster som distribueras av Azure Machine Learning endast stöd för TLS version 1,2. När du skapar ett klient program måste du kontrol lera att den har stöd för den här versionen.

Mer information finns i [använda TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autentisering för tjänster

Azure Machine Learning ger dig möjlighet att styra åtkomsten till dina webb tjänster på två sätt.

|Autentiseringsmetod|ACI|AKS|
|---|---|---|
|Nyckel|Inaktiverat som standard| Aktiverat som standard|
|Token| Inte tillgängligt| Inaktiverat som standard |

När du skickar en begäran till en tjänst som skyddas med en nyckel eller token ska du använda __Authorization__ -huvudet för att skicka nyckeln eller token. Nyckeln eller token måste formateras som `Bearer <key-or-token>` , där `<key-or-token>` är nyckel-eller token-värdet.

Den primära skillnaden mellan nycklar och tokens är att **nycklarna är statiska och kan återskapas manuellt**, och **tokens måste uppdateras när de upphör att gälla**. Nyckelbaserad autentisering stöds för Azure Container instance och Azure Kubernetes-tjänsten distribuerade webb tjänster, och tokenbaserad autentisering är **bara** tillgängligt för Azure Kubernetes service-distributioner. Mer information om hur du konfigurerar autentisering finns i [Konfigurera autentisering för modeller som distribueras som webb tjänster](how-to-authenticate-web-service.md).


#### <a name="authentication-with-keys"></a>Autentisering med nycklar

När du aktiverar autentisering för en distribution skapar du automatiskt nycklar för autentisering.

* Autentisering aktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Autentisering inaktive ras som standard när du distribuerar till Azure Container Instances.

Om du vill kontrol lera autentiseringen använder `auth_enabled` du parametern när du skapar eller uppdaterar en distribution.

Om autentisering är aktive rad kan du använda `get_keys` metoden för att hämta en primär och sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Autentisering med token

När du aktiverar token-autentisering för en webb tjänst måste en användare ange en Azure Machine Learning JWT-token för webb tjänsten för att få åtkomst till den. 

* Token-autentisering inaktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering stöds inte när du distribuerar till Azure Container Instances.

Om du vill kontrol lera token-autentisering använder `token_auth_enabled` du parametern när du skapar eller uppdaterar en distribution.

Om token-autentisering har Aktiver ATS kan du använda `get_token` metoden för att hämta en Bearer-token och förfallo tiden för token:

```python
token, refresh_by = service.get_token()
print(token)
```

Om du har [Azure CLI och Machine Learning-tillägget](reference-azure-machine-learning-cli.md)kan du använda följande kommando för att hämta en token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> För närvarande är det enda sättet att hämta token genom att använda Azure Machine Learning SDK eller Azure CLI Machine Learning-tillägget.

Du måste begära en ny token efter det att token har `refresh_by` uppnåtts. 

## <a name="request-data"></a>Begär data

I REST API förväntas bröd texten i begäran vara ett JSON-dokument med följande struktur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Data strukturen måste matcha vad bedömnings skriptet och modellen i tjänsten förväntar sig. Bedömnings skriptet kan ändra data innan de skickas till modellen.

### <a name="binary-data"></a>Binära data

Information om hur du aktiverar stöd för binära data i tjänsten finns i [binära data](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> Aktivering av stöd för binära data sker i den score.py-fil som används av den distribuerade modellen. Använd HTTP-funktionerna i ditt programmeringsspråk från klienten. Följande fragment skickar till exempel innehållet i en JPG-fil till en webb tjänst:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)

Information om hur du aktiverar CORS-stöd i tjänsten finns i [resurs delning mellan ursprung](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Anropa tjänsten (C#)

Det här exemplet visar hur du använder C# för att anropa webb tjänsten som skapats från [träna i Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) -exempel:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Anropa tjänsten (go)

Det här exemplet visar hur du använder Go för att anropa webb tjänsten som skapats från [tåget i Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) -exemplet:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Anropa tjänsten (Java)

Det här exemplet visar hur du använder Java för att anropa webb tjänsten som skapats från [tåget i Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) -exemplet:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Anropa tjänsten (python)

Det här exemplet visar hur du använder python för att anropa webb tjänsten som skapats från [tåget i Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) -exemplet:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

De resultat som returneras liknar följande JSON-dokument:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Webb tjänst schema (OpenAPI-specifikation)

Om du har använt automatisk schema generering med din distribution kan du hämta adressen till OpenAPI-specifikationen för tjänsten med hjälp av [egenskapen swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri). (Till exempel `print(service.swagger_uri)` .) Använd en GET-begäran eller öppna URI: n i en webbläsare för att hämta specifikationen.

Följande JSON-dokument är ett exempel på ett schema (OpenAPI-specifikation) som genereras för en distribution:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Mer information finns i [openapi-specifikation](https://swagger.io/specification/).

Ett verktyg som kan skapa klient bibliotek från specifikationen finns i [Swagger-CODEGEN](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Du kan hämta schema-JSON-dokumentet när du har distribuerat tjänsten. Använd [egenskapen swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) från den distribuerade webb tjänsten (till exempel `service.swagger_uri` ) för att hämta URI: n till den lokala webb tjänstens Swagger-fil.

## <a name="consume-the-service-from-power-bi"></a>Använda tjänsten från Power BI

Power BI stöder användning av Azure Machine Learning webb tjänster för att utöka data i Power BI med förutsägelser. 

Om du vill generera en webb tjänst som stöds för användning i Power BI måste schemat ha stöd för det format som krävs av Power BI. [Lär dig hur du skapar ett schema som stöds av Power BI](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

När webbtjänsten har distribuerats kan den förbrukas från Power BI-dataflöden. [Lär dig hur du använder en Azure Machine Learning-webbtjänst från Power BI](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Nästa steg

Om du vill visa en referens arkitektur för real tids poängsättning av python-och djup inlärnings modeller går du till [Azure Architecture Center](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
