---
title: Köra din app från ett ZIP-paket
description: Distribuera appens ZIP-paket med Atomicitet. Förbättra förutsägbarheten och tillförlitligheten för appens beteende under ZIP-distributions processen.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 036dd8c86251af2ed5553939e74a0d85fc303ecb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577554"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Kör din app i Azure App Service direkt från ett ZIP-paket

I [Azure App Service](overview.md)kan du köra dina appar direkt från en distributions paket fil. Den här artikeln visar hur du aktiverar den här funktionen i din app.

Alla andra distributions metoder i App Service har något gemensamt: dina filer distribueras till *katalogen d:\home\site\wwwroot* i din app (eller */Home/site/wwwroot* för Linux-appar). Eftersom samma katalog används av din app vid körning, är det möjligt att distributionen inte kan utföras på grund av fil låsnings konflikter och att appen kan fungera oförutsägbart på grund av att några av filerna ännu inte har uppdaterats.

Om du däremot kör direkt från ett paket kopieras filerna i paketet inte till katalogen *wwwroot* . I stället monteras själva ZIP-paketet direkt som den skrivskyddade *wwwroot* -katalogen. Det finns flera fördelar med att köra direkt från ett paket:

- Eliminerar fil låsnings konflikter mellan distribution och körning.
- Säkerställer att endast distribuerade appar körs när som helst.
- Kan distribueras till en produktions-app (med omstart).
- Förbättrar prestandan för Azure Resource Manager distributioner.
- Kan minska kall start tider, särskilt för JavaScript-funktioner med stora NPM paket träd.

> [!NOTE]
> För närvarande stöds endast ZIP-paketfiler.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Aktivera körning från paket

Med `WEBSITE_RUN_FROM_PACKAGE` appens inställning kan du köra från ett paket. Ange det genom att köra följande kommando med Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` gör att du kan köra din app från ett paket lokalt till din app. Du kan också [köra från ett fjärrpaket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Kör paketet

Det enklaste sättet att köra ett paket i App Service är med Azure CLI [-distributions källans konfigurations-zip-](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) kommando. Exempel:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Eftersom `WEBSITE_RUN_FROM_PACKAGE` appens inställning är inställd extraherar det här kommandot inte paket innehållet till *katalogen d:\home\site\wwwroot* -katalogen för din app. I stället överförs ZIP-filen som-är till *D:\home\data\SitePackages* och skapar en *packagename.txt* i samma katalog som innehåller namnet på zip-paketet som ska läsas in vid körning. Om du överför ZIP-paketet på ett annat sätt (t. ex. [FTP](deploy-ftp.md)) måste du skapa *D:\home\data\SitePackages* -katalogen och *packagename.txt* -filen manuellt.

Kommandot startar också om appen. Eftersom `WEBSITE_RUN_FROM_PACKAGE` har angetts monterar App Service det överförda paketet som den skrivskyddade *wwwroot* -katalogen och kör appen direkt från den monterade katalogen.

## <a name="run-from-external-url-instead"></a>Kör från extern URL i stället

Du kan också köra ett paket från en extern URL, till exempel Azure Blob Storage. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler till ditt Blob Storage-konto. Du bör använda en privat lagrings behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att göra det möjligt för App Service runtime att komma åt paketet på ett säkert sätt. 

När du har överfört filen till Blob Storage och har en SAS-URL för filen anger du `WEBSITE_RUN_FROM_PACKAGE` appens inställning till URL: en. I följande exempel används Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Om du publicerar ett uppdaterat paket med samma namn i Blob Storage måste du starta om appen så att det uppdaterade paketet läses in i App Service.

## <a name="troubleshooting"></a>Felsökning

- Att köra direkt från ett paket är `wwwroot` skrivskyddat. Din app får ett fel meddelande om det försöker skriva filer till den här katalogen.
- TAR-och GZIP-format stöds inte.
- ZIP-filen kan vara högst 1 GB
- Den här funktionen är inte kompatibel med [lokal cache](overview-local-cache.md).
- För förbättrade kall start prestanda använder du det lokala zip-alternativet ( `WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="more-resources"></a>Fler resurser

- [Kontinuerlig distribution för Azure App Service](deploy-continuous-deployment.md)
- [Distribuera kod med en ZIP-eller WAR-fil](deploy-zip.md)
