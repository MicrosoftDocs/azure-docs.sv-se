---
title: 'Snabbstart: Skapa en statisk HTML-webbapp'
description: Distribuera din första HTML-Hello World att Azure App Service på några minuter. Du distribuerar med Git, vilket är ett av många sätt att distribuera till App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be555039375e2d71d00bcf8ee58898f54cc3645a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379578"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Skapa en statisk HTML-webbapp i Azure

Den här snabbstarten visar hur du distribuerar en enkel HTML+CSS-webbplats till <abbr title="En HTTP-baserad tjänst som är värd för webbprogram, REST-API:er och mobila serverdelsprogram.">Azure App Service</abbr>. Du slutför den här snabbstarten i [Cloud Shell](../cloud-shell/overview.md), men du kan också köra dessa kommandon lokalt med [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

I [Cloud Shell](../cloud-shell/overview.md)du en snabbstartskatalog och ändrar sedan till den.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din snabbstartskatalog.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Skapa en webbapp

Ändra till den katalog som innehåller exempelkoden och kör `az webapp up` kommandot. **Ersätt** `<app-name>` med ett globalt unikt namn.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Felsökning</summary>
<ul>
<li>Om kommandot inte känns igen kontrollerar du att du har Installerat Azure CLI enligt <code>az</code> beskrivningen i <a href="#1-prepare-your-environment">Förbereda din miljö.</a></li>
<li>Ersätt <code>&lt;app-name&gt;</code> med ett namn som är unikt för hela Azure ( giltiga tecken är , och <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.</li>
<li>Argumentet <code>--sku F1</code> skapar webbappen på prisnivån Kostnadsfri. Utelämna det här argumentet för att använda en snabbare premiumnivå, vilket medför en kostnad per timme.</li>
<li>Argumentet <code>--html</code> säger att allt mappinnehåll ska behandlas som statiskt innehåll och att byggautomatisering ska inaktiveras.</li>
<li>Du kan även inkludera argumentet där är <code>--location &lt;location-name&gt;</code> <code>&lt;location-name&gt;</code> en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> kommandot .</li>
</ul>
</details>

Det kan ta några minuter att slutföra kommandot. 

<details>
<summary>Vad <code>az webapp up</code> gör?</summary>
<p>Kommandot <code>az webapp up</code> utför följande åtgärder:</p>
<ul>
<li>Skapa en standardresursgrupp.</li>
<li>Skapa en App Service plan.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Skapa en App Service med</a> det angivna namnet.</li>
<li><a href="/azure/app-service/deploy-zip">Zip-distribuera</a> filer från den aktuella arbetskatalogen till appen.</li>
<li>När den körs visas meddelanden om resursskapande, loggning och ZIP-distribution.</li>
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

Du behöver värdet `resourceGroup` för att rensa [resurser](#6-clean-up-resources) senare.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Bläddra till appen

Gå till appens URL i en webbläsare: `http://<app_name>.azurewebsites.net`.

Sidan körs som en Azure App Service-webbapp.

![Exempelstartsida för app](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Uppdatera och distribuera om appen

I Cloud Shell du **för att** öppna `nano index.html` nanotextredigeraren. 

I `<h1>` rubriktaggen ändrar du "Azure App Service - Sample Static HTML Site" till "Azure App Service".

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Spara** ändringarna med hjälp av kommandot `^O` .

**Avsluta** nano med hjälp av kommandot `^X` .

Distribuera om appen med `az webapp up` kommandot .

```bash
az webapp up --html
```

Växla tillbaka till webbläsarfönstret som öppnades i **steget Bläddra till** appen.

**Uppdatera** sidan.

![Uppdaterad exempelstartsida för app](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Hantera din nya Azure-app

**Gå** till [Azure Portal](https://portal.azure.com)., 

**Sök** efter och **välj** **App Services**.

![Välj App Services i Azure Portal](./media/quickstart-html/portal0.png)

**Välj** namnet på din Azure-app.

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
