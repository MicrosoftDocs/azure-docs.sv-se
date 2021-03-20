---
title: Exempel på anpassade kunskaper (python)
titleSuffix: Azure Cognitive Search
description: För python-utvecklare lär du dig verktyg och tekniker för att skapa en anpassad färdighet med Azure Functions och Visual Studio. Anpassade kunskaper innehåller användardefinierade modeller eller logik som du kan lägga till i en AI-fördefinierad indexerings pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936137"
---
# <a name="example-create-a-custom-skill-using-python"></a>Exempel: skapa en anpassad kunskap med hjälp av python

I det här Azure Kognitiv sökning färdigheter-exemplet får du lära dig hur du skapar en webb-API anpassad kunskap med hjälp av python och Visual Studio Code. I exemplet används en [Azure-funktion](https://azure.microsoft.com/services/functions/) som implementerar det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md).

Den anpassade kunskapen är enkel genom design (den sammanfogar två strängar) så att du kan fokusera på de verktyg och tekniker som används för anpassad kunskaps utveckling i python. När du har lyckats med en enkel kunskap kan du förgrena ut med mer komplexa scenarier.

## <a name="prerequisites"></a>Förutsättningar

+ Granska det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md) för en introduktion till gränssnittet för indata/utdata som en anpassad färdighet ska implementera.

+ Konfigurera din miljö. Vi följde [den här självstudien från slut punkt till slut punkt](/azure/python/tutorial-vs-code-serverless-python-01) för att konfigurera en server lös Azure-funktion med Visual Studio Code och python-tillägg. Självstudien vägleder dig genom installationen av följande verktyg och komponenter: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Python-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Azure Functions-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här exemplet används en Azure-funktion för att demonstrera konceptet som värd för ett webb-API, men andra metoder är möjliga. Så länge du uppfyller [gränssnitts kraven för en kognitiv skicklighet](cognitive-search-custom-skill-interface.md), är den metod du tar oväsentlig. Azure Functions, gör det dock enkelt att skapa en anpassad färdighet.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. I Visual Studio Code, trycker du på F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Create new project...` .

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Därför ska du inte välja en projektmapp som är en del av en arbets yta.

1. Välj ett språk för ditt Function Apps-projekt. I den här självstudien väljer du **python**.
1. Välj python-versionen (version 3.7.5 stöds av Azure Functions)
1. Välj en mall för projektets första funktion. Välj **http-utlösare** för att skapa en http-utlöst funktion i den nya Function-appen.
1. Ange ett funktions namn. I det här fallet ska vi använda **sammanlänkning** 
1. Välj **funktion** som autentiseringsnivå. Det innebär att vi kommer att tillhandahålla en [funktions nyckel](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) för att anropa FUNKTIONENS http-slutpunkt. 
1. Välj hur du vill öppna projektet. I det här steget väljer du **Lägg till i arbets yta** för att skapa Function-appen i den aktuella arbets ytan.

I Visual Studio Code skapas funktionsappsprojektet på en ny arbetsyta. Det här projektet innehåller konfigurationsfilerna [host.json](../azure-functions/functions-host-json.md) och [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), plus eventuella språkspecifika projektfiler. 

En ny HTTP-utlöst funktion skapas också i mappen **sammanlänkningar** i Function app-projektet. I det här fallet är det en fil med namnet " \_ \_ init__. py" med det här innehållet:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Nu ska vi ändra koden så att den följer det [anpassade kunskaps gränssnittet](cognitive-search-custom-skill-interface.md). Ändra koden med följande innehåll:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Metoden **transform_value** utför en åtgärd på en enskild post. Du kan ändra metoden för att uppfylla dina speciella behov. Kom ihåg att utföra alla nödvändiga verifierings verifieringar och returnera eventuella fel och varningar som uppstår om åtgärden inte kunde slutföras för posten.

### <a name="debug-your-code-locally"></a>Felsök din kod lokalt

Med Visual Studio Code är det enkelt att felsöka koden. Tryck på F5 eller gå till **fel söknings** menyn och välj **Starta fel sökning**.

Du kan ställa in eventuella Bryt punkter på koden genom att trycka på F9 på ränte raden.

När du har startat fel sökningen körs funktionen lokalt. Du kan använda ett verktyg som Postman eller Fiddler för att skicka begäran till localhost. Anteckna platsen för den lokala slut punkten i terminalfönstret. 

## <a name="publish-your-function"></a>Publicera din funktion

När du är nöjd med funktions beteendet kan du publicera den.

1. I Visual Studio Code, trycker du på F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer **distribuera till Funktionsapp...**. 

1. Välj den Azure-prenumeration där du vill distribuera programmet.

1. Välj **+ skapa nya Funktionsapp i Azure**

1. Ange ett globalt unikt namn för din Function-app.

1. Välj python-version (python 3.7. x fungerar för den här funktionen).

1. Välj en plats för den nya resursen (till exempel USA, västra 2).

I det här läget skapas de nödvändiga resurserna i din Azure-prenumeration för att vara värd för den nya Azure-funktionen på Azure. Vänta tills distributionen har slutförts. I fönstret Utdata visas distributions processens status.

1. I [Azure Portal](https://portal.azure.com)navigerar du till **alla resurser** och letar efter funktionen som du har publicerat med namnet. Om du har namngett IT- **sammansättning** väljer du resursen.

1. Klicka på knappen **</> Hämta funktions-URL** . Detta gör att du kan kopiera URL: en för att anropa funktionen.

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standard värd nyckeln testar du funktionen enligt följande:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Det här exemplet bör ge samma resultat som du såg tidigare när du körde funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Anslut till din pipeline

Nu när du har en ny anpassad färdighet kan du lägga till den i din färdigheter. Exemplet nedan visar hur du anropar kunskapen för att sammanfoga rubriken och dokumentets författare till ett enda fält som vi anropar merged_title_author. Ersätt `[your-function-url-here]` med URL: en för den nya Azure-funktionen.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade färdighet. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. Klicka på följande länkar om du vill veta mer.

+ [Energi kunskaper: ett lager med anpassade kunskaper](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Lägg till en anpassad färdighet till en AI-pipeline för anrikning](cognitive-search-custom-skill-interface.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa färdigheter (REST)](/rest/api/searchservice/create-skillset)
+ [Så här mappar du omfattande fält](cognitive-search-output-field-mapping.md)