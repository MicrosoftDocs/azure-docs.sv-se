---
title: 'Snabb start: skapa en statisk HTML-webbapp'
description: Distribuera din första HTML-Hello World till Azure App Service på några minuter. Du distribuerar med git, som är ett av många sätt att distribuera till App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 538d414ce606b944fcea7adbb1c817386e13090e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178583"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Skapa en statisk HTML-webbapp i Azure

Den här snabb starten visar hur du distribuerar en enkel HTML + CSS-webbplats till <abbr title="En HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila backend-program.">Azure App Service</abbr>. Du kommer att slutföra den här snabb starten i [Cloud Shell](../cloud-shell/overview.md), men du kan också köra dessa kommandon lokalt med [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

I [Cloud Shell](../cloud-shell/overview.md)skapar du en snabb starts katalog och ändrar sedan till den.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din snabbstartskatalog.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. skapa en webbapp

Ändra till den katalog som innehåller exempelkoden och kör `az webapp up` kommandot. **Ersätt** `<app-name>` med ett globalt unikt namn.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Felsökning</summary>
<ul>
<li>Om <code>az</code> kommandot inte känns igen kontrollerar du att Azure CLI är installerat enligt beskrivningen i <a href="#1-prepare-your-environment">förbereda din miljö</a>.</li>
<li>Ersätt <code>&lt;app-name&gt;</code> med ett namn som är unikt för alla Azure ( <em> giltiga tecken är <code>a-z</code> , <code>0-9</code> och <code>-</code> </em> ). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.</li>
<li><code>--sku F1</code>Argumentet skapar webb programmet på den kostnads fria pris nivån. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå, vilket innebär en timkostnad.</li>
<li>Argumentet innehåller information <code>--html</code> om att behandla allt mappinnehåll som statiskt innehåll och inaktivera Bygg automatisering.</li>
<li>Du kan också inkludera argumentet <code>--location &lt;location-name&gt;</code> där <code>&lt;location-name&gt;</code> är en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> kommandot.</li>
</ul>
</details>

Det kan ta några minuter att slutföra kommandot. 

<details>
<summary>Vad <code>az webapp up</code> gör jag?</summary>
<p>Kommandot <code>az webapp up</code> utför följande åtgärder:</p>
<ul>
<li>Skapa en standardresursgrupp.</li>
<li>Skapa en standard App Service plan.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Skapa en app service-app</a> med det angivna namnet.</li>
<li><a href="/azure/app-service/deploy-zip">Zip-distribuera</a> filer från den aktuella arbetskatalogen till appen.</li>
<li>Under körningen innehåller den meddelanden om att skapa, logga och ZIP-distribution.</li>
</ul>

När den är klar visas information som liknar följande exempel:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Du behöver `resourceGroup` värdet för att [Rensa resurser](#6-clean-up-resources) senare.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Bläddra till appen

Gå till appens URL i en webbläsare: `http://<app_name>.azurewebsites.net`.

Sidan körs som en Azure App Service-webbapp.

![Exempelstartsida för app](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. uppdatera och distribuera om appen

I Cloud Shell **skriver** `nano index.html` du för att öppna nano text redigeraren. 

`<h1>`Ändra "Azure App Service-exempel statisk HTML-webbplats" till "Azure App Service" i taggen rubrik.

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Spara** ändringarna med hjälp av kommandot `^O` .

**Avsluta** nano genom att använda kommandot `^X` .

Distribuera appen igen med `az webapp up` kommandot.

```bash
az webapp up --html
```

Växla tillbaka till webbläsarfönstret som öppnades i **Bläddra till app** -steget.

**Uppdatera** sidan.

![Uppdaterad exempelstartsida för app](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. hantera din nya Azure-App

**Navigera** till [Azure Portal](https://portal.azure.com)., 

**Sök** efter och **Välj** **app Services**.

![Välj App Services i Azure Portal](./media/quickstart-html/portal0.png)

**Välj** namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/quickstart-html/portal1.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![App Service-blad på Azure Portal](./media/quickstart-html/portal2.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen.

<hr/>

## <a name="6-clean-up-resources"></a>6. Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando i Cloud Shell. Kom ihåg att resursgruppens namn har genererats automatiskt för dig i steget [skapa en webbapp](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Det kan några minuter att köra kommandot.

<hr/>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain-uiex.md)
