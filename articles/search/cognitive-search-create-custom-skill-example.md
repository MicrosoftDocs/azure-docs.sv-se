---
title: Anpassat kunskaps exempel som använder API för entitetsökning i Bing
titleSuffix: Azure Cognitive Search
description: Visar hur du använder tjänsten Entitetssökning i Bing i en anpassad färdighet som är mappad till en AI-fördefinierad indexerings pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89020192"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exempel: skapa en anpassad kunskap med hjälp av API för entitetsökning i Bing

I det här exemplet lär du dig hur du skapar en anpassad webb-API-färdighet. Den här kunskapen tar emot platser, offentliga uppgifter och organisationer och returnerar beskrivningar för dem. I exemplet används en [Azure-funktion](https://azure.microsoft.com/services/functions/) för att omsluta [API för entitetsökning i Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) så att den implementerar det anpassade kunskaps gränssnittet.

## <a name="prerequisites"></a>Förutsättningar

+ Läs om det [anpassade kunskaps gränssnitts](cognitive-search-custom-skill-interface.md) artikel om du inte är bekant med gränssnittet för indata/utdata som en anpassad färdighet ska implementera.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) eller senare, inklusive arbets belastningen Azure Development.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här exemplet används en Azure-funktion som värd för ett webb-API, men det är inte obligatoriskt.  Så länge du uppfyller [gränssnitts kraven för en kognitiv skicklighet](cognitive-search-custom-skill-interface.md), är den metod du tar oväsentlig. Azure Functions, gör det dock enkelt att skapa en anpassad färdighet.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

1. Välj **Nytt** > **Projekt** på Arkiv-menyn i Visual Studio.

1. I dialog rutan nytt projekt väljer du **installerad**, expanderar **Visual C#**  >  -**moln**, väljer **Azure Functions**, skriver ett namn för projektet och väljer **OK**. Namnet på Function-appen måste vara giltigt som C#-namn område, så Använd inte under streck, bindestreck eller andra icke-alfanumeriska tecken.

1. Välj **Azure Functions v2 (.net Core)**. Du kan också göra det med version 1, men koden som skrivs nedan baseras på v2-mallen.

1. Välj den typ som ska vara **http-utlösare**

1. För lagrings kontot kan du välja **ingen** eftersom du inte behöver något lagrings utrymme för den här funktionen.

1. Välj **OK** för att skapa funktionen projekt och http-utlöst funktion.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Ändra koden för att anropa tjänsten Entitetssökning i Bing

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet *FunctionName* i metoden anger namnet på funktionen. Attributet *HttpTrigger* anger att funktionen utlöses av en HTTP-förfrågan.

Ersätt nu allt innehåll i filen *Function1. cs* med följande kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Se till att ange ditt eget *nyckel* värde i `key` konstanten baserat på den nyckel du fick när du registrerade dig för API för sökning i Bing-entiteten.

I det här exemplet ingår all nödvändig kod i en enda fil för enkelhetens skull. Du kan hitta en något mer strukturerad version av samma färdighet i [lagrings platsen för energi kunskaper](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Självklart kan du byta namn på filen från `Function1.cs` till `BingEntitySearch.cs` .

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

Tryck på **F5** för att köra program-och test funktions beteenden. I det här fallet använder vi funktionen nedan för att leta upp två entiteter. Använd Postman eller Fiddler för att utfärda ett anrop som det som visas nedan:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Svarsåtgärder
Du bör se ett svar som liknar följande exempel:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen i Azure

När du är nöjd med funktions beteendet kan du publicera den.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **Skapa ny**  >  **publicera**.

1. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto....**

1. Följ anvisningarna på skärmen. Du uppmanas att ange ett unikt namn för din app service, Azure-prenumerationen, resurs gruppen, värd planen och det lagrings konto som du vill använda. Du kan skapa en ny resurs grupp, en ny värd plan och ett lagrings konto om du inte redan har dessa. När du är färdig väljer du **skapa**

1. När distributionen är klar, Lägg märke till webbplatsens URL. Det är adressen till din Function-app i Azure. 

1. I [Azure Portal](https://portal.azure.com)navigerar du till resurs gruppen och letar efter `EntitySearch` funktionen som du har publicerat. Under avsnittet **Hantera** bör du se värd nycklar. Välj **kopierings** ikonen för *standard* värd nyckeln.  

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standard värd nyckeln testar du funktionen enligt följande:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Det här exemplet bör ge samma resultat som du såg tidigare när du körde funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Anslut till din pipeline
Nu när du har en ny anpassad färdighet kan du lägga till den i din färdigheter. Exemplet nedan visar hur du anropar kunskapen för att lägga till beskrivningar till organisationer i dokumentet (det kan även utökas för att även fungera på platser och i personer). Ersätt `[your-entity-search-app-name]` med namnet på din app.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Här är vi inräknat på den inbyggda [enhets igenkännings kompetensen](cognitive-search-skill-entity-recognition.md) som ska finnas i färdigheter och för att kunna använda dokumentet med listan över organisationer. För referens är det här en kunskaps konfiguration för enhets extrahering som skulle räcka för att skapa data som vi behöver:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade färdighet. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. Klicka på följande länkar om du vill veta mer.

+ [Energi kunskaper: ett lager med anpassade kunskaper](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Lägg till en anpassad färdighet till en AI-pipeline för anrikning](cognitive-search-custom-skill-interface.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa färdigheter (REST)](/rest/api/searchservice/create-skillset)
+ [Så här mappar du omfattande fält](cognitive-search-output-field-mapping.md)