---
title: 'Självstudie: Skicka e-post med Logic Apps'
description: Lär dig hur du anropar affärsprocesser från din App Service app. Skicka e-post, tweets och Facebook-inlägg, lägg till i e-postlistor och mycket mer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6b15c09162146de7aef8abfcdba40627eb77621c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830942"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Självstudie: Skicka e-post och anropa andra affärsprocesser från App Service

I den här självstudien lär du dig att integrera App Service app med dina affärsprocesser. Detta är vanligt för webbappscenarier, till exempel:

- Skicka e-postbekräftelse för en transaktion
- Lägga till användare i Facebook-gruppen
- Anslut till system från tredje part som SAP, Salesforce osv.
- Exchange B2B-standardmeddelanden

I den här självstudien skickar du e-postmeddelanden med Gmail från din App Service-app med [hjälp av Azure Logic Apps](../logic-apps/logic-apps-overview.md). Det finns andra sätt att skicka e-postmeddelanden från en webbapp, till exempel SMTP-konfiguration som tillhandahålls av ditt språkramverk. Men Logic Apps ger mycket mer kraft till din App Service utan att lägga till komplexitet i koden. Logic Apps ett enkelt konfigurationsgränssnitt för de populäraste företagsintegreringarna och din app kan anropa dem när som helst med en HTTP-begäran.

## <a name="prerequisite"></a>Förutsättning

Distribuera en app med det språkramverk du väljer för att App Service. Om du vill följa en självstudie om hur du distribuerar en exempelapp kan du se nedan:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Självstudie: Skapa en ASP.NET Core- SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Självstudie: Skapa en Node.js- och MongoDB-app i Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Självstudie: Skapa en PHP- och MySQL-app i Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Självstudie: Köra en Python-webbapp (Django) med PostgreSQL i Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Skapa en Ruby- och Postgres-app i Azure App Service på Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Skapa logikappen

