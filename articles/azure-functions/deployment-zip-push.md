---
title: Zip-push-distribution för Azure Functions
description: Använd .zip-fildistributionsresurser i Kudu-distributionstjänsten för att publicera Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: fb6867d7719f9650acb00f80ac3a933713ce0e23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777659"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-distribution för Azure Functions

I den här artikeln beskrivs hur du distribuerar funktionsappens projektfiler till Azure från en ZIP-fil (komprimerad). Du lär dig hur du gör en push-distribution, både med hjälp av Azure CLI och med hjälp av REST-API:er. [Azure Functions Core Tools](functions-run-local.md) använder även dessa distributions-API:er när du publicerar ett lokalt projekt till Azure.

Azure Functions har en fullständig mängd alternativ för kontinuerlig distribution och integrering som tillhandahålls av Azure App Service. Mer information finns i [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

För att påskynda utvecklingen kan det vara enklare att distribuera funktionsappens projektfiler direkt från en .zip-fil. API:et för .zip-distribution tar innehållet i en .zip-fil och extraherar innehållet till `wwwroot` mappen i funktionsappen. Den här ZIP-fildistributionen använder samma Kudu-tjänst som driver kontinuerliga integreringsbaserade distributioner, inklusive:

+ Borttagning av filer som har lämnats kvar från tidigare distributioner.
+ Distributionsanpassning, inklusive körning av distributionsskript.
+ Distributionsloggar.
+ Synkronisera funktionsutlösare i en [funktionsapp för](functions-scale.md) förbrukningsplan.

Mer information finns i [distributionsreferensen för .zip.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

## <a name="deployment-zip-file-requirements"></a>Krav för .zip-fil för distribution

ZIP-filen som du använder för push-distribution måste innehålla alla filer som behövs för att köra funktionen.

>[!IMPORTANT]
> När du använder .zip-distributionen tas alla filer från en befintlig distribution som inte finns i ZIP-filen bort från funktionsappen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En funktionsapp innehåller alla filer och mappar i `wwwroot` katalogen. En .zip-fildistribution innehåller innehållet i `wwwroot` katalogen, men inte själva katalogen. När du distribuerar ett C#-klassbiblioteksprojekt måste du inkludera de kompilerade biblioteksfilerna och beroendena i en `bin` undermapp i zip-paketet.

## <a name="download-your-function-app-files"></a>Ladda ned dina funktionsappfiler

När du utvecklar på en lokal dator är det enkelt att skapa en ZIP-fil i funktionsappens projektmapp på utvecklingsdatorn.

Du kan dock ha skapat dina funktioner med hjälp av redigeraren i Azure Portal. Du kan ladda ned ett befintligt funktionsappsprojekt på något av följande sätt:

+ **Från Azure Portal:**

  1. Logga in på [Azure Portal](https://portal.azure.com)och gå sedan till funktionsappen.

  2. På fliken **Översikt** väljer du Ladda **ned appinnehåll.** Välj dina nedladdningsalternativ och välj sedan **Ladda ned.**

      ![Ladda ned funktionsappsprojektet](./media/deployment-zip-push/download-project.png)

     Den nedladdade ZIP-filen har rätt format för att publiceras på nytt till funktionsappen med push-distribution med .zip. Du kan också ladda ned portalen och lägga till de filer som behövs för att öppna funktionsappen direkt Visual Studio.

+ **Använda REST-API:er:**

    Använd följande GET API för distribution för att ladda ned filerna från `<function_app>` projektet: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Genom `/site/wwwroot/` att inkludera ser du till att zip-filen endast innehåller funktionsappens projektfiler och inte hela webbplatsen. Om du inte redan har loggat in på Azure uppmanas du att göra det.  

Du kan också ladda ned en .zip-fil från en GitHub-lagringsplats. När du laddar ned en GitHub-lagringsplats som en ZIP-fil lägger GitHub till en extra mappnivå för -grenen. Den här extra mappnivån innebär att du inte kan distribuera ZIP-filen direkt när du laddade ned den från GitHub. Om du använder en GitHub-lagringsplats för att underhålla funktionsappen bör du använda [kontinuerlig integrering för](functions-continuous-deployment.md) att distribuera din app.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Push-distribuera en .zip-fil till funktionsappen med hjälp av [kommandot az functionapp deployment source config-zip.](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) Om du vill använda det här kommandot måste du använda Azure CLI version 2.0.21 eller senare. Om du vill se vilken Azure CLI-version du använder använder du `az --version` kommandot .

I följande kommando ersätter du `<zip_file_path>` platshållaren med sökvägen till platsen för ZIP-filen. Ersätt också `<app_name>` med funktionsappens unika namn och ersätt `<resource_group>` med namnet på din resursgrupp.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Det här kommandot distribuerar projektfiler från den nedladdade ZIP-filen till funktionsappen i Azure. Den startar sedan om appen. Om du vill visa listan över distributioner för den här funktionsappen måste du använda REST-API:erna.

När du använder Azure CLI på den lokala datorn är `<zip_file_path>` sökvägen till ZIP-filen på datorn. Du kan också köra Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md). När du använder Cloud Shell måste du först ladda upp zip-filen för distributionen till det Azure Files-konto som är associerat med Cloud Shell. I så fall är `<zip_file_path>` den lagringsplats som ditt Cloud Shell använder. Mer information finns i [Spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Köra funktioner från distributionspaketet

Du kan också välja att köra dina funktioner direkt från distributionspaketets fil. Den här metoden hoppar över distributionssteget för att kopiera filer från paketet till `wwwroot` katalogen i funktionsappen. Paketfilen monteras i stället av Functions-körningen och innehållet i `wwwroot` katalogen blir skrivskyddad.  

Zip-distributionen integreras med den här funktionen, vilket du kan aktivera genom att ange `WEBSITE_RUN_FROM_PACKAGE` funktionsappinställningen till värdet `1` . Mer information finns i Köra [dina funktioner från en distributionspaketfil](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
