---
title: 'Snabb start: skapa en Node.js webbapp'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 3b1ff02d6a9b3e8c7b7abaf1b185a9becd2be12b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105841"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure

::: zone pivot="platform-windows"  

Kom igång med Azure App Service genom att skapa en Node.js/Express-app lokalt med Visual Studio Code och sedan distribuera appen till molnet. Eftersom du använder en kostnads fri App Service nivå debiteras du inga kostnader för att slutföra den här snabb starten.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installera Git</a>
- [Node.js och NPM](https://nodejs.org). Kör kommandot `node --version` för att kontrol lera att Node.js är installerat.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) för Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klona och köra ett lokalt Node.js program

1. Öppna en terminal på den lokala datorn och klona exempel lagrings platsen:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigera till den nya mappen app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Starta appen för att testa den lokalt:

    ```bash
    npm start
    ```
    
1. Öppna webbläsaren och gå till `http://localhost:1337` . Webbläsaren ska Visa "Hello World!".

1. Tryck på **CTRL** + **C** i terminalen för att stoppa servern.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här avsnittet ska du distribuera din Node.js-app till Azure med VS Code och Azure App Service tillägget.

1. I terminalen kontrollerar du att du är i mappen *NodeJS-dok-Hello-World* och startar sedan Visual Studio Code med följande kommando:

    ```bash
    code .
    ```

1. I aktivitets fältet VS Code väljer du Azure-logotypen för att visa **Azure App Service** Explorer. Välj **Logga in på Azure...** och följ instruktionerna. (Mer information finns i [fel sökning av Azure-inloggning](#troubleshooting-azure-sign-in) nedan om du stöter på fel.) När du har loggat in ska Explorer Visa namnet på din Azure-prenumeration.

    ![Logga in på Azure](media/quickstart-nodejs/sign-in.png)

1. I **Azure App Service** Explorer med vs Code väljer du ikonen blå UPPIL för att distribuera appen till Azure. (Du kan också anropa samma kommando från **kommando rads verktyget** (**CTRL** + **Shift** + **P**) genom att skriva "distribuera till webbapp" och välja **Azure App Service: distribuera till Web App**).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Skärm bild av Azure App tjänsten i VS Code som visar den blå pilens ikon vald.":::
        
1. Välj mappen *NodeJS-dok-Hello-World* .

1. Välj ett alternativ för att skapa baserat på det operativ system som du vill distribuera till:

    - Linux: Välj **Skapa ny webbapp**
    - Windows: Välj **Skapa ny webbapp... Avancerad**

1. Ange ett globalt unikt namn för din webbapp och tryck på **RETUR**. Namnet måste vara unikt för alla Azure och använder endast alfanumeriska tecken ("A-Z", "a-z" och "0-9") och bindestreck (-).

1. Om du anger Linux-mål väljer du en Node.js version när du uppmanas till det. En **LTS** -version rekommenderas.

1. Om du riktar in dig på Windows följer du ytterligare prompter:
    1. Välj **skapa en ny resurs grupp** och ange sedan ett namn för resurs gruppen, till exempel `AppServiceQS-rg` .
    1. Välj **Windows** för operativ systemet.
    1. Välj **Skapa ny app service plan** och ange sedan ett namn för planen (till exempel `AppServiceQS-plan` ) och välj **F1 kostnads fritt** för pris nivån.
    1. Välj **hoppa över för tillfället** när du tillfrågas om Application Insights.
    1. Välj en region nära dig eller nära resurser som du vill ha åtkomst till.

1. När du har svarat på alla prompter visar VS Code de Azure-resurser som skapas för din app i popup-fönstret för meddelanden.

    När du distribuerar till Linux väljer du **Ja** när du uppmanas att uppdatera konfigurationen så att den körs `npm install` på mål-Linux-servern.

    ![Prompt för att uppdatera konfigurationen på mål servern för Linux](media/quickstart-nodejs/server-build.png)

1. Välj **Ja** när du uppmanas **att alltid distribuera arbets ytan "NodeJS-dok-Hello-World" till (App Name) "**. Välj **Ja** om du vill att vs-kod automatiskt ska rikta in samma app service-webbapp med efterföljande distributioner.

1. Om du distribuerar till Linux väljer du **Bläddra på webbplats** i prompten för att visa din nyligen distribuerade webbapp när distributionen är klar. Webbläsaren ska Visa "Hello World!"

1. Om du distribuerar till Windows måste du först ange Node.js versions nummer för webbappen:

    1. I VS Code expanderar du noden för den nya app service, högerklickar på **program inställningar** och väljer **Lägg till ny inställning...**:

        ![Lägg till app Setting-kommando](media/quickstart-nodejs/add-setting.png)

    1. Ange `WEBSITE_NODE_DEFAULT_VERSION` för inställnings nyckeln.
    1. Ange `10.15.2` som inställnings värde.
    1. Högerklicka på noden för app service och välj **starta om**

        ![Starta om App Service-kommando](media/quickstart-nodejs/restart.png)

    1. Högerklicka på noden för App Service en gång till och välj **Bläddra webbplats**.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Felsöka Azure-inloggning

Om du ser felet **"det går inte att hitta en prenumeration med namnet [prenumerations-ID]"** när du loggar in på Azure kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om du ställer in miljövariablerna inte löser problemet kan du kontakta oss genom att välja knappen **jag körde i ett ärende** ovan.

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att göra redigeringar i VS Code, spara filer och sedan använda samma process som innan du väljer den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

Du kan visa loggens utdata (anrop till `console.log` ) från appen direkt i fönstret vs-kod.

1. I **Azure App Service** Explorer högerklickar du på noden app och väljer **Starta strömmande loggar**.

    ![Starta strömmande loggar](media/quickstart-nodejs/view-logs.png)

1. När du uppmanas väljer du att aktivera loggning och starta om programmet. När appen har startats om öppnas VS Code-utdatafönstret med en anslutning till logg strömmen. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Skärm bild av Visual Studio Code-prompten för att aktivera loggning och starta om programmet med knappen Ja markerat.":::

1. Efter några sekunder visar utdatafönstret ett meddelande som anger att du är ansluten till logg strömnings tjänsten. Du kan generera fler utdata-aktiviteter genom att uppdatera sidan i webbläsaren.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

> [!div class="nextstepaction"]
> [Självstudie: Node.js app med MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Node.js app](configure-language-nodejs.md)

Kolla in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera [Node Pack för Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Förutsättningar

Om du inte har ett Azure-konto kan du [Registrera dig](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) för ett kostnads fritt konto med $200 i Azure-krediter för att testa en kombination av tjänster.

Du behöver [Visual Studio Code](https://code.visualstudio.com/) installerat tillsammans med [Node.js och NPM](https://nodejs.org/en/download), Node.js Package Manager.

Du måste också installera [Azure App Service tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), som du kan använda för att skapa, hantera och distribuera Linux-Web Apps på Azure-plattformen som en tjänst (PaaS).

### <a name="sign-in"></a>Logga in

När tillägget har installerats loggar du in på ditt Azure-konto. I aktivitets fältet väljer du Azure-logotypen för att visa **Azure App Service** Explorer. Välj **Logga in på Azure...** och följ instruktionerna.

![Logga in på Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Felsökning

Om du ser felet **"det går inte att hitta prenumerationen med namnet [prenumerations-ID]"** kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av `export` .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Om du ställer in miljövariablerna inte löser problemet kan du kontakta oss genom att välja knappen **jag körde i ett ärende** nedan.

### <a name="prerequisite-check"></a>Krav kontroll

Innan du fortsätter måste du kontrol lera att alla nödvändiga komponenter är installerade och konfigurerade.

I VS Code bör du se din Azure-e-postadress i statusfältet och din prenumeration i **Azure App Service** Explorer.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Skapa ditt Node.js program

Skapa sedan ett Node.js-program som kan distribueras till molnet. Den här snabb starten använder en program generator för att snabbt Autogenerera ut programmet från en Terminal.

> [!TIP]
> Om du redan har slutfört [ guidenNode.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)kan du gå vidare till [distribuera till Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Autogenerera ett nytt program med Express generatorn

[Express](https://www.expressjs.com) är ett populärt ramverk för att skapa och köra Node.js program. Du kan Autogenerera (skapa) ett nytt Express-program med hjälp av verktyget [Express Generator](https://expressjs.com/en/starter/generator.html) . Express generatorn levereras som en NPM-modul och kan köras direkt (utan installation) med hjälp av kommando rads verktyget NPM `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git`Parametrarna anger att generatorn ska använda [pug](https://pugjs.org/api/getting-started.html) -mall (tidigare kallat `jade` ) och för att skapa en `.gitignore` fil.

Om du vill installera alla program beroenden går du till den nya mappen och kör `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Kör programmet

Se sedan till att programmet körs. Starta-programmet från terminalen med hjälp av `npm start` kommandot för att starta servern.

```bash
npm start
```

Öppna din webbläsare och gå till `http://localhost:3000` , där du bör se något som liknar detta:

![Kör Express-program](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här avsnittet ska du distribuera din Node.js-app med VS Code och Azure App Service tillägget. Den här snabb starten använder den mest grundläggande distributions modellen där appen är zippad och distribuerad till en Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Distribuera med Azure App Service

Öppna först mappen Application i VS Code.

```bash
code .
```

I **Azure App Service** Explorer väljer du ikonen blå UPPIL för att distribuera appen till Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Skärm bild av Azure App Service menyn i Visual Studio Code med pilen blå distribution vald.":::

> [!TIP]
> Du kan också distribuera från **kommando paletten** (Ctrl + Shift + P) genom att skriva "distribuera till webbapp" och köra kommandot **Azure App Service: distribuera till** webbapp.

1. Välj den katalog som för närvarande är öppen `myExpressApp` .

1. Välj **Skapa ny webbapp**, som distribueras till App Service på Linux som standard.

1. Ange ett globalt unikt namn för din webbapp och tryck på RETUR. Giltiga tecken för ett app-namn är "a-z", "0-9" och "-".

1. Välj din **Node.js-version**, LTS rekommenderas.

    Meddelande kanalen visar de Azure-resurser som skapas för din app.

1. Välj **Ja** när du uppmanas att uppdatera konfigurationen så att den körs `npm install` på mål servern. Din app distribueras sedan.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Skärm bild av prompten för att uppdatera konfigurationen på mål servern med knappen Ja markerad.":::

1. När distributionen startar uppmanas du att uppdatera arbets ytan så att senare distributioner automatiskt riktar sig till samma App Service-webbapp. Välj **Ja** för att se till att ändringarna distribueras till rätt app.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Skärm bild av prompten för att uppdatera din arbets yta med knappen Ja markerad.":::

> [!TIP]
> Se till att ditt program lyssnar på porten som tillhandahålls av PORT miljö variabeln: `process.env.PORT` .

### <a name="browse-the-app-in-azure"></a>Bläddra i appen i Azure

När distributionen är klar väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp.

### <a name="troubleshooting"></a>Felsökning

Om du ser felet **"du har inte behörighet att visa den här katalogen eller sidan."**, kunde programmet antagligen inte starta korrekt. Gå till nästa avsnitt och Visa loggens utdata för att hitta och åtgärda felet. Om du inte kan åtgärda det, kontakta oss genom att välja knappen **jag körde i ett ärende** nedan. Vi är glada att hjälpa!

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Uppdatera appen

Du kan distribuera ändringar i den här appen genom att använda samma process och välja den befintliga appen i stället för att skapa en ny.

## <a name="viewing-logs"></a>Visa loggar

I det här avsnittet får du lära dig att visa (eller "pilslut") loggarna från den App Service appen som körs. Alla anrop till `console.log` i appen visas i fönstret utdata i Visual Studio Code.

Hitta appen i **Azure App Service** Explorer, högerklicka på appen och välj **Starta strömmande loggar**.

Fönstret VS Code output öppnas med en anslutning till logg strömmen.

![Starta strömmande loggar](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Skärm bild av VS Code-prompten för att aktivera fil loggning och starta om webbappen med knappen Ja markerat.":::

Efter några sekunder visas ett meddelande som anger att du är ansluten till logg strömnings tjänsten. Uppdatera sidan några gånger för att se mer aktivitet.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera [Node Pack för Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.


::: zone-end
