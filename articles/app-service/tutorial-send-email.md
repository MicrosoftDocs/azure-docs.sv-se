---
title: 'Självstudie: skicka e-post med Logic Apps'
description: Lär dig hur du anropar affärs processer från din App Service-app. Skicka e-post, tweets och Facebook-inlägg, Lägg till i e-postlistor och mycket mer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95014670"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Självstudie: skicka e-post och anropa andra affärs processer från App Service

I den här självstudien får du lära dig hur du integrerar din App Service-app med dina affärs processer. Detta är vanligt för scenarier med webb program, till exempel:

- Skicka bekräftelse meddelande för en transaktion
- Lägg till användare i Facebook-gruppen
- Ansluta till tredje parts system som SAP, Salesforce osv.
- Exchange standard B2B-meddelanden

I den här självstudien skickar du e-post med Gmail från din App Service-app med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Det finns andra sätt att skicka e-postmeddelanden från en webbapp, till exempel SMTP-konfigurationen som tillhandahålls av ditt språk ramverk. Logic Apps ger dock mycket mer kraft till din App Service-app utan att öka komplexiteten i din kod. Logic Apps tillhandahåller ett enkelt konfigurations gränssnitt för de mest populära företags integreringarna och din app kan anropa dem när som helst med en HTTP-begäran.

## <a name="prerequisite"></a>Förutsättning

Distribuera en app med det språk ramverk som du väljer att App Service. Om du vill följa en självstudie för att distribuera en exempel-app, se nedan:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Självstudie: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Självstudie: Bygg en Node.js-och MongoDB-app i Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Självstudie: bygga en PHP-och MySQL-app i Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Självstudie: köra en python-webbapp (django) med PostgreSQL i Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Skapa en Ruby- och Postgres-app i Azure App Service på Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Skapa Logic-appen

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app genom att följa anvisningarna i [skapa din Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). När du ser **Logic Apps designer** går du tillbaka till den här självstudien.
1. På Välkomst sidan för Logic Apps designer väljer du **när en HTTP-begäran tas emot** under **starta med en gemensam utlösare**.

    ![Skärm bild som visar Välkomst sidan för Logic Apps designer med när en H-T-P-begäran tas emot.](./media/tutorial-send-email/receive-http-request.png)
