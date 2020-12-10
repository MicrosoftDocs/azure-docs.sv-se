---
title: 'Snabb start: skapa en PHP-webbapp'
description: Distribuera din första PHP-Hello World för att Azure App Service på några minuter. Du distribuerar med git, som är ett av många sätt att distribuera till App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 848b3dd60e4c8ea5ac1fae0b7632fa5a54e07dd1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007797"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Skapa en PHP-webbapp i Azure App Service

::: zone pivot="platform-windows"  
[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabb starts guiden visar hur du distribuerar en PHP-app till Azure App Service i Windows.
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst.  Den här snabbstartskursen visar hur du distribuerar en PHP-app till Azure App Service on Linux.
::: zone-end  

Du skapar webbappen med [Azure CLI](/cli/azure/get-started-with-azure-cli) i Cloud Shell och använder Git för att distribuera PHP-exempelkoden till webbappen.

![Exempelapp som körs i Azure](media/quickstart-php/hello-world-in-browser.png)

Du kan följa stegen här på en Mac-, Windows- eller Linux-dator. Det tar cirka fem minuter att slutföra självstudiekursen när de nödvändiga komponenterna har installerats.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* <a href="https://git-scm.com/" target="_blank">Installera Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">Installera PHP</a>

## <a name="download-the-sample-locally"></a>Ladda ned exemplet lokalt

Kör följande kommandon i ett terminalfönster. Exempelprogrammet klonas då till din lokala dator och du kommer till katalogen med exempelkoden. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. Öppna ett terminalfönster och använd kommandot `php` för att starta den inbyggda PHP-webbservern.

```bash
php -S localhost:8080
```

Öppna en webbläsare och navigera till exempelappen på `http://localhost:8080`.

Du ser meddelandet **Hello World!** från exempelappen på sidan.

![Exempelapp som körs lokalt](media/quickstart-php/localhost-hello-world-in-browser.png)

Tryck på **Ctrl+C** i terminalfönstret för att avsluta webbservern.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

Skapa i Cloud Shell en webbapp i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp#az-webapp-create). 

Ersätt `<app-name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen har angetts till `PHP|7.4`. Om du vill se alla körningar som stöds kör du [`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> Med stopp tolknings symbolen `(--%)` , som introducerades i powershell 3,0, använder PowerShell för att undvika att tolka ininformation som PowerShell-kommandon eller uttryck.
>

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Du har skapat en tom ny webbapp med Git-distribution aktive rad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>

Bläddra till webbappen som precis skapades. Ersätt _&lt; app-name->_ med ditt unika app-namn som skapades i föregående steg.

```bash
http://<app-name>.azurewebsites.net
```

Så här bör din nya webbapp se ut:

![Sida för tom webbapp](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  master -> master
</pre>

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```
http://<app-name>.azurewebsites.net
```

PHP-exempelkoden körs i en Azure App Service-webbapp.

![Exempelapp som körs i Azure](media/quickstart-php/hello-world-in-browser.png)

**Grattis!** Du har distribuerat din första PHP-app till App Service.

## <a name="update-locally-and-redeploy-the-code"></a>Uppdatera lokalt och distribuera om koden

Öppna filen `index.php` i PHP-appen med ett lokalt textredigeringsprogram och gör små ändringar i texten i strängen bredvid `echo`:

```php
echo "Hello Azure!";
```

I det lokala terminalfönstret sparar du ändringarna i Git och skickar sedan kodändringarna till Azure.

```bash
git commit -am "updated output"
git push azure master
```

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades när du skulle **söka efter appen** och klickar på knappen för att uppdatera sidan.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

1. Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den webbapp som du skapade. Sök efter och välj **app Services**.

    ![Sök efter App Services, Azure Portal, skapa PHP-webbapp](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Välj namnet på din Azure-App.

    ![Portalnavigering till Azure-app](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Webb programmets **översikts** sida visas. Här kan du utföra grundläggande hanterings åtgärder som att **Bläddra**, **stoppa**, **starta om** och **ta bort**.

    ![App Service-sidan på Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Menyn webbapp innehåller olika alternativ för att konfigurera din app. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [PHP med MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera PHP-app](configure-language-php.md)
