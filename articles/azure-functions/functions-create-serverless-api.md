---
title: Anpassa en HTTP-slutpunkt i Azure Functions
description: Lär dig hur du anpassar en HTTP trigger-slutpunkt i Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683750"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Anpassa en HTTP-slutpunkt i Azure Functions

I den här artikeln får du lära dig hur Azure Functions gör att du kan bygga mycket skalbara API: er. Azure Functions levereras med en samling inbyggda HTTP-utlösare och bindningar som gör det enkelt att skapa en slut punkt på flera olika språk, inklusive Node.js, C# med mera. I den här artikeln ska du anpassa en HTTP-utlösare för att hantera vissa åtgärder i din API-design. Du kommer också att förbereda för att utveckla ditt API genom att integrera det med Azure Functions-proxyservrar och ställa in modellerade API: er. Dessa uppgifter utförs ovanpå funktionerna i Server lös beräknings miljö, så du behöver inte bekymra dig om att skala resurser – du kan bara fokusera på din API-logik.

## <a name="prerequisites"></a>Förutsättningar 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Den resulterande funktionen kommer att användas för resten av den här artikeln.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="customize-your-http-function"></a>Anpassa din HTTP-funktion

Som standard är din HTTP-utlösare konfigurerad att godkänna alla HTTP-metoder. Du kan också använda standard-URL: en `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . I det här avsnittet ändrar du funktionen så att den endast svarar på GET-begäranden `/api/hello` . 

1. Gå till din funktion i Azure-portalen. Välj **integration** i den vänstra menyn och välj sedan **http (req)** under **utlösare**.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Anpassa en HTTP-funktion":::

1. Använd inställningarna för HTTP-utlösare som anges i följande tabell.

    | Fält | Exempelvärde | Description |
    |---|---|---|
    | Flödesmall | /hello | Avgör vilket flöde som används för att anropa den här funktionen |
    | Auktoriseringsnivå | Anonym | Valfritt: Gör din funktion tillgänglig utan API-nyckel |
    | Valda HTTP-metoder | GET | Tillåter endast de valda HTTP-metoderna att användas för att anropa den här funktionen |

    Du har inte angett `/api` prefixet för bas Sök väg i Route-mallen eftersom den hanteras av en global inställning.

1. Välj **Spara**.

Mer information om hur du anpassar HTTP Functions finns i [Azure Functions HTTP-bindningar](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Testa ditt API

Testa sedan din funktion för att se hur den fungerar med den nya API-ytan:
1. På funktions sidan väljer du **kod + test** på den vänstra menyn.

1. Välj **Hämta funktions webb adress** från den översta menyn och kopiera URL: en. Bekräfta att den nu använder `/api/hello` sökvägen.
 
1. Kopiera webbadressen till en ny webbläsarflik eller den REST-klient som du föredrar. 

   Webbläsare använder GET som standard.
 
1. Lägg till parametrar i frågesträngen i din URL. 

   Ett exempel är `/api/hello/?name=John`.
 
1. Tryck på RETUR för att bekräfta att det fungerar. Svaret "*Hej John*" bör visas.

1. Du kan också prova att anropa slut punkten med en annan HTTP-metod för att bekräfta att funktionen inte körs. Det gör du genom att använda en REST-klient, till exempel sväng, Postman eller Fiddler.

## <a name="proxies-overview"></a>Översikt över proxyservrar

I nästa avsnitt kommer du att få fram ditt API via en proxyserver. Med Azure Functions Proxies kan du vidarebefordra begäranden till andra resurser. Du definierar en HTTP-slutpunkt precis som med HTTP-utlösare. I stället för att skriva kod som ska köras när slut punkten anropas, anger du dock en URL till en fjärrimplementering. På så sätt kan du skapa flera API-källor i en enda API-yta, vilket är enkelt för klienter att använda, vilket är användbart om du vill skapa ditt API som mikrotjänster.

En proxy kan peka på en HTTP-resurs, till exempel:
- Azure Functions 
- API-appar i [Azure App Service](../app-service/overview.md)
- Docker-container i [App Service i Linux](../app-service/overview.md#app-service-on-linux)
- Övriga värdbaserade API:er

Läs mer om proxyservrar i [Arbeta med Azure Functions Proxies].

## <a name="create-your-first-proxy"></a>Skapa din första proxy

I det här avsnittet skapar du en ny proxy som fungerar som en klient del för ditt övergripande API. 

### <a name="setting-up-the-frontend-environment"></a>Konfigurera klientdelsmiljön

Upprepa stegen för att [Skapa en funktionsapp](./functions-get-started.md) och skapa en ny funktionsapp som du sedan skapar proxyservern i. Den här nya appens URL fungerar som klient del för vårt API och Function-appen som du redigerade tidigare fungerar som en server del.

1. Navigera till din nya klientdelsfunktionsapp i portalen.
1. Välj **Plattformsfunktioner** och välj sedan **Programinställningar**.
1. Rulla ned till **program inställningar**, där nyckel/värde-par lagras och skapa en ny inställning med nyckeln `HELLO_HOST` . Ange värdet till klientdelsfunktionsappens värd, till exempel `<YourBackendApp>.azurewebsites.net`. Värdet är en del av den URL som du kopierade tidigare när du testar din HTTP-funktion. Du kan referera till den här inställningen i konfigurationen senare.

    > [!NOTE] 
    > Programinställningar rekommenderas för värdkonfigurationen för att förhindra ett hårdkodat miljöberoende för proxyn. Om du använder programinställningar kan du flytta proxykonfigurationen mellan miljöer, och de miljöspecifika programinställningarna tillämpas.

1. Välj **Spara**.

### <a name="creating-a-proxy-on-the-frontend"></a>Skapa en proxy på klientdelen

1. Gå tillbaka till klient delens funktions app i portalen.

1. Välj **proxyservrar** på menyn till vänster och välj sedan **Lägg till**. 

1. På sidan **ny proxy** använder du inställningarna i följande tabell och väljer sedan **skapa**.

    | Fält | Exempelvärde | Beskrivning |
    |---|---|---|
    | Name | HelloProxy | Ett eget namn som endast används för hantering |
    | Flödesmall | /api/remotehello | Avgör vilket flöde som används för att anropa den här proxyn |
    | Webbadress för serverdel | https://%HELLO_HOST%/api/hello | Anger den slutpunkt som begäran ska nå via proxy |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Skapa en proxy":::

    Azure Functions-proxyservrar tillhandahåller inte `/api` prefixet för bas Sök väg, som måste ingå i Route-mallen. `%HELLO_HOST%`Syntaxen refererar till den app-inställning som du skapade tidigare. Den matchade webbadressen pekar på din ursprungliga funktion.

1. Prova din nya proxy genom att kopiera URL: en för proxyn och testa den i webbläsaren eller med din favorit-HTTP-klient:
    - För en anonym funktion används:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - För en funktion som använder auktorisering:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Skapa ett fingerat API

Sedan använder du en proxy för att skapa ett modell-API för din lösning. Med den här proxyn kan klient utveckling förloppet utan att Server delen implementeras fullständigt. Senare i utvecklingen kan du skapa en ny function-app som har stöd för den här logiken och dirigera om proxyn till den.

För att skapa den här bildmappen API skapar vi en ny proxy, den här gången med hjälp av [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Börja med att navigera till din funktionsapp i portalen. Välj **plattforms funktioner** och Sök **App Service Editor** under **utvecklingsverktyg** . App Service Editor öppnas på en ny flik.

Välj `proxies.json` i det vänstra navigeringsfältet. Den här filen lagrar konfigurationen för alla dina proxyservrar. Om du använder en av [funktions distributions metoderna](./functions-continuous-deployment.md), behåller du den här filen i käll kontrollen. Läs mer om den här filen i [Avancerad proxykonfiguration](./functions-proxies.md#advanced-configuration).

Om du har följt den här typen av proxies.jsbör det se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Nu ska du lägga till ditt modell-API. Ersätt proxies.jspå filen med följande kod:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Den här koden lägger till en ny proxy, `GetUserByName` utan `backendUri` egenskapen. Istället för att anropa en annan resurs modifierar den standardsvaret från proxyservrar med en åsidosättning av svaret. Åsidosättningar av begäranden och svar kan också användas tillsammans med en serverdels-URL. Den här tekniken är särskilt användbar vid proxyering till ett äldre system, där du kan behöva ändra huvuden, frågeparametrar och så vidare. Läs mer om åsidosättningar av begäranden och svar i [Ändra begäranden och svar i proxyservrar](./functions-proxies.md).

Testa ditt fingerade API genom att anropa slutpunkten `<YourProxyApp>.azurewebsites.net/api/users/{username}` med en webbläsare eller den REST-klient du föredrar. Se till att ersätta _{username}_ med ett strängvärde som representerar ett användarnamn.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar och anpassar ett API på Azure Functions. Du lärde dig också att hantera flera API:er, även fingerade, tillsammans som en enhetlig API-yta. Du kan använda de här metoderna för att bygga ut API:er i olika komplexitetsgrader medan de körs på den serverlösa beräkningsmodell som Azure Functions ger.

Följande referenser kan vara till hjälp när du utvecklar ditt API vidare:

- [HTTP-bindningar för Azure Functions](./functions-bindings-http-webhook.md)
- [Arbeta med proxyservrar i Azure Functions]
- [Dokumentera ett Azure Functions-API (förhandsversion)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Arbeta med proxyservrar i Azure Functions]: ./functions-proxies.md
