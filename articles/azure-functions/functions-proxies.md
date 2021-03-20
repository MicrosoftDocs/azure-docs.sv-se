---
title: Arbeta med proxyservrar i Azure Functions
description: Översikt över hur du använder Azure Functions-proxyservrar
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96601381"
---
# <a name="work-with-azure-functions-proxies"></a>Arbeta med Azure Functions-proxyservrar

Den här artikeln förklarar hur du konfigurerar och arbetar med Azure Functions-proxyservrar. Med den här funktionen kan du ange slut punkter i din Function-app som implementeras av en annan resurs. Du kan använda dessa proxyservrar för att dela upp ett stort API i flera funktions program (som i en arkitektur för mikrotjänster), samtidigt som du presenterar en enskild API-yta för klienter.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Faktureringen av standard funktioner gäller för proxyautentisering. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Skapa en proxy

I det här avsnittet visas hur du skapar en proxy i functions-portalen.

1. Öppna [Azure Portal]och gå sedan till din Function-app.
2. I den vänstra rutan väljer du **ny proxy**.
3. Ange ett namn för proxyservern.
4. Konfigurera den slut punkt som exponeras för den här Function-appen genom att ange en **vägfil** och **http-metoder**. Dessa parametrar fungerar enligt reglerna för [http-utlösare].
5. Ange **backend-URL:** en till en annan slut punkt. Den här slut punkten kan vara en funktion i en annan Function-app, eller så kan den vara något annat API. Värdet behöver inte vara statiskt och det kan referera till [program inställningar] och [parametrar från den ursprungliga klient förfrågan].
6. Klicka på **Skapa**.

Proxyservern finns nu som en ny slut punkt i din Function-app. Från ett klient perspektiv motsvarar det en HttpTrigger i Azure Functions. Du kan prova din nya proxy genom att kopiera URL: en för proxyservern och testa den med din favorit-HTTP-klient.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Ändra förfrågningar och svar

Med Azure Functions-proxyservrar kan du ändra begär anden till och svar från Server delen. Dessa omvandlingar kan använda variabler som definieras i [använda variabler].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Ändra backend-begäran

Som standard initieras backend-begäran som en kopia av den ursprungliga begäran. Förutom att ställa in backend-URL: en kan du göra ändringar i parametrarna HTTP-metod, sidhuvud och fråga sträng. De ändrade värdena kan referera till [program inställningar] och [parametrar från den ursprungliga klient förfrågan].

Backend-begäranden kan ändras i portalen genom att expandera avsnittet *begär åsidosättning* på sidan information om proxyserver. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Ändra svaret

Som standard initieras klient svaret som en kopia av Server dels svaret. Du kan göra ändringar i svarets status kod, orsaks fras, sidhuvud och brödtext. De ändrade värdena kan referera till [program inställningar], [parametrar från den ursprungliga klientbegäran]och [parametrar från Server delens svar].

Server dels begär Anden kan ändras i portalen genom att avsnittet om *åsidosättning av svar* expanderas på sidan information om proxyserver. 

## <a name="use-variables"></a><a name="using-variables"></a>Använda variabler

Konfigurationen för en proxy behöver inte vara statisk. Du kan ange att den ska använda variabler från den ursprungliga klientbegäran, Server delens svar eller program inställningar.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Referera till lokala funktioner
Du kan använda `localhost` för att referera till en funktion inuti samma Function-app direkt, utan en tur-proxy-begäran.

`"backendUri": "https://localhost/api/httptriggerC#1"` hänvisar till en lokal HTTP-utlöst funktion på vägen `/api/httptriggerC#1`

 
>[!Note]  
>Om din funktion använder auktoriseringsregler *-, admin-eller sys* -nivåer måste du ange koden och clientId, enligt den ursprungliga funktions webb adressen. I det här fallet skulle referensen se ut: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` vi rekommenderar att du lagrar dessa nycklar i [program inställningar] och refererar till dem i dina proxyservrar. På så sätt undviker du att lagra hemligheter i käll koden. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametrar för referens förfrågan

Du kan använda parametrarna för begäran som indata till URL-egenskapen för Server delen eller som en del av att ändra begär Anden och svar. Vissa parametrar kan bindas från den vägfil som anges i Bask proxy-konfigurationen, och andra kan komma från egenskaperna för den inkommande begäran.

#### <a name="route-template-parameters"></a>Parametrar för Route-mall
Parametrar som används i flödes mal len är tillgängliga för referenser till namn. Parameter namnen omges av klammerparenteser ( {} ).

Om en proxyserver till exempel har en vägfil, till exempel `/pets/{petId}` , kan Server delens URL innehålla värdet `{petId}` , som i `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Om väg mal len avslutas i ett jokertecken, till exempel `/api/{*restOfPath}` , är värdet `{restOfPath}` en sträng representation av återstående Sök vägs segment från den inkommande begäran.

