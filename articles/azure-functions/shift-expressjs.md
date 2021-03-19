---
title: Växlar från Express.js till Azure Functions
description: Lär dig att Express.js slut punkter till Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87810232"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Växlar från Express.js till Azure Functions

Express.js är ett av de mest populära Node.js ramverken för webbutvecklare och är ett utmärkt alternativ för att skapa appar som hanterar API-slutpunkter.

När du migrerar kod till en server lös arkitektur, påverkar omfabriker Express.js slut punkter följande områden:

- **Mellanprogram**: Express.js innehåller en robust samling av mellanprogram. Många mellanliggande moduler krävs inte längre med hänsyn till Azure Functions och [Azure API Management](../api-management/api-management-key-concepts.md) -funktioner. Se till att du kan replikera eller ersätta all logik som hanteras av grundläggande mellanprogram innan du migrerar slut punkter.

- **Olika API: er** som används för att bearbeta både förfrågningar och svar skiljer sig mellan Azure Functions och Express.js. I följande exempel beskrivs de nödvändiga ändringarna.

- **Standard väg**: Azure Functions slut punkter visas som standard under `api` vägen. Routningsregler kan konfigureras via [ `routePrefix` i _host.jsi_ filen](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Konfiguration och konventioner**: en Functions-app använder _function.jspå_ filen för att definiera HTTP-verb, definiera säkerhets principer och kan konfigurera funktionens [indata och utdata](./functions-triggers-bindings.md). Som standard definierar mappnamnet som innehåller funktions namnen namnet på slut punkten, men du kan ändra namnet via `route` egenskapen i [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) filen.

> [!TIP]
> Läs mer i de interaktiva självstudierna [Node.js-och Express-API: er till Server lös API: er med Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Exempel

### <a name="expressjs"></a>Express.js

I följande exempel visas en typisk Express.js- `GET` slutpunkt.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

När en `GET` begäran skickas till `/hello` `HTTP 200` returneras ett svar som innehåller `Success` . Om slut punkten påträffar ett fel, är svaret en `HTTP 500` med fel informationen.

### <a name="azure-functions"></a>Azure Functions

Azure Functions ordnar konfigurations-och kod filer i en enda mapp för varje funktion. Som standard används funktions namnet i mappens namn.

Till exempel har en funktion `hello` som heter har en mapp med följande filer.

``` files
| - hello
|  - function.json
|  - index.js
```

I följande exempel implementeras samma resultat som Express.js slut punkten ovan, men med Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

När du flyttar till Functions görs följande ändringar:

- **Modul:** Funktions koden implementeras som en JavaScript-modul.

- **Kontext-och svars objekt**: med [`context`](./functions-reference-node.md#context-object) kan du kommunicera med funktionens körnings tid. Från kontexten kan du läsa begär ande data och ange funktionens svar. Med synkron kod måste du anropa `context.done()` för att slutföra körningen, medan `asyc` funktioner löser begäran implicit.

- **Namngivnings konvention**: det mappnamn som används för att innehålla Azure Functions-filerna används som standard slut punkts namn (detta kan åsidosättas i [function.jspå](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Konfiguration**: du definierar http-verben i [function.jspå](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) filen, till exempel `POST` eller `PUT` .

Följande _function.jsi_ filen innehåller konfigurations information för funktionen.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

`get` `methods` Funktionen är tillgänglig för HTTP-begäranden genom att definiera i matrisen `GET` . Om du vill att ditt API ska acceptera support `POST` begär Anden kan du även lägga till dem `post` i matrisen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer med den interaktiva självstudien [Node.js och Express-API: er till Server lös API: er med Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/)