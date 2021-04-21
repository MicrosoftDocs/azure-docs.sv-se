---
title: Distribuera från lokal Git-lagringsplatsen
description: Lär dig hur du aktiverar lokal Git-distribution för att Azure App Service. Ett av de enklaste sätten att distribuera kod från din lokala dator.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3196233728bb7f6493bbc06234c62d261ac99254
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832418"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure App Service

Den här guiden visar hur du distribuerar din app till Azure App Service [från](overview.md) en Git-lagringsplats på din lokala dator.

## <a name="prerequisites"></a>Förutsättningar

Så här följer du stegen i den här instruktionerna:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installera Git](https://www.git-scm.com/downloads).

- Ha en lokal Git-lagringsplats med kod som du vill distribuera. Om du vill ladda ned en exempeldatabas kör du följande kommando i det lokala terminalfönstret:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

Se [Konfigurera autentiseringsuppgifter för distribution för Azure App Service](deploy-configure-credentials.md). Du kan antingen använda autentiseringsuppgifter för användaromfång eller autentiseringsuppgifter för programomfång.

## <a name="create-a-git-enabled-app"></a>Skapa en Git-aktiverad app

Om du redan har en App Service-app och vill konfigurera lokal Git-distribution för den kan du se [Konfigurera en befintlig app i](#configure-an-existing-app) stället.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör [`az webapp create`](/cli/azure/webapp#az_webapp_create) med `--deployment-local-git` alternativet . Exempel:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Utdata innehåller en URL som: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Använd den här URL:en för att distribuera din app i nästa steg.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör [New-AzWebApp från](/powershell/module/az.websites/new-azwebapp) roten på Git-lagringsplatsen. Exempel:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

När du kör denna cmdlet från en katalog som är en Git-lagringsplats skapar den automatiskt en Fjärransluten Git till din App Service-app åt dig med namnet `azure` .

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

I portalen måste du först skapa en app och sedan konfigurera distributionen för den. Se [Konfigurera en befintlig app.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Konfigurera en befintlig app

Om du inte har skapat en app ännu kan du se Skapa [en Git-aktiverad app i](#create-a-git-enabled-app) stället.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) . Exempel:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Utdata innehåller en URL som: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Använd den här URL:en för att distribuera din app i nästa steg.

> [!TIP]
> Den här URL:en innehåller användarnamnet för distribution av användaromfång. Om du vill kan du använda [autentiseringsuppgifterna för programomfång i](deploy-configure-credentials.md#appscope) stället. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ange för `scmType` din app genom att köra [cmdleten Set-AzResource.](/powershell/module/az.resources/set-azresource)

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. I [Azure Portal](https://portal.azure.com)går du till appens hanteringssida.

1. På den vänstra menyn väljer du **Inställningar för**  >  **Distributionscenter.** Välj **Lokal Git** i **Källa** och klicka sedan på **Spara.**

    ![Visar hur du aktiverar lokal Git-App Service i Azure Portal](./media/deploy-local-git/enable-portal.png)

1. I avsnittet Lokal Git kopierar du Git Clone **URI** för senare. Den här URI:en innehåller inga autentiseringsuppgifter.

-----

## <a name="deploy-the-web-app"></a>Distribuera webbappen

1. I ett lokalt terminalfönster ändrar du katalogen till roten på Git-lagringsplatsen och lägger till en Fjärransluten Git med hjälp av den URL som du fick från din app. Om den valda metoden inte ger dig en URL använder du `https://<app-name>.scm.azurewebsites.net/<app-name>.git` med appnamnet i `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Om du [har skapat en Git-aktiverad app i PowerShell med hjälp av New-AzWebApp](#create-a-git-enabled-app)har fjärrservern redan skapats åt dig.
   
1. Push-skicka till Azure-fjärrservern med `git push azure master` . 
   
1. I **fönstret Git Autentiseringshanteraren** anger du dina autentiseringsuppgifter för användaromfång eller programomfång, [](#configure-a-deployment-user)inte dina autentiseringsuppgifter för Azure-inloggning.

    Om din fjärranslutna URL för Git redan innehåller användarnamnet och lösenordet uppmanas du inte att göra det. 
   
1. Granska utdata. Du kan se körningsspecifik automatisering, till exempel MSBuild för ASP.NET, `npm install` för Node.js och för `pip install` Python. 
   
1. Bläddra till appen i Azure Portal för att verifiera att innehållet har distribuerats.

## <a name="troubleshoot-deployment"></a>Felsöka distribution

Du kan se följande vanliga felmeddelanden när du använder Git för att publicera till en App Service i Azure:

|Meddelande|Orsak|Lösning
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Appen är inte igång.|Starta appen i Azure Portal. Git-distributionen är inte tillgänglig när webbappen stoppas.|
|`Couldn't resolve host 'hostname'`|Adressinformationen för "azure"-fjärrdatorn är felaktig.|Använd kommandot `git remote -v` för att visa en lista över alla fjärrdatorer, tillsammans med den associerade URL:en. Kontrollera att URL:en för "azure"-fjärrdatorn är korrekt. Om det behövs tar du bort och återskapar den här fjärrdatorn med rätt URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Du angav inte någon gren under `git push` , eller så har du inte angett värdet i `push.default` `.gitconfig` .|Kör `git push` igen och ange huvudgrenen: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Du push-distribuerade en lokal gren som inte matchar appdistributionsgrenen på "azure".|Kontrollera att current branch är `master` . Om du vill ändra standardgrenen använder du `DEPLOYMENT_BRANCH` programinställningen.|
|`src refspec [branchname] does not match any.`|Du försökte skicka till en annan gren än main på fjärrdatorn "azure".|Kör `git push` igen och ange huvudgrenen: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Det här felet kan inträffa om du försöker skicka en stor git-lagringsplats via HTTPS.|Ändra git-konfigurationen på den lokala datorn så att den `postBuffer` blir större. Exempel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Du har distribuerat en Node.js-app _medpackage.jsfil_ som anger ytterligare nödvändiga moduler.|Granska `npm ERR!` felmeddelandena före det här felet för mer kontext om felet. Följande är kända orsaker till det här felet och motsvarande `npm ERR!` meddelanden:<br /><br />**Felaktigt package.jspå filen**: `npm ERR! Couldn't read dependencies.`<br /><br />**Inbyggd modul har ingen binär distribution för Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />eller <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ytterligare resurser

- [App Service (Project Kudu-dokumentation)](https://github.com/projectkudu/kudu/wiki)
- [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exempel: Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