#### <a name="additional-request-parameters"></a>Parametrar för ytterligare begäran
Förutom parametrar för väg mal len kan följande värden användas i konfigurations värden:

* **{Request. Method}**: http-metoden som används på den ursprungliga begäran.
* **{Request. headers. \<HeaderName\> }**: en rubrik som kan läsas från den ursprungliga begäran. Ersätt *\<HeaderName\>* med namnet på rubriken som du vill läsa. Om rubriken inte finns med i begäran kommer värdet vara den tomma strängen.
* **{Request. QueryString. \<ParameterName\> }**: en frågesträngparametern som kan läsas från den ursprungliga begäran. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill läsa. Om parametern inte finns med i begäran kommer värdet vara den tomma strängen.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Referera till parametrar för backend-svar

Svars parametrar kan användas som en del av att ändra svaret till klienten. Följande värden kan användas i konfigurations värden:

* **{Server del. Response. StatusCode}**: den HTTP-statuskod som returneras av backend-svaret.
* **{Server del. Response. statusReason}**: http-orsaks frasen som returneras i Server delens svar.
* **{Server del. Response. headers. \<HeaderName\> }**: en rubrik som kan läsas från Server delens svar. Ersätt *\<HeaderName\>* med namnet på den rubrik som du vill läsa. Om rubriken inte ingår i svaret är värdet den tomma strängen.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referens program inställningar

Du kan också referera till [program inställningar som definierats för Function-appen](./functions-how-to-use-azure-function-app-settings.md) genom att omgivande inställnings namnet med procent tecken (%).

Till exempel har en backend-URL på *https://%ORDER_PROCESSING_HOST%/api/orders* "% ORDER_PROCESSING_HOST%" ersatts med värdet för inställningen ORDER_PROCESSING_HOST.

> [!TIP] 
> Använd program inställningar för Server dels värdar när du har flera distributioner eller test miljöer. På så sätt kan du se till att du alltid pratar med den högra Server delen för den aktuella miljön.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Felsöka proxyservrar

Genom att lägga till flaggan `"debug":true` till en proxy i din `proxies.json` aktive ras fel söknings loggning. Loggarna lagras i `D:\home\LogFiles\Application\Proxies\DetailedTrace` och kan nås via avancerade verktyg (kudu). Alla HTTP-svar kommer också att innehålla en `Proxy-Trace-Location` rubrik med en URL för att komma åt logg filen.

Du kan felsöka en proxyserver från klient sidan genom att lägga till en `Proxy-Trace-Enabled` huvud uppsättning i `true` . Detta kommer också att logga en spårning till fil systemet och returnera spårnings-URL: en som en rubrik i svaret.

### <a name="block-proxy-traces"></a>Blockera proxy-spår

Av säkerhets skäl kanske du inte vill att någon ska kunna anropa tjänsten för att generera en spårning. De kommer inte att kunna komma åt spårnings innehållet utan dina inloggnings uppgifter, men om du genererar spårning förbrukar resurserna och exponeras att du använder funktions-proxy.

Inaktivera spår helt genom att lägga till `"debug":false` dem i en viss proxy i `proxies.json` .

## <a name="advanced-configuration"></a>Avancerad konfiguration

De proxyservrar som du konfigurerar lagras i en *proxies.jspå* en fil som finns i roten i en Function app-katalog. Du kan redigera den här filen manuellt och distribuera den som en del av din app när du använder någon av de [distributions metoder](./functions-continuous-deployment.md) som stöds av functions. 

> [!TIP] 
> Om du inte har konfigurerat någon av distributions metoderna kan du också arbeta med *proxies.js* filen i portalen. Gå till din Function-app, Välj **plattforms funktioner** och välj sedan **App Service Editor**. Genom att göra det kan du Visa hela fil strukturen för din Function-app och sedan göra ändringar.

