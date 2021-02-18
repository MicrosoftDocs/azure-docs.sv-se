---
title: Java Script för betrodda tjänster
description: Det här är JavaScript-versionen av att skapa en betrodd tjänst för kommunikations tjänster.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3bf0831084b9dd6f16d8208def87033fc0be2900
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645321"
---
## <a name="download-code"></a>Hämta kod

Hitta den färdiga koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/Trusted%20Authentication%20Service)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), aktiva LTS och underhåll LTS-versioner (10.14.1 rekommenderas). Använd `node --version` kommandot för att kontrol lera din version. 
- [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. 
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Översikt

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagram över arkitektur för betrodda tjänster":::

I den här självstudien kommer vi att skapa en Azure-funktion som fungerar som en betrodd token för etablerings tjänsten. Du kan använda den här självstudien för att starta din egen token Provisioning-tjänst.

Den här tjänsten ansvarar för att autentisera användare i Azure Communication Services. Användare av kommunikations tjänst program kräver en för `Access Token` att kunna delta i chatt-trådar och VoIP-samtal. Azure-funktionen fungerar som en betrodd mellanman mellan användaren och kommunikations tjänsterna. På så sätt kan du etablera åtkomsttoken utan att exponera resurs anslutnings strängen för dina användare.

Mer information finns i dokumentationen för [klient-serverns arkitektur](../../concepts/client-and-server-architecture.md) och [autentisering och auktorisering](../../concepts/authentication.md) .

## <a name="setting-up"></a>Konfigurera

### <a name="azure-functions-set-up"></a>Azure Functions konfigureras

Låt oss börja med att konfigurera den grundläggande strukturen för vår Azure-funktion. Stegvisa instruktioner på fliken konfiguration finns här: [skapa en funktion med Visual Studio Code](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

Azure-funktionen kräver följande konfiguration:

- Språk: Java Script
- Mall: HTTP-utlösare
- Autentiseringsnivå: Anonym (detta kan växlas senare om du föredrar en annan auktoriserings modell)
- Funktions namn: användardefinierad

När du har påträffat [Azure Functions instruktionerna](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) med ovanstående konfiguration, bör du ha ett projekt i Visual Studio Code för Azure-funktionen med en `index.js` fil som innehåller själva funktionen. Koden i den här filen bör vara följande:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Nu ska vi fortsätta att installera Azure Communication Services-biblioteken.

### <a name="install-communication-services-libraries"></a>Installera kommunikations tjänst bibliotek

Vi använder `Identity` biblioteket för att skapa `User Access Tokens` .

Använd `npm install` kommandot för att installera Azure Communication Services administrations klient bibliotek för Java Script.

```console

npm install @azure/communication-identity --save

```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

`index.js`Importera gränssnittet för i filen överst i filen`CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>Generering av åtkomsttoken

För att Azure-funktionen ska kunna generera `User Access Tokens` , behöver vi först använda anslutnings strängen för vår kommunikations tjänst resurs.

Besök den här snabb starten för [resurs etablering](../../quickstarts/create-communication-resource.md) om du vill ha mer information om hur du hämtar anslutnings strängen.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Sedan ändrar vi vår ursprungliga funktion till att generera `User Access Tokens` . 

`User Access Tokens` genereras genom att skapa en användare från- `createUser` metoden. När användaren har skapats kan vi använda- `issueToken` metoden för att generera en token för den användare som Azure-funktionen returnerar.

I det här exemplet ska vi konfigurera token-scopet till `voip` . Andra omfattningar kan vara nödvändiga för ditt program. Läs mer om [omfattningar](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

För befintliga kommunikations tjänster `CommunicationUser` kan du hoppa över skapande steget och bara generera en åtkomsttoken. Mer information finns i snabb starten för [create User Access-token](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Testa Azure-funktionen

Kör Azure-funktionen lokalt med `F5` . Detta initierar Azure-funktionen lokalt och gör den tillgänglig via: `http://localhost:7071/api/FUNCTION_NAME` . Ta en titt på ytterligare dokumentation om att [köra lokalt](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)

Öppna URL: en i webbläsaren så bör du se en svars text med kommunikations användarens ID, token och förfallo datum för token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Skärm bild som visar ett svars exempel för den skapade Azure-funktionen.":::

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Om du vill distribuera din Azure-funktion kan du följa [steg-för-steg-instruktioner](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)

Sammanfattnings vis måste du:
1. Logga in på Azure från Visual Studio
2. Publicera ditt projekt på ditt Azure-konto. Här måste du välja en befintlig prenumeration.
3. Skapa en ny Azure Function-resurs med hjälp av Visual Studio-guiden eller Använd en befintlig resurs. För en ny resurs måste du konfigurera den till önskad region, körning och unik identifierare.
4. Vänta tills distributionen har slutförts
5. Kör funktionen 🎉

## <a name="run-azure-function"></a>Kör Azure Function

Kör Azure-funktionen med URL: en `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Du kan hitta URL: en genom att högerklicka på funktionen i Visual Studio Code och kopiera funktions webb adressen.

Mer information om hur [du kör Azure Function](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
