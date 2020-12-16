---
title: 'Självstudie: Felsöka med Azure Monitor'
description: Lär dig hur Azure Monitor och Log Analytics hjälper dig att övervaka App Service-webbappen. Azure Monitor maximerar tillgängligheten genom att leverera en omfattande lösning för övervakning av dina miljöer.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 049adac5521efd68ae8aa77af2d1007f9dfe0c0e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587000"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Självstudie: Felsöka en App Service-app med Azure Monitor

> [!NOTE]
> Azure Monitor-integrering med App Service är i för [hands version](https://aka.ms/appsvcblog-azmon).
>

Den här självstudien visar hur du felsöker en [App Service](overview.md) -app med hjälp av [Azure Monitor](../azure-monitor/overview.md). Exempel appen innehåller kod som är avsedd för avgas minne och orsakar HTTP 500-fel, så att du kan diagnostisera och åtgärda problemet med hjälp av Azure Monitor. När du är klar har du ett exempel på en app som körs på App Service på Linux integrerat med [Azure Monitor](../azure-monitor/overview.md).

[Azure Monitor](../azure-monitor/overview.md) maximerar tillgängligheten och prestandan för dina program och tjänster genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera en webbapp med Azure Monitor
> * Skicka konsol loggar till Log Analytics
> * Använd logg frågor för att identifiera och felsöka webb program fel

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- [Azure-prenumeration](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Skapa Azure-resurser

Först kör du flera kommandon lokalt för att konfigurera en exempel-app som ska användas med den här självstudien. Kommandona klonar en exempel app, skapar Azure-resurser, skapar en distributions användare och distribuerar appen till Azure. Du uppmanas att ange det lösen ord som anges som en del av att skapa distributions användaren. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Konfigurera Azure Monitor (för hands version)

### <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics arbets yta

Nu när du har distribuerat exempel appen till Azure App Service konfigurerar du övervaknings funktioner för att felsöka appen när problem uppstår. Azure Monitor lagrar loggdata i en Log Analytics arbets yta. En arbets yta är en behållare som innehåller data och konfigurations information.

I det här steget skapar du en Log Analytics arbets yta för att konfigurera Azure Monitor med din app.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [För Azure Monitor Log Analytics betalar du för data inmatning och data lagring.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning

Diagnostiska inställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor loggar för analys med andra övervaknings data med hjälp av logg frågor. I den här självstudien aktiverar du webb servern och standard-utdata-/fel loggar. Se de [logg typer som stöds](./troubleshoot-diagnostic-logs.md#supported-log-types) för en fullständig lista över logg typer och beskrivningar.

Du kör följande kommandon för att skapa diagnostiska inställningar för AppServiceConsoleLogs (standardutdata/fel) och AppServiceHTTPLogs (webb server loggar). Ersätt _\<app-name>_ och _\<workspace-name>_ med dina värden. 

> [!NOTE]
> De två första kommandona `resourceID` och `workspaceID` , är variabler som ska användas i `az monitor diagnostic-settings create` kommandot. Mer information om det här kommandot finns i [skapa diagnostiska inställningar med hjälp av Azure CLI](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli) .
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Felsöka appen

Gå till `http://<app-name>.azurewebsites.net`.

Exempel appen, ImageConverter, konverterar inkluderade bilder från `JPG` till `PNG` . En bugg har avsiktligt placerats i koden för den här själv studie kursen. Om du väljer tillräckligt många avbildningar genererar appen ett HTTP 500-fel under bild konverteringen. Tänk på att det här scenariot inte ansågs under utvecklings fasen. Du ska använda Azure Monitor för att felsöka felet.

### <a name="verify-the-app-is-works"></a>Kontrol lera att appen fungerar

Om du vill konvertera avbildningar klickar du på `Tools` och väljer `Convert to PNG` .

![Klicka på verktyg och välj Konvertera till PNG](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Välj de två första bilderna och klicka på `convert` . Detta kommer att konverteras.

![Välj de två första bilderna](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Bryt appen

Nu när du har verifierat appen genom att konvertera två avbildningar, försöker vi konvertera de första fem bilderna.

![Omvandla de första fem bilderna](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Den här åtgärden Miss lyckas och genererar ett `HTTP 500` fel som inte har testats under utvecklingen.

![Konverteringen leder till ett HTTP 500-fel](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Använd logg fråga för att Visa Azure Monitor loggar

Nu ska vi se vilka loggar som är tillgängliga på arbets ytan Log Analytics. 

Klicka på den här [Log Analytics arbets ytans länk](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) för att komma åt din arbets yta i Azure Portal.

I Azure Portal väljer du Log Analytics arbets ytan.

### <a name="log-queries"></a>Loggfrågor

Med logg frågor kan du utnyttja värdet för de data som samlas in i Azure Monitor loggar. Du använder logg frågor för att identifiera loggarna i både AppServiceHTTPLogs och AppServiceConsoleLogs. Mer information om logg frågor finns i [Översikt över logg](../azure-monitor/log-query/log-query-overview.md) frågor.

### <a name="view-appservicehttplogs-with-log-query"></a>Visa AppServiceHTTPLogs med logg fråga

Nu när vi har öppnat appen ska vi se de data som är kopplade till HTTP-begäranden, som finns i `AppServiceHTTPLogs` .

1. Klicka på `Logs` från den vänstra navigeringen.

![Logga Anlytics Worksace-loggar](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Sök efter `appservice` och dubbelklicka på `AppServiceHTTPLogs` .

![Tabellerna i Log Analytics-arbetsyta](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klicka på `Run`.

![Log Analytics arbets yta App Service HTTP-loggar](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs`Frågan returnerar alla begär Anden under de senaste 24 timmarna. Kolumnen `ScStatus` innehåller HTTP-statusen. För att diagnosticera `HTTP 500` felen begränsar `ScStatus` du till 500 och kör frågan, enligt nedan:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Visa AppServiceConsoleLogs med logg fråga

Nu när du har bekräftat HTTP-500 ska vi ta en titt på standardutdata/fel från appen. Dessa loggar finns i ' AppServiceConsoleLogs '.

(1) klicka `+` för att skapa en ny fråga. 

(2) dubbelklicka på `AppServiceConsoleLogs` tabellen och klicka på `Run` . 

Eftersom konverteringen av fem bilder resulterar i Server fel, kan du se om appen också skriver fel genom `ResultDescription` att filtrera efter fel, som visas nedan:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

I `ResultDescription` kolumnen visas följande fel:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Anslut AppServiceHTTPLogs och AppServiceConsoleLogs

Nu när du har identifierat både HTTP-500 och standard fel måste du kontrol lera om det finns en korrelation mellan dessa meddelanden. Sedan kopplar du ihop tabellerna baserat på tidstämpeln `TimeGenerated` .

> [!NOTE]
> En fråga har förberetts för dig som gör följande:
>
> - Filtrerar HTTPLogs för 500-fel
> - Frågor konsol loggar
> - Kopplar tabellerna på `TimeGenerated`
>

Kör följande fråga:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

I `ResultDescription` kolumnen visas följande fel vid samma tidpunkt som webb server fel:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

Meddelande tillståndets minne har nåtts på rad 20 av `process.php` . Nu har du bekräftat att programmet genererade ett fel under HTTP 500-fel. Vi tar en titt på koden för att identifiera problemet.

## <a name="identify-the-error"></a>Identifiera felet

Öppna `process.php` och titta på rad 20 i den lokala katalogen. 

```php
imagepng($imgArray[$x], $filename);
```

Det första argumentet, `$imgArray[$x]` , är en variabel som håller alla JPGs (i minnet) som behöver konverteras. Dock `imagepng` behöver bara bilden konverteras och inte alla bilder. För inläsning av avbildningar är inte nödvändigt och kan orsaka minnes överbelastning, vilket leder till HTTP-500. Vi uppdaterar koden för att läsa in avbildningar på begäran för att se om det löser problemet. Sedan kommer du att förbättra koden för att åtgärda minnes problemet.

## <a name="fix-the-app"></a>Åtgärda appen

### <a name="update-locally-and-redeploy-the-code"></a>Uppdatera lokalt och distribuera om koden

Du gör följande ändringar för `process.php` att hantera minnes överbelastningen:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Gå till `http://<app-name>.azurewebsites.net`. 

Att konvertera bilder bör inte längre producera HTTP 500-fel.

![PHP-app som körs i Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Ta bort den diagnostiska inställningen med följande kommando:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Vad du lärt dig:

> [!div class="checklist"]
> * Konfigurerat en webbapp med Azure Monitor
> * Skickade loggar till Log Analytics
> * Använda logg frågor för att identifiera och felsöka webb program fel

## <a name="next-steps"></a><a name="nextsteps"></a> Nästa steg
* [Fråga efter loggar med Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera app-loggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)