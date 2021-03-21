---
title: JavaScript-referens för utvecklare för Azure Functions
description: Lär dig hur du utvecklar funktioner med hjälp av Java Script.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 03/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: 971fb2a3239614a708e14c109e567081f1ec9ff6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614912"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript-guide för utvecklare

Den här guiden innehåller detaljerad information som hjälper dig att utveckla Azure Functions med hjälp av Java Script.

Som Express.js, Node.js eller JavaScript-utvecklare, om du är nybörjare på Azure Functions, bör du först läsa någon av följande artiklar:

| Komma igång | Begrepp| Interaktiv utbildning |
| -- | -- | -- | 
| <ul><li>[Node.js funktionen med Visual Studio Code](./create-first-function-vs-code-node.md)</li><li>[Node.js funktion med Terminal/kommando-prompt](./create-first-function-cli-node.md)</li><li>[Node.js funktionen med hjälp av Azure Portal](functions-create-function-app-portal.md)</li></ul> | <ul><li>[Utvecklarguide](functions-reference.md)</li><li>[Värdalternativ](functions-scale.md)</li><li>[TypeScript-funktioner](#typescript)</li><li>[Prestanda &nbsp; överväganden](functions-best-practices.md)</li></ul> | <ul><li>[Skapa serverlösa program](/learn/paths/create-serverless-applications/)</li><li>[Node.js-och Express-API: er till Server lös API: er](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Grundläggande om JavaScript-funktioner

En JavaScript-funktion (Node.js) är en exporterad `function` som körs när den utlöses ([utlösare konfigureras i function.jspå](functions-triggers-bindings.md)). Det första argumentet som skickas till varje funktion är ett `context` -objekt som används för att ta emot och skicka bindnings data, loggning och kommunikation med körnings miljön.

## <a name="folder-structure"></a>Mappstruktur

Den obligatoriska mappstrukturen för ett JavaScript-projekt ser ut ungefär så här. Standardvärdet kan ändras. Mer information finns i avsnittet [scriptFile](#using-scriptfile) nedan.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

I projekt roten finns det en delad [host.jspå](functions-host-json.md) en fil som kan användas för att konfigurera Function-appen. Varje funktion har en mapp med en egen kod fil (. js) och en bindnings konfigurations fil (function.jspå). Namnet på `function.json` den överordnade katalogen är alltid namnet på din funktion.

De bindnings tillägg som krävs i [version 2. x](functions-versions.md) av Functions-körningen definieras i `extensions.csproj` filen med de faktiska biblioteksfilerna i `bin` mappen. När du utvecklar lokalt måste du [Registrera bindnings tillägg](./functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

## <a name="exporting-a-function"></a>Exportera en funktion

JavaScript-funktioner måste exporteras via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (eller [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). Den exporterade funktionen bör vara en JavaScript-funktion som körs när den utlöses.

Som standard söker Functions-körningen efter din funktion i `index.js` , där `index.js` delar samma överordnade katalog som dess motsvarande `function.json` . I standard fallet bör den exporterade funktionen vara den enda exporten från filen eller exporten med namnet `run` eller `index` . Om du vill konfigurera fil platsen och export namnet för din funktion läser du om hur [du konfigurerar din funktions start punkt](functions-reference-node.md#configure-function-entry-point) nedan.

Den exporterade funktionen skickas ett antal argument vid körning. Det första argumentet som det tar är alltid ett `context` objekt. Om funktionen är synkron (returnerar inte ett löfte) måste du skicka `context` objektet, vilket `context.done` krävs för korrekt användning.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportera en async-funktion
När du använder [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen eller det vanliga JavaScript- [löfteet](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) i version 2. x av Functions-körningen behöver du inte uttryckligen anropa [`context.done`](#contextdone-method) återanropet för att signalera att funktionen har slutförts. Funktionen slutförs när den exporterade asynkrona funktionen/Promise-funktionen slutförs. För funktioner som är riktade till version 1. x-körningen, måste du fortfarande anropa [`context.done`](#contextdone-method) när koden har körts.

Följande exempel är en enkel funktion som loggar att den utlöstes och slutfört körningen direkt.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

När du exporterar en async-funktion kan du också konfigurera en utgående bindning för att ta `return` värdet. Detta rekommenderas om du bara har en utgående bindning.

Om du vill tilldela en utmatning med hjälp av `return` ändrar du `name` egenskapen till `$return` i `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

I det här fallet bör din funktion se ut som i följande exempel:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindningar 
I Java Script konfigureras och definieras [bindningarna](functions-triggers-bindings.md) i en funktions function.jspå. Funktioner interagerar med bindningar på flera sätt.

### <a name="inputs"></a>Indata
Indatamängden är indelade i två kategorier i Azure Functions: en är utlösaren och den andra är den extra ingången. Utlösare och andra indatamasker (bindningar av `direction === "in"` ) kan läsas av en funktion på tre sätt:
 - **_[Rekommenderas]_ Som parametrar som skickas till din funktion.** De skickas till funktionen i samma ordning som de definieras i *function.jspå*. Den `name` egenskap som definierats i *function.jspå* behöver inte matcha namnet på din parameter, men den borde.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Som medlemmar i [`context.bindings`](#contextbindings-property) objektet.** Varje medlem namnges av den `name` egenskap som definierats i *function.jspå*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Som indata med JavaScript- [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) objektet.** Detta är i stort sett detsamma som att skicka indata som parametrar, men gör att du kan hantera indata dynamiskt.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Utdata
Utdata (bindningar av `direction === "out"` ) kan skrivas till av en funktion på flera sätt. I samtliga fall `name` motsvarar egenskapen för bindningen som definieras i *function.jspå* namnet på den objekt medlem som skrivits till i din funktion. 

Du kan tilldela data till utgående bindningar på något av följande sätt (kombinera inte dessa metoder):

- **_[Rekommenderas för flera utdata]_ Returnera ett objekt.** Om du använder en asynkron/löftes funktion för att returnera, kan du returnera ett objekt med tilldelad utmatnings information. I exemplet nedan heter utgående bindningarna "httpResponse" och "queueOutput" i *function.jspå*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Om du använder en synkron funktion kan du returnera objektet med hjälp av [`context.done`](#contextdone-method) (se exemplet).
- **_[Rekommenderas för enstaka utdata]_ Returnerar ett värde direkt och använder $return bindnings namn.** Detta fungerar endast för asynkrona/löftes retur funktioner. Se exempel i [Exportera en async-funktion](#exporting-an-async-function). 
- **Tilldela värden till `context.bindings`** Du kan tilldela värden direkt till Context. bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Data typen bindings

Använd egenskapen i bindnings definitionen för att definiera data typen för en indata-bindning `dataType` . Om du till exempel vill läsa innehållet i en HTTP-begäran i binärt format använder du typen `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Alternativen för `dataType` är: `binary` , `stream` och `string` .

## <a name="context-object"></a>kontext objekt

Körningen använder ett- `context` objekt för att skicka data till och från din funktion och körnings miljön. Används för att läsa och ange data från bindningar och för att skriva till loggar, `context` är objektet alltid den första parametern som skickas till en funktion.

För funktioner med synkron kod inkluderar kontext objektet det `done` återanrop som du anropar när funktionen utförs. Explicit anrop `done` är onödig vid skrivning av asynkron kod. `done` återanropet anropas implicit.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

Kontexten som skickas till funktionen exponerar en `executionContext` egenskap, som är ett objekt med följande egenskaper:

| Egenskapsnamn  | Typ  | Beskrivning |
|---------|---------|---------|
| `invocationId` | Sträng | Innehåller en unik identifierare för det specifika funktions anropet. |
| `functionName` | Sträng | Anger namnet på den aktiva funktionen |
| `functionDirectory` | Sträng | Innehåller app-katalogen functions. |

I följande exempel visas hur du returnerar `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Egenskapen context. bindings

```js
context.bindings
```

Returnerar ett namngivet objekt som används för att läsa eller tilldela data bindnings data. Du kan få åtkomst till indata och utlösnings bindnings data genom att läsa egenskaper på `context.bindings` . Data för utgående bindning kan tilldelas genom att lägga till data i `context.bindings`

Följande bindnings definitioner i din function.jspå kan till exempel komma åt innehållet i en kö från `context.bindings.myInput` och tilldela utdata till en kö med hjälp av `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Du kan välja att definiera utgående bindnings data med hjälp av `context.done` -metoden i stället för `context.binding` objektet (se nedan).

### <a name="contextbindingdata-property"></a>Context. bindingData-egenskap

```js
context.bindingData
```

Returnerar ett namngivet objekt som innehåller utlösarens metadata och anrops data ( `invocationId` , `sys.methodName` , `sys.utcNow` , `sys.randGuid` ). Ett exempel på att utlösa metadata finns i exemplet på den här [händelse hubben](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>metoden context. Done

```js
context.done([err],[propertyBag])
```

Låter körnings miljön veta att koden har slutförts. När din funktion använder [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen behöver du inte använda `context.done()` . `context.done`Återanropet anropas implicit. Async Functions är tillgängliga i Node 8 eller en senare version, vilket kräver version 2. x av Functions-körningen.

Om funktionen inte är en async-funktion **måste du anropa** `context.done` för att informera körnings miljön om att funktionen har slutförts. Körnings tiden går ut om det saknas.

`context.done`Metoden gör att du kan skicka tillbaka både ett användardefinierat fel till körnings miljön och ett JSON-objekt som innehåller data för utgående bindning. Egenskaper som har överförts för att `context.done` skriva över någon uppsättning i `context.bindings` objektet.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>kontext. log-metod  

```js
context.log(message)
```

Gör att du kan skriva till strömnings funktions loggarna på standard spårnings nivån, med andra tillgängliga loggnings nivåer. Spårnings loggning beskrivs i detalj i nästa avsnitt. 

## <a name="write-trace-output-to-logs"></a>Skriv spårningsutdata till loggar

I functions använder du `context.log` metoder för att skriva spårningsutdata till loggarna och-konsolen. När du anropar `context.log()` , skrivs ditt meddelande till loggarna på standard spårnings nivån, vilket är spårnings nivån för _information_ . Funktioner integreras med Azure Application insikter för att bättre avbilda dina funktions program loggar. Application Insights, en del av Azure Monitor, tillhandahåller funktioner för insamling, visuell åter givning och analys av både programtelemetri och spårnings resultat. Mer information finns i [övervaknings Azure Functions](functions-monitoring.md).

I följande exempel skrivs en logg på spårnings nivån information, inklusive anrops-ID:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Alla `context.log` metoder har stöd för samma parameter format som stöds av metoden Node.js [util. format](https://nodejs.org/api/util.html#util_util_format_format). Tänk på följande kod, som skriver funktions loggar med hjälp av standard spårnings nivån:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Du kan också skriva samma kod i följande format:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Använd inte `console.log` för att skriva spårnings data. Eftersom utdata från samlas in `console.log` på Function-app-nivå, är det inte kopplat till ett särskilt funktions anrop och visas inte i en speciell funktions loggar. Dessutom stöder inte version 1. x av Functions-körningen användning `console.log` för att skriva till-konsolen.

### <a name="trace-levels"></a>Spårnings nivåer

Utöver standard nivån är följande loggnings metoder tillgängliga som gör att du kan skriva funktions loggar på vissa spårnings nivåer.

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **fel (_meddelande_)**   | Skriver en händelse på fel nivå till loggarna.   |
| **Varna (_meddelande_)**    | Skriver en varnings nivå händelse till loggarna. |
| **info (_meddelande_)**    | Skriver till loggning på informations nivå eller lägre.    |
| **utförlig (_meddelande_)** | Skriver till utförlig nivå loggning.           |

I följande exempel skrivs samma logg på varnings spårnings nivån, i stället för informations nivån:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Eftersom _fel_ är den högsta spårnings nivån skrivs denna spårning till utdata vid alla spårnings nivåer så länge loggning har Aktiver ATS.

### <a name="configure-the-trace-level-for-logging"></a>Konfigurera spårnings nivå för loggning

Med Functions kan du definiera tröskel spårnings nivå för att skriva till loggarna eller konsolen. De angivna tröskelvärdena beror på din version av Functions-körningen.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Om du vill ange tröskelvärdet för spår som skrivs till loggarna använder du `logging.logLevel` egenskapen i host.jspå filen. Med det här JSON-objektet kan du definiera ett standard tröskelvärde för alla funktioner i din Function-app, och du kan definiera specifika tröskelvärden för enskilda funktioner. Mer information finns i [så här konfigurerar du övervakning för Azure Functions](configure-monitoring.md).

# <a name="v1x"></a>[v1. x](#tab/v1)

Om du vill ange tröskelvärdet för alla spår som skrivs till loggar och-konsolen använder du `tracing.consoleLevel` egenskapen i host.jsi filen. Den här inställningen gäller för alla funktioner i din Function-app. I följande exempel anges spårnings tröskeln för att aktivera utförlig loggning:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Värdena för **consoleLevel** motsvarar namnen på `context.log` metoderna. Om du vill inaktivera all spårnings loggning till-konsolen anger du **consoleLevel** till _av_. Mer information finns i [host.jspå v1. x-referens](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Logga anpassad telemetri

Som standard skriver funktionen utdata som spår till Application Insights. För mer kontroll kan du i stället använda [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) för att skicka anpassade telemetridata till din Application Insights-instans. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides`Parametern ställer in `operation_Id` till funktionens anrops-ID. Med den här inställningen kan du korrelera alla automatiskt genererade och anpassade telemetri för ett angivet funktions anrop.

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-och webhook-utlösare och HTTP-utgående bindningar använder begäran-och svars objekt för att representera HTTP-meddelanden.  

### <a name="request-object"></a>Begär ande objekt

`context.req`(Request)-objektet har följande egenskaper:

| Egenskap      | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _brödtext_        | Ett objekt som innehåller bröd texten i begäran.               |
| _sidhuvud_     | Ett objekt som innehåller begärandehuvuden.                   |
| _metodsignatur_      | HTTP-metoden för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _parametrar_      | Ett objekt som innehåller Dirigerings parametrarna för begäran. |
| _frågeterm_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody_     | Bröd texten i meddelandet som en sträng.                           |


### <a name="response-object"></a>Svarsobjekt

`context.res`Objektet (Response) har följande egenskaper:

| Egenskap  | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _brödtext_    | Ett objekt som innehåller bröd texten i svaret.         |
| _sidhuvud_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formateringen hoppas över för svaret.    |
| _statusfältet_  | HTTP-statuskod för svaret.                     |
| _cookies_ | En matris med HTTP-cookie-objekt som anges i svaret. Ett HTTP-cookie-objekt har en `name` , `value` -och andra cookie-egenskaper, till exempel `maxAge` eller `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Åtkomst till begäran och svar 

När du arbetar med HTTP-utlösare kan du komma åt HTTP-begäran och svars objekt på flera olika sätt:

+ **Från `req` och `res` Egenskaper för `context` objektet.** På så sätt kan du använda det konventionella mönstret för att komma åt HTTP-data från objektet Context, i stället för att behöva använda det fullständiga `context.bindings.name` mönstret. I följande exempel visas hur du kommer åt- `req` och- `res` objekten på `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Från namngivna indata och utgående bindningar.** På så sätt fungerar HTTP-utlösaren och bindningarna på samma sätt som andra bindningar. I följande exempel anges Response-objektet med hjälp av en namngiven `response` bindning: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Endast svar]_ Genom att anropa `context.res.send(body?: any)` .** Ett HTTP-svar skapas med inkommande `body` som svars text. `context.done()` har implicit namn.

+ **_[Endast svar]_ Genom att anropa `context.done()` .** En särskild typ av HTTP-bindning returnerar svaret som skickas till `context.done()` metoden. Följande HTTP-utgående bindning definierar en `$return` utdataparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Skalning och samtidighet

Som standard övervakar Azure Functions automatiskt belastningen på ditt program och skapar ytterligare värd instanser för Node.js efter behov. Funktionerna använder inbyggda (inte användar konfigurerbara) tröskelvärden för olika utlösare för att bestämma när du ska lägga till instanser, till exempel ålder för meddelanden och kös Tor lek för QueueTrigger. Mer information finns i [så här fungerar förbruknings-och Premium planerna](event-driven-scaling.md).

Det här skalnings beteendet räcker för många Node.js-program. För CPU-baserade program kan du förbättra prestanda ytterligare genom att använda flera språk arbets processer.

Som standard har varje Functions Host-instans en enda språk arbets process. Du kan öka antalet arbets processer per värd (upp till 10) med hjälp av inställningen [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) program. Azure Functions försöker sedan jämnt distribuera samtidiga funktions anrop över dessa arbetare. 

FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som fungerar när du skalar ditt program för att möta efter frågan. 

## <a name="node-version"></a>Node-version

I följande tabell visas aktuella Node.js-versioner som stöds för varje huvud version av Functions-körningen, efter operativ system:

| Funktions version | Node-version (Windows) | Node-version (Linux) |
|---|---| --- |
| 3. x (rekommenderas) | `~14` rekommenderas<br/>`~12`<br/>`~10` | `node|14` rekommenderas<br/>`node|12`<br/>`node|10` |
| 2x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 1.x | 6.11.2 (låst av körningen) | saknas |

Du kan se den aktuella versionen som körningen använder genom att logga `process.version` från vilken funktion som helst.

### <a name="setting-the-node-version"></a>Ställer in Node-versionen

För Windows Function-appar ska du rikta in dig på versionen i Azure genom att ange `WEBSITE_NODE_DEFAULT_VERSION` [appens inställning](functions-how-to-use-azure-function-app-settings.md#settings) till en LTS-version som stöds, till exempel `~14` .

För Linux Function-appar kör du följande Azure CLI-kommando för att uppdatera Node-versionen.

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Beroendehantering
För att kunna använda grupp bibliotek i JavaScript-koden, som visas i exemplet nedan, måste du se till att alla beroenden är installerade på Funktionsapp i Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Du bör definiera en `package.json` fil i roten för din Funktionsapp. Genom att definiera filen kan alla funktioner i appen dela samma cachelagrade paket, vilket ger bästa möjliga prestanda. Om en versions konflikt uppstår kan du lösa det genom att lägga till en `package.json` fil i mappen för en speciell funktion.  

När du distribuerar Function-appar från käll kontroll `package.json` utlöser en fil som finns i din lagrings platsen `npm install` i mappen under distributionen. Men när du distribuerar via portalen eller CLI måste du manuellt installera paketen.

Det finns två sätt att installera paket på Funktionsapp: 

### <a name="deploying-with-dependencies"></a>Distribuera med beroenden
1. Installera alla nödvändiga paket lokalt genom att köra `npm install` .

2. Distribuera koden och se till att `node_modules` mappen ingår i distributionen. 


### <a name="using-kudu"></a>Använda kudu
1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **Felsök konsol**  >  **cmd**.

3. Gå till `D:\home\site\wwwroot` och dra sedan package.jspå filen till mappen **wwwroot** på sidans övre halva.  
    Du kan också ladda upp filer till din Function-app på andra sätt. Mer information finns i [så här uppdaterar du Function Apps-filer](functions-reference.md#fileupdate). 

4. När package.jsfilen har överförts kör du `npm install` kommandot i **kudu-konsolen för fjärrkörning**.  
    Den här åtgärden hämtar de paket som anges i package.jspå filen och startar om Function-appen.

## <a name="environment-variables"></a>Miljövariabler

Lägg till dina egna miljövariabler i en Function-app i både lokala och molnbaserade miljöer, till exempel drift hemligheter (anslutnings strängar, nycklar och slut punkter) eller miljö inställningar (till exempel profilering av variabler). Få åtkomst till de här inställningarna med hjälp av `process.env` i funktions koden.

### <a name="in-local-development-environment"></a>I lokal utvecklings miljö

När du kör lokalt, innehåller Functions-projektet en [ `local.settings.json` fil](./functions-run-local.md)där du lagrar miljövariablerna i `Values` objektet. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>I Azures moln miljö

När du kör i Azure kan du med hjälp av Function-appen ange [program inställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, och exponera dessa inställningar som miljövariabler under körningen. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Åtkomst till miljövariabler i kod

Få åtkomst till program inställningar som miljövariabler med `process.env` , som visas här i den andra och tredje anropet till den `context.log()` plats där vi loggar `AzureWebJobsStorage` `WEBSITE_SITE_NAME` variablerna och.

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>ECMAScript-moduler (för hands version)

> [!NOTE]
> Eftersom ECMAScript-moduler är märkta med *experiment* i Node.js 14 är de tillgängliga som en förhands gransknings funktion i Node.js 14 Azure Functions. Tills Node.js 14-stöd för ECMAScript-moduler blir *stabilt*, förväntar vi dig eventuella ändringar i dess API eller beteende.

[ECMAScript-moduler](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules) (ES-moduler) är det nya officiella standardmoduls systemet för Node.js. I så fall använder kod exemplen i den här artikeln CommonJS-syntaxen. När du kör Azure Functions i Node.js 14, kan du välja att skriva dina funktioner med hjälp av ES modules syntax.

Använd ES-moduler i en funktion genom att ändra dess fil namn till att använda ett `.mjs` tillägg. Följande exempel på *index. MJS* -fil är en http-utlöst funktion som använder sig av ES modules syntax för att importera `uuid` biblioteket och returnera ett värde.

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>Konfigurera funktionens start punkt

`function.json`Egenskaperna `scriptFile` och `entryPoint` kan användas för att konfigurera platsen och namnet på den exporterade funktionen. Dessa egenskaper kan vara viktiga när ditt java script är indelat.

### <a name="using-scriptfile"></a>Använda `scriptFile`

Som standard körs en JavaScript-funktion från `index.js` en fil som delar samma överordnade katalog som dess motsvarande `function.json` .

`scriptFile` kan användas för att hämta en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json`För `myNodeFunction` ska inkludera en `scriptFile` egenskap som pekar på filen med den exporterade funktionen som ska köras.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Använda `entryPoint`

I `scriptFile` (eller `index.js` ) måste en funktion exporteras med `module.exports` för att kunna hittas och köras. Som standard är den funktion som körs när den utlöses den enda exporten från filen, exporten med namnet `run` eller exporten med namnet `index` .

Detta kan konfigureras med `entryPoint` i `function.json` , som i följande exempel:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

I functions v2. x, som stöder `this` parametern i användar funktioner, kan funktions koden sedan vara som i följande exempel:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

I det här exemplet är det viktigt att Observera att även om ett objekt exporteras, finns det inga garantier för att bevara tillstånd mellan körningar.

## <a name="local-debugging"></a>Lokal fel sökning

När den startas med `--inspect` parametern lyssnar en Node.js process efter en fel söknings klient på den angivna porten. I Azure Functions 2. x kan du ange argument som ska skickas till den Node.js processen som kör koden genom att lägga till miljövariabeln eller app-inställningen `languageWorkers:node:arguments = <args>` . 

Om du vill felsöka lokalt lägger du till `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` i [local.settings.jspå](./functions-run-local.md#local-settings-file) filen och kopplar en fel sökare till port 5858.

När du felsöker med VS Code `--inspect` läggs parametern automatiskt till med `port` värdet i projektets launch.jsi filen.

Inställningen fungerar inte i version 1. x `languageWorkers:node:arguments` . Du kan välja fel söknings port med [`--nodeDebugPort`](./functions-run-local.md#start) parametern på Azure Functions Core tools.

## <a name="typescript"></a>TypeScript

När du riktar in dig på version 2. x av Functions-körningen kan du använda både [Azure Functions för Visual Studio Code](./create-first-function-cli-typescript.md) och [Azure Functions Core tools](functions-run-local.md) för att skapa Function-appar med hjälp av en mall som stöder typescript Function app projects. Mallen genererar `package.json` och `tsconfig.json` projektfiler som gör det enklare att stapla, köra och publicera JavaScript-funktioner från typescript-kod med dessa verktyg.

En genererad `.funcignore` fil används för att ange vilka filer som ska undantas när ett projekt publiceras i Azure.  

TypeScript-filer (. TS) är destaplade i JavaScript-filer (. js) i `dist` utdatakatalogen. TypeScript-mallar använder- [ `scriptFile` parametern](#using-scriptfile) i `function.json` för att ange platsen för motsvarande. js-fil i `dist` mappen. Platsen för utdata anges av mallen med hjälp av `outDir` parametern i `tsconfig.json` filen. Om du ändrar den här inställningen eller mappens namn kan inte körningen hitta koden som ska köras.

Hur du utvecklar och distribuerar lokalt från ett TypeScript-projekt beror på ditt utvecklingsverktyg.

### <a name="visual-studio-code"></a>Visuell Studio-kod

Med [Azure Functions för kod tillägget för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) kan du utveckla dina funktioner med typescript. Kärn verktygen är ett krav i Azure Functions-tillägget.

Om du vill skapa en TypeScript Function-app i Visual Studio Code väljer `TypeScript` du som språk när du skapar en Function-app.

När du trycker på **F5** för att köra appen lokalt görs transpilation innan värden (func.exe) initieras. 

När du distribuerar din Function-app till Azure med hjälp av knappen **distribuera till Function-appen...** , skapar Azure Functions-tillägget först en produktions klar version av JavaScript-filer från källfilerna för typescript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Det finns flera sätt på vilka ett TypeScript-projekt skiljer sig från ett JavaScript-projekt när du använder kärn verktygen.

#### <a name="create-project"></a>Skapa projekt

Om du vill skapa ett TypeScript Function-projekt med hjälp av kärn verktyg måste du ange språk alternativet TypeScript när du skapar din Function-app. Det kan du göra på något av följande sätt:

- Kör `func init` kommandot, Välj `node` som språk stack och välj sedan `typescript` .

- Kör kommandot `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Kör lokal

Om du vill köra en Function-app-kod lokalt med hjälp av kärn verktyg, använder du följande kommandon i stället för `func host start` : 

```command
npm install
npm start
```

`npm start`Kommandot motsvarar följande kommandon:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicera till Azure

Innan du använder [`func azure functionapp publish`] kommandot för att distribuera till Azure skapar du en produktions klar version av JavaScript-filer från källfilerna för typescript. 

Följande kommandon förbereder och publicerar ditt TypeScript-projekt med hjälp av kärn verktyg: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

I det här kommandot ersätter du `<APP_NAME>` med namnet på din Function-app.

## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner bör du vara medveten om överväganden i följande avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj en vCPU App Service planer

När du skapar en Function-app som använder App Service plan rekommenderar vi att du väljer en plan med en vCPU i stället för en plan med flera virtuella processorer. Idag kör funktionerna JavaScript-funktioner på ett mer effektivt sätt på virtuella datorer med en vCPU och användningen av större virtuella datorer ger inte de förväntade prestanda förbättringarna. Vid behov kan du manuellt skala ut genom att lägga till fler VM-instanser med en vCPU, eller så kan du aktivera autoskalning. Mer information finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Kall start

När du utvecklar Azure Functions i den serverbaserade värd modellen är kall start en verklighet. *Kall start* avser det faktum att när din Function-app startar för första gången efter en tids inaktivitet tar det längre tid att starta. För JavaScript-funktioner med stora beroende träd i synnerhet kan kall start vara betydande. [Kör dina funktioner som en paket fil](run-functions-from-deployment-package.md) när det är möjligt för att påskynda den kall start processen. Många distributions metoder använder modellen kör från paket som standard, men om du har stora kall starter och inte körs på det här sättet kan den här ändringen ge en betydande förbättring.

### <a name="connection-limits"></a>Anslutnings gränser

När du använder en tjänstespecifik klient i ett Azure Functions program ska du inte skapa en ny klient med varje funktions anrop. Skapa i stället en enda statisk klient i det globala omfånget. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Använda `async` och `await`

När du skriver Azure Functions i Java Script ska du skriva kod med hjälp av `async` `await` nyckelorden och. Att skriva kod med hjälp av `async` och `await` i stället för återanrop eller `.then` och `.catch` med löfte bidrar till att undvika två vanliga problem:
 - Utlöser ej fångade undantag som [kraschar Node.js processen](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), vilket kan påverka körningen av andra funktioner.
 - Oväntat beteende, t. ex. saknade loggar från context. log, som orsakas av asynkrona anrop som inte är korrekt förväntat.

I exemplet nedan anropas den asynkrona metoden `fs.readFile` med en fel-första återanrops funktion som den andra parametern. Den här koden orsakar båda de problem som anges ovan. Ett undantag som inte uttryckligen fångas i rätt område kraschade hela processen (utfärdande #1). Anrop `context.done()` utanför omfånget för motringningsfunktionen innebär att funktions anropet kan avslutas innan filen läses (utfärdande #2). I det här exemplet anropar `context.done()` för tidigt resultatet att logg poster som saknas börjar med `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Genom att använda- `async` och- `await` nyckelorden kan du undvika båda dessa fel. Du bör använda funktionen Node.js-verktyget [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) för att aktivera fel-första funktions sätt för callback-format till inväntade funktioner.

I exemplet nedan går det inte att utföra alla ohanterade undantag som genererades under funktions körningen, men det enskilda anropet som utlöste ett undantag kunde inte utföras. `await`Nyckelordet innebär att stegen nedan `readFileAsync` bara körs när `readFile` har slutförts. Med `async` och `await` behöver du inte heller anropa `context.done()` återanropet.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

+ [Metodtips för Azure Functions](functions-best-practices.md)
+ [Azure Functions, info för utvecklare](functions-reference.md)
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

[' FUNC Azure functionapp Publish ']: functions-run-local.md#project-file-deployment
