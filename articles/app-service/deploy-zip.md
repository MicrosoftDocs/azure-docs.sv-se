---
title: Distribuera kod med en ZIP-eller WAR-fil
description: Lär dig hur du distribuerar din app för att Azure App Service med en ZIP-fil (eller en WAR-fil för Java-utvecklare).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007916"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Distribuera din app till Azure App Service med en ZIP-eller WAR-fil

Den här artikeln visar hur du använder en ZIP-fil eller WAR-fil för att distribuera din webbapp till [Azure App Service](overview.md). 

Den här ZIP-fildistributionen använder samma kudu-tjänst som driver kontinuerlig integrerings-baserade distributioner. Kudu stöder följande funktioner för ZIP-fil distribution: 

- Borttagning av filer som är kvar från en tidigare distribution.
- Alternativ för att aktivera standard Bygg processen, vilket inkluderar paket återställning.
- Distributions anpassning, inklusive körning av distributions skript.  
- Distributions loggar. 
- En fil storleks gräns på 2048 MB.

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

WAR-fildistributionen distribuerar [War](https://wikipedia.org/wiki/WAR_(file_format)) -filen till App Service för att köra Java-webbappen. Se [distribuera War-fil](#deploy-war-file).

> [!NOTE]
> När du använder så `ZipDeploy` kopieras filerna bara om deras tidsstämplar inte matchar vad som redan har distribuerats. Att generera ett zip med en Bygg process som cachelagrar utdata kan leda till snabbare distributioner. Mer information finns i [distribuera från en zip-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln [skapar du en app service app](./index.yml)eller använder en app som du har skapat för en annan själv studie kurs.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Ovanstående slut punkt fungerar inte för Linux-App Services för tillfället. Överväg att använda FTP eller [zip Deploy-API](faq-app-service-linux.md#continuous-integration-and-deployment) i stället.

## <a name="deploy-zip-file-with-azure-cli"></a>Distribuera ZIP-fil med Azure CLI

Distribuera den uppladdade ZIP-filen till din webbapp med hjälp av [AZ webapp Deployment source config-zip-](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) kommandot.  

I följande exempel distribueras ZIP-filen som du laddade upp. När du använder en lokal installation av Azure CLI anger du sökvägen till den lokala ZIP-filen för `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen.

Som standard förutsätter distributions motorn att en ZIP-fil är klar att köras i befintligt skick och inte kör någon build-automatisering. Om du vill aktivera samma build-automatisering som i en [Git-distribution](deploy-local-git.md)anger du `SCM_DO_BUILD_DURING_DEPLOYMENT` appens inställning genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Mer information finns i [Kudu-dokumentationen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Distribuera WAR-fil

Om du vill distribuera en WAR-fil till App Service skickar du en POST-begäran till `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Din POST-begäran måste innehålla .war-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering.

Använd alltid `/api/wardeploy` när du distribuerar War-filer. Detta API expanderar WAR-filen och placerar den på den delade fil enheten. användning av andra distributions-API: er kan resultera i inkonsekvent beteende. 

För den grundläggande HTTP-autentiseringen behöver du autentiseringsuppgifterna för App Service distribution. Information om hur du anger autentiseringsuppgifter för distributionen finns i [Ange och återställa autentiseringsuppgifter på användar nivå](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med sväng

I följande exempel används verktyget sväng för att distribuera en War-fil. Ersätt plats hållarna `<username>` , `<war-file-path>` och `<app-name>` . Skriv lösen ordet när du uppmanas till en sväng.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) för att ladda upp War-filen. Ersätt plats hållarna `<group-name>` , `<app-name>` och `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

Försök [att distribuera till Azure med git](deploy-local-git.md)för mer avancerade distributions scenarier. Git-baserad distribution till Azure möjliggör versions kontroll, paket återställning, MSBuild med mera.

## <a name="more-resources"></a>Fler resurser

* [Kudu: Distribuera från en zip-fil](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Autentiseringsuppgifter för Azure App Service distribution](deploy-ftp.md)
