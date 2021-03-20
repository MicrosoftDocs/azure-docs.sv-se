---
title: Konfigurera lokal utveckling för Azures statiska Web Apps
description: Lär dig hur du ställer in din lokala utvecklings miljö för Azures statiska Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326175"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Konfigurera lokal utveckling för för hands versionen av Azure statisk Web Apps

En statisk Web Apps instans av Azure består av två olika typer av program. Den första är en webbapp för statiskt innehåll. Webbappar skapas ofta med frontend-ramverk och-bibliotek eller med statiska webbplats generatorer. Den andra aspekten är API: et, som är en Azure Functions app som ger en omfattande utvecklings miljö på Server sidan.

Vid körning i molnet mappar Azure static Web Apps sömlöst begär anden till `api` vägen från webbappen till Azure Functions-appen utan att kräva CORS-konfiguration. Lokalt måste du konfigurera ditt program så att det här beteendet efterliknas.

Den här artikeln visar rekommenderade metod tips för lokal utveckling, inklusive följande begrepp:

- Konfigurera webb programmet för statiskt innehåll
- Konfigurera Azure Functions-appen för programmets API
- Felsöka och köra programmet
- Metod tips för appens fil-och mappstruktur

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Functions tillägg](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code
- [Live Server-tillägg](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) för Visual Studio Code
  - Krävs endast om du inte använder ett front-end JavaScript-ramverk eller en statisk webbplats Generators CLI

## <a name="run-projects-locally"></a>Kör projekt lokalt

Att köra en statisk Azure-webbapp lokalt omfattar tre processer, beroende på om projektet innehåller ett API.

- Köra en lokal webb server
- Köra API: et
- Ansluta webb projektet till API: et

Beroende på hur en webbplats har skapats kan det hända att en lokal webb server kanske inte behöver köra programmet i webbläsaren. När du använder frontend-ramverk och statiska webbplats generatorer, är den här funktionen inbyggd i deras respektive Plat (kommando rads gränssnitt). Följande länkar pekar på CLI-referensen för ett urval av ramverk, bibliotek och generatorer.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript-ramverk och bibliotek

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [Reakta CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Statiska webbplats generatorer

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Om du använder ett CLI-verktyg för att betjäna platsen kan du gå vidare till avsnittet om [API](#run-api-locally) .

### <a name="running-a-local-web-server-with-live-server"></a>Köra en lokal webb server med Live Server

Live Server-tillägget för Visual Studio Code tillhandahåller en lokal utvecklings webb server som hanterar statiskt innehåll.

#### <a name="create-a-repository"></a>Klona en lagringsplats

1. Kontrol lera att du är inloggad på GitHub och navigera till [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) och skapa ett nytt GitHub-projekt med namnet **vanilj-API** med hjälp av den här mallen.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub nytt lagrings platsen-fönster":::

1. Öppna Visual Studio Code.

1. Tryck på **F1** för att öppna kommandopaletten.

1. Skriv **klona** i sökrutan och välj **git: klon**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="git-klonings alternativ i Visual Studio Code":::

1. Ange följande värde för **lagrings platsens URL**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Välj en mapplats för det nya projektet.

1. När du uppmanas att öppna den klonade lagringsplatsen väljer du **Öppna**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Öppna i nytt fönster":::

Visual Studio Code öppnar det klonade projektet i redigeraren.

### <a name="run-the-website-locally-with-live-server"></a>Kör webbplatsen lokalt med Live Server

1. Tryck på **F1** för att öppna kommandopaletten.

1. Skriv **Live Server** i sökrutan och välj **Live Server: öppna med Live Server**

    En webbläsare-flik öppnas för att visa programmet.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Enkel statisk plats som körs i webbläsaren":::

    Det här programmet gör en HTTP-begäran till `api/message` slut punkten. Just nu fungerar inte begäran eftersom API-delen av det här programmet måste startas.

### <a name="run-api-locally"></a>Kör API lokalt

Azures statiska Web Apps-API: er drivs av Azure Functions. Mer information om hur du lägger till ett API till ett statiskt Azure-Web Apps-projekt finns i [lägga till ett API till Azure static Web Apps med Azure Functions](add-api.md) .

Som en del av processen för att skapa API skapas en start konfiguration för Visual Studio Code. Den här konfigurationen finns i mappen _. VSCode_ . Den här mappen innehåller alla inställningar som krävs för att skapa och köra API: et lokalt.

1. Tryck på **F5** i Visual Studio Code för att starta API: et.

1. En ny terminal-instans öppnas som visar utdata från API-build-processen.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API som körs i Visual Studio Code Terminal":::

   Statusfältet i Visual Studio Code är nu orange. Den här färgen indikerar att API: et körs och fel söknings programmet är anslutet.

1. Tryck sedan på **CTRL/cmd** och klicka på URL: en i terminalen för att öppna ett webbläsarfönster som anropar API: et.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Webbläsarens visnings resultat för API-anrop":::

### <a name="debugging-the-api"></a>Felsöka API: et

1. Öppna filen _API/GetMessage/index.js_ i Visual Studio Code.

1. Ange en Bryt punkt genom att klicka i den vänstra marginalen på rad 2. En röd prick visas som anger att Bryt punkten har angetts.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Bryt punkt i Visual Studio Code":::

1. I webbläsaren uppdaterar du sidan som körs på <http://127.0.0.1:7071/api/message> .

1. Bryt punkten har nåtts i Visual Studio Code och program körningen har pausats.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Bryt punkts träff i Visual Studio Code":::

   En fullständig [fel söknings funktion finns i Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging) för ditt API.

1. Fortsätt att köra genom att klicka på knappen **Fortsätt** i fel söknings fältet.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Knappen Fortsätt i Visual Studio Code":::

### <a name="calling-the-api-from-the-application"></a>Anropar API: et från programmet

När Azures statiska Web Apps har distribuerats mappar automatiskt de här förfrågningarna till slut punkterna i _API_ -mappen. Den här mappningen säkerställer att förfrågningar från programmet till API: et ser ut som i följande exempel.

```javascript
let response = await fetch("/api/message");
```

Beroende på om ditt program har skapats med en JavaScript Framework CLI, finns det två sätt att konfigurera sökvägen till `api` vägen när du kör programmet lokalt.

- Miljö konfigurations filer (rekommenderas för JavaScript-ramverk och bibliotek)
- Lokal Proxy

### <a name="environment-configuration-files"></a>Miljö konfigurations filer

Om du skapar din app med klient dels ramverk som har en CLI bör du använda miljö konfigurationsfiler. Varje ramverk eller bibliotek har ett annat sätt att hantera de här miljö konfigurations filerna. Det är vanligt att ha en konfigurations fil för utveckling som används när ditt program körs lokalt och en för produktion som används när ditt program körs i produktion. CLI för JavaScript-ramverket eller den statiska webbplats generatorn som du använder kommer automatiskt att kunna använda utvecklings filen lokalt och produktions filen när din app har skapats av Azures statiska Web Apps.

I utvecklings konfigurations filen kan du ange sökvägen till API: et, som pekar på den lokala platsen `http:127.0.0.1:7071` där API: et för platsen körs lokalt.

```
API=http:127.0.0.1:7071/api
```

I produktions konfigurations filen anger du sökvägen till API: t som `api` . På så sätt anropar programmet API: t via "yoursite.com/api" när det körs i produktion.

```
API=api
```

Dessa konfigurations värden kan refereras som Node-miljövariabler i webbappens Java Script.

```js
let response = await fetch(`${process.env.API}/message`);
```

När CLI används för att köra din webbplats i utvecklings läge eller för att skapa en plats för produktion, `process.env.API` ersätts värdet med värdet från lämplig konfigurations fil.

Mer information om hur du konfigurerar miljöfiler för front-end JavaScript-ramverk och bibliotek finns i följande artiklar:

- [Miljö variabler för vinkel](https://angular.io/guide/build#configuring-application-environments)
- [Reagera – lägga till anpassade miljövariabler](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue-lägen och miljövariabler](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Starta om Live Server

1. Tryck på **F1** för att öppna kommando-paletten i Visual Studio Code.

1. Skriv **Live** Server och välj **Live Server: stoppa Live Server**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Stoppa Live Server-kommandot i Visual Studio Command-paletten":::

1. Tryck på **F1** för att öppna kommandopaletten.

1. Skriv **Live** Server och välj **Live Server: öppna med Live Server**.

1. Uppdatera programmet som körs vid `http://locahost:3000` . Webbläsaren visar nu meddelandet som returnerades från API: et.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Hej från API: t som visas i webbläsaren":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera appinställningar](application-settings.md)