1. I [Azure Portal](https://portal.azure.com)skapar du en tom logikapp genom att följa anvisningarna [i Skapa logikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). När du ser Logic Apps **designern återgår** du till den här självstudien.
1. På välkomstsidan för Logic Apps Designer väljer du **När en HTTP-begäran tas emot** under Starta med en vanlig **utlösare**.

    ![Skärmbild som visar välkomstsidan för Logic Apps Designer med När en H T T P-begäran tas emot markerad.](./media/tutorial-send-email/receive-http-request.png)
1. I dialogrutan för När **en HTTP-begäran tas emot väljer du** Använd **exempelnyttolast för att generera schemat**.

    ![Skärmbild som visar dialogrutan When an H T T P request (När en H T T P-begäran) och Use sample payload to generate schema opion (Använd exempelnyttolast för att generera schemaanvändning valt). ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopiera följande JSON-exempel till textrutan och välj **Klar.**

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat genereras nu för de begärandedata som du vill ha. I praktiken kan du bara samla in de faktiska begärandedata som din programkod genererar och låta Azure generera JSON-schemat åt dig. 
1. Längst upp i Logic Apps Designer väljer du **Spara**. 

    Nu kan du se URL:en för http-begärandeutlösaren. Välj kopieringsikonen för att kopiera den för senare användning.

    ![Skärmbild som visar kopieringsikonen för att kopiera U R L för begärandeutlösaren.](./media/tutorial-send-email/http-request-url.png)

    Den här HTTP-begärandedefinitionen är en utlösare för allt du vill göra i den här logikappen, vare sig det är Gmail eller något annat. Senare kommer du att anropa den här URL:en i App Service appen. Mer information om begärandeutlösaren finns i [referensen för HTTP-begäran/-svar.](../connectors/connectors-native-reqres.md)

1. Längst ned i designern klickar du på **Nytt steg,** skriver **Gmail** i sökrutan för åtgärder och letar upp och väljer **Skicka e-post (V2).**
    
    > [!TIP]
    > Du kan söka efter andra typer av integreringar, till exempel SendGrid, MailChimp, Microsoft 365 och SalesForce. Mer information finns i [dokumentationen Logic Apps .](../logic-apps/index.yml)

1. I **Gmail-dialogrutan** väljer du **Logga in och** loggar in på det Gmail-konto som du vill skicka e-postmeddelandet från.

    ![Skärmbild som visar dialogrutan Gmail som du använder för att logga in på det Gmail-konto som du vill skicka e-post från.](./media/tutorial-send-email/gmail-sign-in.png)

1. När du har loggat in klickar **du i** textrutan Till så öppnas dialogrutan dynamiskt innehåll automatiskt.

1. Bredvid åtgärden **When an HTTP request is received (När** en HTTP-begäran tas emot) väljer **du Visa mer.**

    ![Skärmbild som visar knappen Visa mer bredvid åtgärden När en H T T P-begäran tas emot.](./media/tutorial-send-email/expand-dynamic-content.png)

    Du bör nu se de tre egenskaperna från dina JSON-exempeldata som du använde tidigare. I det här steget använder du dessa egenskaper från HTTP-begäran för att skapa ett e-postmeddelande.
1. Eftersom du väljer värdet för fältet Till väljer **du e-post** .  Om du vill kan du inaktivera dialogrutan för dynamiskt innehåll genom att klicka på **Lägg till dynamiskt innehåll.**

    ![Skärmbild som visar e-postalternativet och alternativet Lägg till dynamisk contention markerat.](./media/tutorial-send-email/hide-dynamic-content.png)

1. I **listrutan Lägg till ny parameter** väljer du Ämne **och** **Brödtext.**

1. Klicka i **textrutan** Ämne och välj uppgift på samma **sätt.** Med markören fortfarande i **rutan Ämne** skriver du *created*. 

1. Klicka på **Brödtext** och välj på samma sätt **på grund av**. Flytta markören till vänster om **due och** skriv This work item is *due on*.

    > [!TIP]
    > Om du vill redigera HTML-innehåll direkt  i e-postmeddelandets brödtext väljer du Kodvy överst i Logic Apps Designer-fönstret. Se bara till att du bevarar koden för dynamiskt innehåll (till exempel `@{triggerBody()?['due']}` )
    >
    > ![Skärmbild som visar kodvyn för att visa H T M L-innehåll direkt i e-postmeddelandet.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Lägg sedan till ett asynkront HTTP-svar till HTTP-utlösaren. Mellan HTTP-utlösaren och Gmail-åtgärden klickar du på tecknet **+** och väljer Lägg till en parallell **gren**.

    ![Skärmbild som visar alternativet + tecken och Lägg till en parallell gren markerat.](./media/tutorial-send-email/add-http-response.png)

1. I sökrutan söker du efter **svar** och väljer sedan **åtgärden** Svar.

    ![Skärmbild som visar sökfältet och svarsåtgärden markerad.](./media/tutorial-send-email/choose-response-action.png)

    Som standard skickar svarsåtgärden HTTP 200. Det är tillräckligt bra för den här självstudien. Mer information finns i referensen för [HTTP-begäran/-svar.](../connectors/connectors-native-reqres.md)

1. Längst upp i Logic Apps Designer väljer du **Spara** igen. 

## <a name="add-http-request-code-to-app"></a>Lägga till HTTP-begärandekod i appen

Kontrollera att du har kopierat URL:en för HTTP-begärandeutlösaren från tidigare. Eftersom den innehåller känslig information är det bästa praxis att du inte lägger in den i koden direkt. Med App Service kan du referera till den som en miljövariabel i stället med hjälp av appinställningar. 

I [Cloud Shell](https://shell.azure.com)skapar du appinställningen med följande kommando (ersätt *\<app-name>* , och *\<resource-group-name>* *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

I koden gör du ett HTTP-standardinlägg till URL:en med val annat HTTP-klientspråk som är tillgängligt för ditt språkramverk, med följande konfiguration:

- Begärandetexten innehåller samma JSON-format som du angav för logikappen:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Begäran innehåller rubriken `Content-Type: application/json` . 
- Om du vill optimera prestanda skickar du begäran asynkront om det är möjligt.

Klicka på fliken önskat språk/ramverk nedan för att se ett exempel.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

I ASP.NET kan du skicka HTTP-inlägget med klassen [System.Net.Http.HttpClient.](/dotnet/api/system.net.http.httpclient) Exempel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Om du testar den här koden i exempelappen för [Självstudie: Skapa en ASP.NET-app](app-service-web-tutorial-dotnet-sqldatabase.md)i Azure med SQL Database kan du använda den för att skicka en e-postbekräftelse i åtgärden [Skapa](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)när objektet har lagts `Todo` till. Om du vill använda den asynkrona koden ovan konverterar du åtgärden Skapa till asynkron.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

I ASP.NET Core kan du skicka HTTP-inlägget med klassen [System.Net.Http.HttpClient.](/dotnet/api/system.net.http.httpclient) Exempel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Den här koden är skriven för enkelhetens skull. I praktiken ska du inte instansiera ett `HttpClient` objekt för varje begäran. Följ riktlinjerna i Använda [IHttpClientFactory för att implementera motståndskraftiga HTTP-begäranden.](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

Om du testar den här koden i exempelappen för Självstudie: Skapa en ASP.NET Core- och [SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)kan du använda den för att skicka en e-postbekräftelse i åtgärden [Skapa](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)när objektet har lagts `Todo` till.

### <a name="nodejs"></a>[Node.js](#tab/node)

I Node.js kan du enkelt skicka HTTP-inlägget med ett npm-paket som [axios](https://www.npmjs.com/package/axios). Exempel:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Om du testar den här koden i exempelappen för Självstudie: Skapa en Node.js- och [MongoDB-app](tutorial-nodejs-mongodb-app.md)i Azure kan du använda den för att skicka en e-postbekräftelse i [create-funktionen](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)när artikeln har [sparats.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)

### <a name="php"></a>[PHP](#tab/php)

I PHP kan du enkelt skicka HTTP-inlägget med [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Exempel:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Om du testar den här koden i exempelappen för Självstudie: Skapa en PHP- och [MySQL-app](tutorial-php-mysql-app.md)i Azure kan du använda den för att skicka en e-postbekräftelse i funktionen [Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)precis före retursatsen.

### <a name="python"></a>[Python](#tab/python)

I Python kan du enkelt skicka HTTP-inlägget med [begäranden](https://pypi.org/project/requests/). Exempel:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
import os
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post(os.environ['LOGIC_APP_URL'], data = payload)
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Om du testar den här koden i exempelappen för Självstudie: Köra en [Python-webbapp (Django) med PostgreSQL i Azure App Service](tutorial-python-postgresql-app.md)kan du använda den för att skicka en e-postbekräftelse i funktionen [Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)precis före retursatsen.

### <a name="ruby"></a>[Ruby](#tab/ruby)

I Ruby kan du enkelt skicka HTTP-inlägget med JSONClient. Exempel:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Om du testar den här koden i exempelappen för Skapa en Ruby- och [Postgres-app](tutorial-ruby-postgres-app.md)i [](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) Azure App Service på Linux kan du använda den för att skicka en e-postbekräftelse i åtgärden Skapa [när @task.save ](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)det lyckas.

---

## <a name="more-resources"></a>Fler resurser

[Självstudie: Vara värd för en RESTful-API med CORS i Azure App Service](app-service-web-tutorial-rest-api.md)  
[HTTP-begäran/svarsreferens för Logic Apps](../connectors/connectors-native-reqres.md)  
[Snabbstart: Skapa ditt första arbetsflöde med hjälp av Azure Logic Apps – Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