*Proxies.jspå* definieras av ett objekt i proxyn, som består av namngivna proxyservrar och deras definitioner. Om Redigeraren stöder det kan du också referera till ett JSON- [schema](http://json.schemastore.org/proxies) för kod komplettering. En exempel fil kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Varje proxy har ett eget namn, till exempel *Proxy1* i föregående exempel. Motsvarande definitions objekt för proxy definieras av följande egenskaper:

* **matchCondition**: krävs--ett objekt som definierar de begär Anden som utlöser körningen av den här proxyn. Den innehåller två egenskaper som delas med [http-utlösare]:
    * _metoder_: en matris med de http-metoder som proxyservern svarar på. Om den inte anges svarar proxyservern på alla HTTP-metoder på vägen.
    * _Route_: required--definierar väg mal len, vilket styr vilka URL: er för begäran som proxyservern svarar på. Till skillnad från HTTP-utlösare finns det inget standardvärde.
* ryggs ryggi **: URL**: en till den backend-resurs som begäran ska skickas till via proxy. Det här värdet kan referera till program inställningar och parametrar från den ursprungliga klient förfrågan. Om den här egenskapen inte ingår svarar Azure Functions med en HTTP 200 OK.
* **requestOverrides**: ett objekt som definierar omvandlingar till backend-begäran. Se [definiera ett requestOverrides-objekt].
* **responseOverrides**: ett objekt som definierar omvandlingar till klient svaret. Se [definiera ett responseOverrides-objekt].

> [!NOTE] 
> Egenskapen *Route* i Azure Functions-proxyservrar följer inte egenskapen *routePrefix* för Funktionsapp-värd konfigurationen. Om du vill inkludera ett prefix som `/api` , måste det ingå i egenskapen *Route* .

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Inaktivera enskilda proxyservrar

Du kan inaktivera enskilda proxyservrar genom att lägga till `"disabled": true` dem i proxyn i `proxies.json` filen. Detta gör att alla begär Anden som uppfyller matchCondition kan returnera 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> Program inställningar

Proxy-beteendet kan styras av flera appinställningar. De beskrivs i avsnittet [Functions App Settings Reference](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Reserverade tecken (sträng formatering)

Proxyservrar läser alla strängar från en JSON-fil med hjälp av \ som en Escape-symbol. Proxyservrar tolkar också klammerparenteser. Se en fullständig uppsättning av exempel nedan.

|Tecken|Escaped tecken|Exempel|
|-|-|-|
|eller|{{eller}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definiera ett requestOverrides-objekt

RequestOverrides-objektet definierar ändringar som gjorts i begäran när backend-resursen anropas. Objektet definieras av följande egenskaper:

* **Server del. Request.-metod**: http-metoden som används för att anropa Server delen.
* **Server del. Request. QueryString \<ParameterName\> .**: en frågesträngparametern som kan anges för anropet till Server delen. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill ange. Observera att om en tom sträng anges, inkluderas parametern fortfarande i backend-begäran.
* **backend. Request. headers \<HeaderName\> .**: en rubrik som kan anges för anropet till Server delen. Ersätt *\<HeaderName\>* med namnet på den rubrik som du vill ange. Observera att om en tom sträng anges, inkluderas parametern fortfarande i backend-begäran.

Värden kan referera till program inställningar och parametrar från den ursprungliga klient förfrågan.

En exempel konfiguration kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definiera ett responseOverrides-objekt

RequestOverrides-objektet definierar ändringar som görs i svaret som skickas tillbaka till klienten. Objektet definieras av följande egenskaper:

* **Response. StatusCode**: den HTTP-statuskod som ska returneras till klienten.
* **Response. statusReason**: den http-orsaks fras som ska returneras till klienten.
* **Response. Body**: sträng representationen för bröd texten som ska returneras till klienten.
* **Response. headers \<HeaderName\> .**: en rubrik som kan anges för svaret på klienten. Ersätt *\<HeaderName\>* med namnet på den rubrik som du vill ange. Om du anger en tom sträng tas inte rubriken med i svaret.

Värden kan referera till program inställningar, parametrar från den ursprungliga klientbegäran och parametrar från Server delens svar.

En exempel konfiguration kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> I det här exemplet anges svars texten direkt, så ingen `backendUri` egenskap krävs. Exemplet visar hur du kan använda Azure Functions-proxyservrar för att modellera API: er.

[Azure-portalen]: https://portal.azure.com
[HTTP-utlösare]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definiera ett requestOverrides-objekt]: #requestOverrides
[Definiera ett responseOverrides-objekt]: #responseOverrides
[program inställningar]: #use-appsettings
[Använda variabler]: #using-variables
[parametrar från den ursprungliga klient förfrågan]: #request-parameters
[parametrar från Server delens svar]: #response-parameters
