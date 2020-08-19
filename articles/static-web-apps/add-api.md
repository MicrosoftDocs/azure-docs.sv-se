---
title: Lägg till ett API i Azures statiska Web Apps med Azure Functions
description: Kom igång med Azures statiska Web Apps genom att lägga till ett Server lös API i din statiska webbapp med hjälp av Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-javascript
ms.openlocfilehash: 8d48a8a9f52c3edb4350c16a25deda9894dcdda1
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271166"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Lägg till en API till för hands versionen av Azure statisk Web Apps med Azure Functions

Du kan lägga till Server lös API: er i Azures statiska Web Apps via integration med Azure Functions. Den här artikeln visar hur du lägger till och distribuerar ett API till en statisk Azure-Web Apps webbplats.

## <a name="prerequisites"></a>Krav

- Azure-konto med en aktiv prenumeration.
  - Om du inte har något konto kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions tillägg](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) -tillägg.
- [Node.js](https://nodejs.org/download/) att köra API-appen lokalt

## <a name="create-a-git-repository"></a>Skapa en Git-lagringsplats

Följande steg visar hur du skapar en ny lagrings plats och klonar filerna till din dator.

1. Kontrol lera att du är inloggad på GitHub och navigera till https://github.com/staticwebdev/vanilla-basic/generate för att skapa en ny lagrings plats.
1. I rutan _databas namn_ anger du **My-vanilj-API**.
1. Klicka på **skapa lagrings plats från mall**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Skapa en ny lagrings plats från vanilj – Basic":::

När projektet har skapats kopierar du webb adressen i webbläsaren för den nya lagrings platsen. Du använder den här URL: en i Visual Studio Code för att klona git-lagringsplatsen.

1. Tryck på **F1** för att öppna kommandot i kommando paletten.
1. Klistra in webb adressen i _git: Clone_ -prompten och tryck på **RETUR**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Klona ett GitHub-projekt med Visual Studio Code":::

    Följ anvisningarna för att välja en lagrings plats för att klona projektet.

## <a name="create-the-api"></a>Skapa API: et

Därefter skapar du ett Azure Functions-projekt som programmets API. 

1. I _mitt-vanilj-API-_ projektet skapar du en undermapp med namnet **API**.
1. Tryck på **F1** för att öppna kommando paletten
1. Skriv **Azure Functions: skapa nytt projekt...**
1. Tryck på **Enter**
1. Välj **Bläddra**
1. Välj mappen **API** som katalog för projekt arbets ytan
1. Välj **Välj**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

1. Ange följande information i prompten:

    - _Välj ett språk_: Välj **Java Script**
    - _Välj en mall för projektets första funktion_: Välj **http-utlösare**
    - _Ange ett funktions namn_: ange **GetMessage**
    - _Autentiseringsnivå_: Välj **Anonym**, vilket gör att vem som helst kan anropa funktions slut punkten.
        - Mer information om auktoriseringsregler finns i [Authorization Keys](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code genererar ett Azure Functions-projekt med en HTTP-utlöst funktion.

Din app har nu en projekt struktur som liknar följande exempel.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Sedan kan du ändra `GetMessage` funktionen så att den returnerar ett meddelande till klient delen.

1. Uppdatera `GetMessage` funktionen under _API/GetMessage/index.js_ med följande kod.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Uppdatera `GetMessage` konfigurationen under `api/GetMessage/function.json` med följande inställningar.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Med ovanstående inställningar är API-slutpunkten:

- Utlöses när en HTTP-begäran görs till funktionen
- Tillgänglig för alla begär Anden oavsett autentiserings status
- Exponeras via _/API/Message_ -vägen

## <a name="run-the-api-locally"></a>Kör API lokalt

Visual Studio Code integreras med [Azure Functions Core tools](../azure-functions/functions-run-local.md) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

> [!TIP]
> Se till att du har alla resurser som anges i avsnittet [krav](#prerequisites) som är installerade innan du fortsätter.

1. Kör funktionen genom att trycka på **F5** för att starta Functions-appen.

1. Om Azure Functions Core Tools inte redan är installerat väljer du **Installera** vid prompten.

    Kärn verktygen visar utdata från det program som körs på panelen _Terminal_ . Som en del av utdata kan du se URL-slutpunkten för din HTTP-utlöst funktion som körs lokalt.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

1. Med kärn verktyg som kör, navigerar du till följande URL för att kontrol lera att API: et körs korrekt: <http://localhost:7071/api/message> .

   Svaret i webbläsaren bör se ut ungefär som i följande exempel:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

1. Tryck på **Shift + F5** för att stoppa felsökningssessionen.

### <a name="call-the-api-from-the-application"></a>Anropa API: et från programmet

Vid distribution till Azure dirigeras begär anden till API: et automatiskt till Functions-appen för begär Anden som skickas till `api` vägen. Fungerar lokalt måste du konfigurera program inställningarna till proxy-begäranden till det lokala API: et.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Uppdatera HTML-filer för att få åtkomst till API: et

1. Uppdatera sedan innehållet i _index.html_ -filen med följande kod för att hämta texten från API-funktionen och visa den på skärmen:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Tryck på **F5** för att starta API-projektet.

1. Tryck på **F1** och välj **Live Server: öppna med Live Server**.

    Du bör nu se API-meddelandet på webb sidan.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Skapa en ny Azure Functions med hjälp av Visual Studio Code":::

   > [!NOTE]
   > Du kan använda andra HTTP-servrar eller proxyservrar för att hantera `index.html` filen. Åtkomst till `index.html` från `file:///` fungerar inte.

1. Tryck på **Shift + F5** för att stoppa API-projektet.

### <a name="commit-and-push-your-changes-to-github"></a>Genomför och skicka ändringarna till GitHub

Använd Visual Studio Code och genomför och skicka ändringarna till den fjärranslutna git-lagringsplatsen.

1. Tryck på **F1** för att öppna kommando paletten
1. Typ **git: genomför alla**
1. Lägg till ett bekräftelse meddelande och tryck på **RETUR**
1. Tryck på **F1**
1. Skriv in **git: push** och tryck på **RETUR**

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **Skapa en resurs**
1. Sök efter **statisk webbapp**
1. Klicka på **statisk webbapp (förhands granskning)**
1. Klicka på **Skapa**

Lägg sedan till de app-/regionsspecifika inställningarna.

1. Välj din _Azure-prenumeration_
1. Välj eller skapa en ny _resurs grupp_
1. Namnge appen **My-vanilj-API**.
1. Välj den _region_ som är närmast dig
1. Välj den **kostnads fria** _SKU: n_
1. Klicka på knappen **Logga in med GitHub** och autentisera med GitHub
1. Välj önskad _organisation_
1. Välj **My-vanilj-API** från List rutan _databas_
1. Välj **original** i list rutan _gren_
1. Klicka på **Nästa: Skapa >** för att redigera versionskonfigurationen

Lägg sedan till följande versions information.

1. Ange **/** för _app-platsen_.
1. Ange **API** i rutan _API-plats_ .
1. Rensa standardvärdet från _appens artefakt plats_, lämna rutan tom.
1. Klicka på **Granska + skapa**.
1. Klicka på **Skapa**

    När du klickar på knappen _skapa_ gör Azure två saker. Först skapas de underliggande moln tjänsterna för att stödja appen. Därefter börjar en bakgrunds process att bygga och distribuera programmet.

1. Klicka på knappen **gå till resurs** för att gå till webbappens _översikts_ sida.

    När appen skapas i bakgrunden kan du klicka på banderollen som innehåller en länk för att Visa versions status.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub-arbetsflöde":::

1. När distributionen är klar kan ou navigera till webbappen genom att klicka på _URL_ -länken som visas på _översikts_ sidan.

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Åtkomst till statisk App-URL från Azure Portal":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill behålla det här programmet kan du använda följande steg för att ta bort Azures statiska webbapp och dess relaterade resurser.

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Skriv **resurs grupper** i det övre Sök fältet
1. Klicka på **resurs grupper**
1. Välj **myResourceGroup**
1. På sidan _myResourceGroup_ kontrollerar du att de listade resurserna är de som du vill ta bort.
1. Välj **ta bort**
1. Skriv **myResourceGroup** i text rutan
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera appinställningar](./application-settings.md)
