---
title: Distribuera kod med en ZIP- eller WAR-fil
description: Lär dig hur du distribuerar din app Azure App Service med en ZIP-fil (eller en WAR-fil för Java-utvecklare).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f59576ea66b72a492e1e6c665a51258861842dd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833030"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service en ZIP- eller WAR-fil

Den här artikeln visar hur du använder en ZIP-fil eller WAR-fil för att distribuera din webbapp till [Azure App Service](overview.md). 

Den här ZIP-fildistributionen använder samma Kudu-tjänst som driver kontinuerliga integreringsbaserade distributioner. Kudu stöder följande funktioner för distribution av ZIP-filer: 

- Borttagning av filer som är kvar från en tidigare distribution.
- Alternativ för att aktivera standardbyggprocessen, vilket innefattar paketåterställning.
- Distributionsanpassning, inklusive körning av distributionsskript.  
- Distributionsloggar. 
- En filstorleksgräns på 2 048 MB.

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR-fildistributionen distribuerar [WAR-filen](https://wikipedia.org/wiki/WAR_(file_format)) till App Service för att köra Java-webbappen. Se [Distribuera WAR-filen](#deploy-war-file).

> [!NOTE]
> När du `ZipDeploy` använder kopieras bara filer om deras tidsstämplar inte matchar det som redan har distribuerats. Att generera en zip-fil med hjälp av en byggprocess som cachelagrar utdata kan resultera i snabbare distributioner. Mer information [finns i Distribuera från en ZIP-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)eller URL.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i den här artikeln genom att [skapa en App Service eller använda](./index.yml)en app som du har skapat för en annan självstudie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Slutpunkten ovan fungerar inte för Linux-App Services just nu. Överväg att använda FTP eller [ZIP-distributions-API:et](faq-app-service-linux.md#continuous-integration-and-deployment) i stället.

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-fil med Azure CLI

Distribuera den uppladdade ZIP-filen till webbappen med kommandot [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip)  

I följande exempel distribueras ZIP-filen som du laddade upp. När du använder en lokal installation av Azure CLI anger du sökvägen till din lokala ZIP-fil för `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen.

Som standard förutsätter distributionsmotorn att en ZIP-fil är redo att köras som den är och inte kör någon byggautomatisering. Om du vill aktivera samma byggautomatisering som i en [Git-distribution](deploy-local-git.md)anger du appinställningen genom att `SCM_DO_BUILD_DURING_DEPLOYMENT` köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-fil

Om du vill distribuera en WAR-App Service skickar du en POST-begäran till `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering.

Använd alltid `/api/wardeploy` när du distribuerar WAR-filer. Det här API:et expanderar WAR-filen och placerar den på den delade filenheten. användning av andra distributions-API:er kan resultera i inkonsekvent beteende. 

För HTTP BASIC-autentisering behöver du dina autentiseringsuppgifter App Service distributionen. Information om hur du anger autentiseringsuppgifter för distribution finns i [Ange och återställa autentiseringsuppgifter på användarnivå.](deploy-configure-credentials.md#userscope)

### <a name="with-curl"></a>Med cURL

I följande exempel används verktyget cURL för att distribuera en WAR-fil. Ersätt platshållarna `<username>` `<war-file-path>` , och `<app-name>` . När du uppmanas av cURL skriver du lösenordet.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publish-AzWebapp för](/powershell/module/az.websites/publish-azwebapp) att ladda upp WAR-filen. Ersätt platshållarna `<group-name>` `<app-name>` , och `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer avancerade distributionsscenarier [kan du prova att distribuera till Azure med Git](deploy-local-git.md). Git-baserad distribution till Azure möjliggör versionskontroll, paketåterställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en ZIP-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service autentiseringsuppgifter för distribution](deploy-ftp.md)
