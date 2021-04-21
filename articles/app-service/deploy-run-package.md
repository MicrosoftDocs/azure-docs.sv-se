---
title: Köra appen från ett ZIP-paket
description: Distribuera appens ZIP-paket med atomicitet. Förbättra förutsägbarheten och tillförlitligheten för appens beteende under ZIP-distributionsprocessen.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772747"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Kör din app i Azure App Service direkt från ett ZIP-paket

I [Azure App Service](overview.md)kan du köra dina appar direkt från en ZIP-paketfil för distribution. Den här artikeln visar hur du aktiverar den här funktionen i din app.

Alla andra distributionsmetoder i App Service har något gemensamt: filerna distribueras till *D:\home\site\wwwroot* i din app (eller */home/site/wwwroot* för Linux-appar). Eftersom samma katalog används av din app vid körning är det möjligt att distributionen misslyckas på grund av fillåskonflikter och att appen beter sig oförutsägbart eftersom vissa av filerna ännu inte har uppdaterats.

När du däremot kör direkt från ett paket kopieras inte filerna i paketet till *katalogen wwwroot.* I stället monteras själva ZIP-paketet direkt som den skrivskyddade *wwwroot-katalogen.* Det finns flera fördelar med att köra direkt från ett paket:

- Eliminerar fillåskonflikter mellan distribution och körning.
- Säkerställer att endast fullständigt distribuerade appar körs när som helst.
- Kan distribueras till en produktionsapp (med omstart).
- Förbättrar prestanda för Azure Resource Manager distributioner.
- Kan minska kallstartstiderna, särskilt för JavaScript-funktioner med stora npm-paketträd.

> [!NOTE]
> För närvarande stöds endast ZIP-paketfiler.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Aktivera körning från paket

Med `WEBSITE_RUN_FROM_PACKAGE` appinställningen kan du köra från ett paket. Ange det genom att köra följande kommando med Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` låter dig köra din app från ett paket lokalt till din app. Du kan också [köra från ett fjärrpaket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Kör paketet

Det enklaste sättet att köra ett paket i App Service är med kommandot Azure CLI [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) Exempel:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Eftersom appinställningen har angetts extraherar det här kommandot inte paketinnehållet till `WEBSITE_RUN_FROM_PACKAGE` *katalogen D:\home\site\wwwroot* för din app. I stället överför den ZIP-filen som den är till *D:\home\data\SitePackages* och skapar en *packagename.txt* i samma katalog som innehåller namnet på ZIP-paketet som ska läsas in vid körning. Om du överför ZIP-paketet på ett annat sätt (till exempel [FTP](deploy-ftp.md)) måste du skapa katalogen *D:\home\data\SitePackages* *ochpackagename.txt* manuellt.

Kommandot startar också om appen. Eftersom har angetts App Service det uppladdade paketet som den skrivskyddade wwwroot-katalogen och kör appen direkt `WEBSITE_RUN_FROM_PACKAGE` från den monterade katalogen. 

## <a name="run-from-external-url-instead"></a>Kör från extern URL i stället

Du kan också köra ett paket från en extern URL, till exempel Azure Blob Storage. Du kan använda Azure Storage Explorer [för att](../vs-azure-tools-storage-manage-with-storage-explorer.md) ladda upp paketfiler till ditt Blob Storage-konto. Du bör använda en privat lagringscontainer med en signatur för delad åtkomst [(SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att aktivera App Service för säker åtkomst till paketet. 

När du har laddat upp filen till Blob Storage och har en SAS-URL för filen anger du `WEBSITE_RUN_FROM_PACKAGE` appinställningen till URL:en. I följande exempel används Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Om du publicerar ett uppdaterat paket med samma namn till Blob Storage måste du starta om appen så att det uppdaterade paketet läses in i App Service.

## <a name="troubleshooting"></a>Felsökning

- Om du kör direkt från ett paket `wwwroot` blir det skrivskyddade. Appen får ett felmeddelande om den försöker skriva filer till den här katalogen.
- TAR- och GZIP-format stöds inte.
- ZIP-filen kan vara högst 1 GB
- Den här funktionen är inte kompatibel med [lokal cache.](overview-local-cache.md)
- För bättre kallstartsprestanda använder du det lokala ZIP-alternativet ( `WEBSITE_RUN_FROM_PACKAGE` =1).

## <a name="more-resources"></a>Fler resurser

- [Kontinuerlig distribution för Azure App Service](deploy-continuous-deployment.md)
- [Distribuera kod med en ZIP- eller WAR-fil](deploy-zip.md)
