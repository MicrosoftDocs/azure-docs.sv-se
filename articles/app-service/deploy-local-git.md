---
title: Distribuera från lokal git-lagrings platsen
description: Lär dig hur du aktiverar lokal Git-distribution till Azure App Service. Ett av de enklaste sätten att distribuera kod från den lokala datorn.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560459"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure App Service

Den här instruktions guiden visar hur du distribuerar appen till [Azure App Service](overview.md) från en git-lagringsplats på den lokala datorn.

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i den här instruktions guiden:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installera git](https://www.git-scm.com/downloads).

- Ha en lokal git-lagringsplats med kod som du vill distribuera. Hämta en exempel lagrings plats genom att köra följande kommando i det lokala terminalfönstret:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

Se [Konfigurera autentiseringsuppgifter för distribution för Azure App Service](deploy-configure-credentials.md). Du kan använda antingen autentiseringsuppgifter för användare eller omfång.

## <a name="create-a-git-enabled-app"></a>Skapa en git-aktiverad app

Om du redan har en App Service-app och vill konfigurera lokal Git-distribution för den, se [Konfigurera en befintlig app](#configure-an-existing-app) i stället.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör [`az webapp create`](/cli/azure/webapp#az_webapp_create) med `--deployment-local-git` alternativet. Exempel:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Utdata innehåller en URL som: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Använd den här URL: en för att distribuera din app i nästa steg.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kör [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) från roten för git-lagringsplatsen. Exempel:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

När du kör denna cmdlet från en katalog som är git-lagringsplats, skapar den automatiskt en git-fjärranslutning till din App Service-app åt dig, med namnet `azure` .

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

I portalen måste du först skapa en app och sedan konfigurera distributionen för den. Se [Konfigurera en befintlig app](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Konfigurera en befintlig app

Om du inte har skapat en app ännu kan du läsa [skapa en git-aktiverad app](#create-a-git-enabled-app) i stället.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Kör [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Exempel:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Utdata innehåller en URL som: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Använd den här URL: en för att distribuera din app i nästa steg.

> [!TIP]
> Denna URL innehåller användar omfångets användar namn för distribution. Om du vill kan du [använda autentiseringsuppgifterna för programscope](deploy-configure-credentials.md#appscope) i stället. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ställ in `scmType` appen genom att köra cmdleten [set-AzResource](/powershell/module/az.resources/set-azresource) .

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Gå till appens hanterings sida i [Azure Portal](https://portal.azure.com).

1. På den vänstra menyn väljer du inställningar för **distributions Center**  >  . Välj **lokal git** i **källa** och klicka sedan på **Spara**.

    ![Visar hur du aktiverar lokal Git-distribution för App Service i Azure Portal](./media/deploy-local-git/enable-portal.png)

1. I avsnittet lokal git kopierar du Git Clone- **URI: n** för senare. Denna URI innehåller inga autentiseringsuppgifter.

-----

## <a name="deploy-the-web-app"></a>Distribuera webbappen

1. I ett lokalt terminalfönster ändrar du katalogen till roten för git-lagringsplatsen och lägger till en git-fjärrserver med den URL som du fick från din app. Om den valda metoden inte ger dig en URL använder du `https://<app-name>.scm.azurewebsites.net/<app-name>.git` med namnet på appen i `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Om du har [skapat en git-aktiverad app i PowerShell med New-AzWebApp](#create-a-git-enabled-app)har fjärrplatsen redan skapats för dig.
   
1. Skicka till Azure-fjärrservern med `git push azure master` . 
   
1. I fönstret **git Credential Manager** anger du [användar omfång eller programomfång](#configure-a-deployment-user), inte dina autentiseringsuppgifter för Azure-inloggning.

    Om din git-Fjärradress redan innehåller användar namn och lösen ord, uppmanas du inte att göra det. 
   
1. Granska utdata. Du kan se en körnings bestämd automatisering, till exempel MSBuild för ASP.NET, `npm install` för Node.js och `pip install` för python. 
   
1. Bläddra till din app i Azure Portal för att kontrol lera att innehållet har distribuerats.

## <a name="troubleshoot-deployment"></a>Felsöka distribution

Följande vanliga fel meddelanden kan visas när du använder Git för att publicera till en App Service-app i Azure:

|Meddelande|Orsak|Lösning
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Appen är inte igång.|Starta appen i Azure Portal. Git-distribution är inte tillgängligt när webbappen har stoppats.|
|`Couldn't resolve host 'hostname'`|Adress informationen för fjärran slutet av Azure är felaktig.|Använd `git remote -v` kommandot för att lista alla fjärranslutna, tillsammans med tillhör ande URL. Kontrol lera att URL: en för "Azure"-fjärrplatsen är korrekt. Om det behövs tar du bort och återskapar denna fjärr anslutning med rätt URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Du har inte angett någon gren under `git push` eller så har du inte angett `push.default` värdet i `.gitconfig` .|Kör `git push` igen och ange huvud grenen: `git push azure main` .|
|`src refspec [branchname] does not match any.`|Du försökte skicka till en annan gren än main på Azure-fjärrplatsen.|Kör `git push` igen och ange huvud grenen: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Det här felet kan inträffa om du försöker skicka en stor git-lagringsplats via HTTPS.|Ändra git-konfigurationen på den lokala datorn så att den blir `postBuffer` större. Exempel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Du har distribuerat en Node.js-app med en _package.jspå_ en fil som anger ytterligare nödvändiga moduler.|Granska `npm ERR!` fel meddelandena före det här felet för mer information om felet. Följande är kända orsaker till det här felet och motsvarande `npm ERR!` meddelanden:<br /><br />**Felaktig package.jspå fil**: `npm ERR! Couldn't read dependencies.`<br /><br />**Den ursprungliga modulen har ingen binär distribution för Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />eller <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ytterligare resurser

- [App Service build Server (Project kudu-dokumentation)](https://github.com/projectkudu/kudu/wiki)
- [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
- [Exempel: skapa en webbapp och distribuera kod från en lokal git-lagringsplats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exempel: skapa en webbapp och distribuera kod från en lokal git-lagringsplats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
