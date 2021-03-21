---
title: 'Snabb start: skapa en Node.js webbapp'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748863"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Skapa en Node.js-webbapp i Azure

Kom igång med <abbr title="En HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila backend-program.">Azure App Service</abbr> genom att skapa en Node.js/Express-app lokalt med Visual Studio Code och sedan distribuera appen till Azure-molnet. Eftersom du använder en <abbr title="I Azure App Service är en bas nivå i vilken din app körs på samma virtuella datorer som andra appar, inklusive appar för andra kunder. Den här nivån är avsedd för utveckling och testning.">kostnads fri nivå</abbr>debiteras du inga kostnader för att slutföra den här snabb starten.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

- Ett Azure-konto med en aktiv <abbr title="En Azure-prenumeration är en logisk container som används för att etablera resurser i Azure. Den innehåller information om alla dina resurser, till exempel virtuella datorer, databaser och så vidare.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installera Git</a>
- [Node.js och NPM](https://nodejs.org). Kör kommandot `node --version` för att kontrol lera att Node.js är installerat.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) för Visual Studio Code.

[Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. klona och kör ett lokalt Node.js program

1. Öppna en terminal på den lokala datorn och klona exempel lagrings platsen:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigera till den nya mappen app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Installera beroendena:

    ```bash
    npm install
    ```

1. Starta appen för att testa den lokalt:

    ```bash
    npm start
    ```
    
1. Öppna webbläsaren och gå till `http://localhost:1337` . Webbläsaren ska Visa "Hello World!".

1. Tryck på <kbd>CTRL</kbd>  +  <kbd>C</kbd> i terminalen för att stoppa servern.

[Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Visa loggar från Visual Studio Code

Visa <abbr title="Alla anrop till `console.log` i appen visas i fönstret utdata i Visual Studio Code.">kvorumloggen</abbr> av appen som körs App Service.

1. Hitta appen i **Azure App Service** Explorer, högerklicka på appens namn och välj **Starta strömmande loggar**.

1. Fönstret Visual Studio-kod för utdata öppnas.

    ![Visa strömmande loggar](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Skärm bild av VS Code-prompten för att aktivera fil loggning och starta om webbappen med knappen Ja markerat.":::

1. Efter några sekunder visas ett meddelande som anger att du är ansluten till logg strömnings tjänsten. 
1. Uppdatera sidan några gånger för att se mer aktivitet.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Rensa resurser

Hitta appen i **Azure App Service** Explorer, högerklicka på appens namn och välj **ta bort**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Hitta appen i &quot;* *&quot; AZURE APP SERVICE &quot;* *&quot;-Utforskaren, högerklicka på appens namn och välj Ta bort":::

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten! Du kan distribuera ändringar i den här appen genom att använda samma process och välja den befintliga appen i stället för att skapa en ny.

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-verktyg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera [Node Pack för Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.