1. I dialog rutan **när en HTTP-begäran tas emot** väljer **du Använd exempel nytto last för att generera schemat**.

    ![Skärm bild som visar dialog rutan när en H T T P P-begäran och den använda exempel nytto lasten för att generera schema opion har valts. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopiera följande exempel-JSON till text rutan och välj **färdig**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat har nu skapats för de begärda data du vill ha. I praktiken kan du bara samla in de faktiska begär ande data som din program kod genererar och låta Azure generera JSON-schemat åt dig. 
1. Välj **Spara** längst upp i Logic Apps designer. 

    Nu kan du se URL: en för din utlösare för HTTP-begäran. Välj kopierings ikonen för att kopiera den för senare användning.

    ![Skärm bild som visar kopierings ikonen för att kopiera U R L i din f T T P-utlösare för begäran.](./media/tutorial-send-email/http-request-url.png)

    Den här definitionen av HTTP-begäran är en utlösare för allt du vill göra i den här Logic-appen, vara Gmail eller något annat. Senare kommer du att anropa den här URL: en i din App Service-app. Mer information om begär ande utlösare finns i [referens för http-begäran/svar](../connectors/connectors-native-reqres.md).

1. Klicka på **nytt steg** längst ned i designern, Skriv **Gmail** i sökrutan åtgärder och Sök och välj **skicka e-post (v2)**.
    
    > [!TIP]
    > Du kan söka efter andra typer av integreringar, till exempel SendGrid, MailChimp, Microsoft 365 och SalesForce. Mer information finns i [Logic Apps-dokumentationen](../logic-apps/index.yml).

1. I dialog rutan **Gmail** väljer du **Logga** in och loggar in på det Gmail-konto som du vill skicka e-postmeddelandet från.

    ![Skärm bild som visar dialog rutan Gmail som du använder för att logga in på det Gmail-konto som du vill skicka e-post från.](./media/tutorial-send-email/gmail-sign-in.png)

1. När du har loggat in klickar du på i rutan **till för att** öppna dialog rutan dynamiskt innehåll automatiskt.

1. Bredvid åtgärden **när en HTTP-begäran tas emot** väljer du **Se fler**.

    ![Skärm bild som visar knappen Se fler bredvid när en H T T P P-begäran tas emot.](./media/tutorial-send-email/expand-dynamic-content.png)

    Du bör nu se de tre egenskaperna från dina exempel-JSON-data som du använde tidigare. I det här steget använder du dessa egenskaper från HTTP-begäran för att skapa ett e-postmeddelande.
1. Eftersom du väljer värdet för fältet **till** väljer du **e-post**. Om du vill kan du växla till dialog rutan dynamiskt innehåll genom att klicka på **Lägg till dynamiskt innehåll**.

    ![Skärm bild som visar alternativet för e-post och alternativet Lägg till dynamiskt innehåll markerat.](./media/tutorial-send-email/hide-dynamic-content.png)

1. I list rutan **Lägg till ny parameter** väljer du **ämne** och **brödtext**.

1. Klicka i text rutan **ämne** och välj **uppgift** på samma sätt. Med markören kvar i rutan **ämne** skriver du *skapat*. 

1. Klicka i **bröd texten** **och välj sedan på på** samma sätt. Flytta markören till vänster om **förfallet** och skriv *det här arbetsobjektet är förfallet*.

    > [!TIP]
    > Om du vill redigera HTML-innehåll direkt i e-postmeddelandets brödtext väljer du **kodvyn** överst i fönstret Logic Apps designer. Se bara till att du behåller koden för dynamiskt innehåll (till exempel `@{triggerBody()?['due']}` )
    >
    > ![Skärm bild som visar kodvyn för att Visa H T M L-innehåll direkt i e-postmeddelandets brödtext.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Lägg sedan till ett asynkront HTTP-svar i HTTP-utlösaren. Mellan HTTP-utlösaren och Gmail-åtgärden klickar du på **+** signeringen och väljer **Lägg till en parallell gren**.

    ![Skärm bild som visar alternativet + signera och Lägg till ett parallellt gren alternativ markerat.](./media/tutorial-send-email/add-http-response.png)

1. I sökrutan söker du efter **svar** och väljer sedan åtgärden **svar** .

    ![Skärm bild som visar den markerade Sök fältet och svars åtgärden.](./media/tutorial-send-email/choose-response-action.png)

    Som standard skickar svars åtgärden en HTTP 200. Det är tillräckligt för den här självstudien. Mer information finns i referens för [http-begäran/svar](../connectors/connectors-native-reqres.md).

1. Välj **Spara** igen överst i Logic Apps designer. 

## <a name="add-http-request-code-to-app"></a>Lägg till kod för HTTP-begäran i appen

Kontrol lera att du har kopierat URL: en för HTTP-begäran från en tidigare version. Eftersom det innehåller känslig information är det bäst att du inte sätter den i koden direkt. Med App Service kan du referera till den som en miljö variabel i stället med hjälp av app-inställningar. 

I [Cloud Shell](https://shell.azure.com)skapar du appens inställning med följande kommando (Ersätt *\<app-name>* , *\<resource-group-name>* och *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

I din kod gör du ett HTTP-standardinlägg till URL: en med valfritt HTTP-klientcertifikat som är tillgängligt för ditt språk ramverk, med följande konfiguration:

- Begär ande texten innehåller samma JSON-format som du angav för din Logic app:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Begäran innehåller rubriken `Content-Type: application/json` . 
- Du kan optimera prestanda genom att skicka begäran asynkront om möjligt.

Klicka på fliken föredraget språk/ramverk nedan om du vill se ett exempel.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

I ASP.NET kan du skicka HTTP-inlägget med klassen [system .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Exempel:

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

Om du testar den här koden i exempel appen för [Självstudier: skapa en ASP.net-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md), kan du använda den för att skicka en bekräftelse av e-post i [åtgärden Skapa](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)när `Todo` objektet har lagts till. Om du vill använda den asynkrona koden ovan konverterar du åtgärden Skapa till asynkron.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

I ASP.NET Core kan du skicka HTTP-inlägget med klassen [system .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Exempel:

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
> Den här koden är avsedd för enkel demonstration. I praktiken ska du inte instansiera ett `HttpClient` objekt för varje begäran. Följ rikt linjerna i [använda IHttpClientFactory för att implementera elastiska HTTP-begäranden](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Om du testar den här koden i exempel appen för [Självstudier: skapa en ASP.net Core-och SQL Database-app i Azure App Service](tutorial-dotnetcore-sqldb-app.md)kan du använda den för att skicka en bekräftelse via e-post i [åtgärden Skapa](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)när `Todo` objektet har lagts till.

### <a name="nodejs"></a>[Node.js](#tab/node)

I Node.js kan du enkelt skicka HTTP-posten med ett NPM-paket som [Axios](https://www.npmjs.com/package/axios). Exempel:

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

Om du testar den här koden i exempel appen för [Självstudier: skapa en Node.js-och MongoDB-app i Azure](tutorial-nodejs-mongodb-app.md), kan du använda den för att skicka en bekräftelse via e-post i [create-funktionen](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)när [artikeln har sparats](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

I PHP kan du enkelt skicka HTTP-posten med [guzzle](http://docs.guzzlephp.org/en/stable/index.html). Exempel:

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

Om du testar den här koden i exempel appen för [Självstudier: bygga en php-och MySQL-app i Azure](tutorial-php-mysql-app.md)kan du använda den för att skicka en bekräftelse via e-post i [Route::p ost-funktionen](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), precis innan Return-instruktionen.

### <a name="python"></a>[Python](#tab/python)

I python kan du enkelt skicka HTTP-posten med [begär Anden](https://pypi.org/project/requests/). Exempel:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
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

Om du testar den här koden i exempel appen för [Självstudier: köra en python-webbapp (django) med postgresql i Azure App Service](tutorial-python-postgresql-app.md)kan du använda den för att skicka en bekräftelse via e-post i [Route::p ost-funktionen](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), precis innan Return-instruktionen.

### <a name="ruby"></a>[Ruby](#tab/ruby)

I ruby kan du enkelt skicka HTTP-posten med JSONClient. Exempel:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Om du testar den här koden i exempel appen för att [skapa en ruby-och postgres-app i Azure App Service på Linux](tutorial-ruby-postgres-app.md), kan du använda den för att skicka en bekräftelse av e-post i åtgärden [skapa](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [när den @task.save lyckas](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>Fler resurser

[Självstudie: Vara värd för en RESTful-API med CORS i Azure App Service](app-service-web-tutorial-rest-api.md)  
[Referens för HTTP-begäran/svar för Logic Apps](../connectors/connectors-native-reqres.md)  
[Snabb start: skapa ditt första arbets flöde med hjälp av Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)