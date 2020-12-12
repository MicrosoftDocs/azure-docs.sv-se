---
title: Distribuera från lokal git-lagrings platsen
description: Lär dig hur du aktiverar lokal Git-distribution till Azure App Service. Ett av de enklaste sätten att distribuera kod från den lokala datorn.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 26fd8bc73fad3ea313641fc4b1e0f454ee2c0813
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347786"
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

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-with-kudu-build-server"></a>Distribuera med kudu build-Server

Det enklaste sättet att aktivera lokal Git-distribution för din app med kudu App Service build Server är att använda Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Hämta distributions-URL: en

Om du vill hämta URL: en för att aktivera lokal Git-distribution för en befintlig app, kör [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) du i Cloud Shell. Ersätt \<app-name> och \<group-name> med namnet på din app och dess Azure-resurs grupp.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Om du använder en Linux app-service-plan måste du lägga till den här parametern:--Runtime python | 3.7


Eller skapa en ny git-aktiverad app genom att köra [`az webapp create`](/cli/azure/webapp#az-webapp-create) i Cloud Shell med `--deployment-local-git` parametern. Ersätt \<app-name> , \<group-name> och \<plan-name> med namnen för din nya git-app, dess Azure-adressresurs och dess Azure App Service plan.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Antingen returnerar kommandot en URL som: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Använd den här URL: en för att distribuera din app i nästa steg.

I stället för att använda den här URL: en på konto nivå kan du också aktivera lokal git genom att använda autentiseringsuppgifter på program nivå. Azure App Service automatiskt genererar dessa autentiseringsuppgifter för varje app. 

Hämta appens autentiseringsuppgifter genom att köra följande kommando i Cloud Shell. Ersätt \<app-name> och \<group-name> med appens namn och namnet på Azure-resurs gruppen.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Använd den URL som används för att distribuera din app i nästa steg.

### <a name="deploy-the-web-app"></a>Distribuera webbappen

1. Öppna ett lokalt terminalfönster till din lokala git-lagringsplats och Lägg till en Azure-fjärrserver. I följande kommando ersätter \<url> du med den användarspecifika URL: en för distributionen eller den app-Specific URL som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Skicka till Azure-fjärrservern med `git push azure master` . 
   
1. I fönstret **git Credential Manager** anger du ditt [distributions användar lösen ord](#configure-a-deployment-user), inte ditt Azures inloggnings lösen ord.
   
1. Granska utdata. Du kan se en körnings bestämd automatisering, till exempel MSBuild för ASP.NET, `npm install` för Node.js och `pip install` för python. 
   
1. Bläddra till din app i Azure Portal för att kontrol lera att innehållet har distribuerats.

## <a name="deploy-with-azure-pipelines-builds"></a>Distribuera med Azure pipelines-versioner

Om ditt konto har de behörigheter som krävs kan du konfigurera Azure-pipeliner (för hands version) för att aktivera lokal Git-distribution för din app. 

- Ditt Azure-konto måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Ditt Azure-konto måste ha **ägar** rollen i din Azure-prenumeration.

- Du måste vara administratör i det Azure DevOps-projekt som du vill använda.

Så här aktiverar du lokal Git-distribution för din app med Azure-pipeliner (för hands version):

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **app Services**. 

1. Välj din Azure App Service-app och välj **Deployment Center** på den vänstra menyn.
   
1. På sidan **distributions Center** väljer du **lokal git** och väljer sedan **Fortsätt**. 
   
   ![Välj lokal git och välj sedan Fortsätt](media/app-service-deploy-local-git/portal-enable.png)
   
1. På sidan **build Provider** väljer du **Azure-pipelines (för hands version)** och väljer sedan **Fortsätt**. 
   
   ![Välj Azure-pipeliner (för hands version) och välj sedan Fortsätt.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Konfigurera en ny Azure DevOps-organisation på sidan **Konfigurera** , eller ange en befintlig organisation och välj sedan **Fortsätt**.
   
   > [!NOTE]
   > Om din befintliga Azure DevOps-organisation inte finns med i listan kan du behöva länka den till din Azure-prenumeration. Mer information finns i [definiera din pipeline för CD-version](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Beroende på din App Service plan [pris nivå](https://azure.microsoft.com/pricing/details/app-service/plans/)kan du se sidan **distribuera till mellanlagringsplats** . Välj om du vill [Aktivera distributions platser](deploy-staging-slots.md)och välj sedan **Fortsätt**.
   
1. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Slutför**.
   
1. När Azure-pipeline är klar kopierar du URL: en för git-lagringsplatsen från sidan **distributions Center** som ska användas i nästa steg. 
   
   ![Kopiera git-lagringsplatsens URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. I det lokala terminalfönstret lägger du till en Azure-fjärrkontroll till din lokala git-lagringsplats. I kommandot ersätter \<url> du med URL: en för git-lagringsplatsen som du fick från föregående steg.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Skicka till Azure-fjärrservern med `git push azure master` . 
   
1. Logga in med ditt visualstudio.com-användarnamn på sidan **git Credential Manager** . Andra autentiseringsmetoder finns i [Översikt över Azure DevOps Services-autentisering](/vsts/git/auth-overview?view=vsts).
   
1. När distributionen är färdig visar du Bygg förloppet vid `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` och distributions förloppet på `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` .
   
1. Bläddra till din app i Azure Portal för att kontrol lera att innehållet har distribuerats.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Felsöka distribution

Följande vanliga fel meddelanden kan visas när du använder Git för att publicera till en App Service-app i Azure:

|Meddelande|Orsak|Lösning
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Appen är inte igång.|Starta appen i Azure Portal. Git-distribution är inte tillgängligt när webbappen har stoppats.|
|`Couldn't resolve host 'hostname'`|Adress informationen för fjärran slutet av Azure är felaktig.|Använd `git remote -v` kommandot för att lista alla fjärranslutna, tillsammans med tillhör ande URL. Kontrol lera att URL: en för "Azure"-fjärrplatsen är korrekt. Om det behövs tar du bort och återskapar denna fjärr anslutning med rätt URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Du har inte angett någon gren under `git push` eller så har du inte angett `push.default` värdet i `.gitconfig` .|Kör `git push` igen och ange huvud grenen: `git push azure master` .|
|`src refspec [branchname] does not match any.`|Du försökte skicka till en annan gren än main på Azure-fjärrplatsen.|Kör `git push` igen och ange huvud grenen: `git push azure master` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Det här felet kan inträffa om du försöker skicka en stor git-lagringsplats via HTTPS.|Ändra git-konfigurationen på den lokala datorn så att den blir `postBuffer` större. Till exempel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Du har distribuerat en Node.js-app med en _package.jspå_ en fil som anger ytterligare nödvändiga moduler.|Granska `npm ERR!` fel meddelandena före det här felet för mer information om felet. Följande är kända orsaker till det här felet och motsvarande `npm ERR!` meddelanden:<br /><br />**Felaktig package.jspå fil**: `npm ERR! Couldn't read dependencies.`<br /><br />**Den ursprungliga modulen har ingen binär distribution för Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />eller <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ytterligare resurser

- [Dokumentation om Project kudu](https://github.com/projectkudu/kudu/wiki)
- [Kontinuerlig distribution till Azure App Service](deploy-continuous-deployment.md)
- [Exempel: skapa en webbapp och distribuera kod från en lokal git-lagringsplats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exempel: skapa en webbapp och distribuera kod från en lokal git-lagringsplats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
