---
title: Zip push-distribution för Azure Functions
description: Använd zip-fil distributions funktionerna i kudu-distributions tjänsten för att publicera din Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018468"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-distribution för Azure Functions

Den här artikeln beskriver hur du distribuerar dina Apps-projektfiler till Azure från en. zip-fil (komprimerad). Du får lära dig hur du utför en push-distribution med hjälp av Azure CLI och med hjälp av REST-API: er. [Azure Functions Core tools](functions-run-local.md) använder också dessa API: er för distribution när du publicerar ett lokalt projekt till Azure.

Azure Functions har en fullständig uppsättning alternativ för kontinuerlig distribution och integrering som tillhandahålls av Azure App Service. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

För att påskynda utvecklingen kan det vara lättare att distribuera dina Apps-projektfiler direkt från en. zip-fil. API: et för zip-distribution tar innehållet i en. zip-fil och extraherar innehållet till `wwwroot` mappen i din Function-app. Den här. zip-fildistributionen använder samma kudu-tjänst som driver kontinuerlig integrerings-baserade distributioner, inklusive:

+ Borttagning av filer som lämnades över från tidigare distributioner.
+ Distributions anpassning, inklusive körning av distributions skript.
+ Distributions loggar.
+ Synkronisera funktions utlösare i en [förbruknings Plans](functions-scale.md) funktion app.

Mer information finns i referens för [zip-distribution](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Krav för distribution. zip-fil

Zip-filen som du använder för push-distribution måste innehålla alla filer som behövs för att köra din funktion.

>[!IMPORTANT]
> När du använder. zip-distribution tas alla filer från en befintlig distribution som inte finns i. zip-filen bort från din Function-app.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En Function-app innehåller alla filer och mappar i `wwwroot` katalogen. En. zip-fil distribution innehåller `wwwroot` katalogens innehåll, men inte själva katalogen. När du distribuerar ett C#-klass biblioteks projekt måste du inkludera de kompilerade biblioteksfilerna och beroendena i en `bin` undermapp i. zip-paketet.

## <a name="download-your-function-app-files"></a>Hämta dina app-filer för funktioner

När du utvecklar på en lokal dator är det enkelt att skapa en. zip-fil för mappen Function app-projekt på din utvecklings dator.

Du kan dock ha skapat dina funktioner med hjälp av redigeraren i Azure Portal. Du kan ladda ned ett befintligt Function app-projekt på något av följande sätt:

+ **Från Azure Portal:**

  1. Logga in på [Azure Portal](https://portal.azure.com)och gå sedan till din Function-app.

  2. På fliken **Översikt** väljer du **Hämta app-innehåll**. Välj nedladdnings alternativ och välj sedan **Ladda ned**.

      ![Hämta projektet för Function-appen](./media/deployment-zip-push/download-project.png)

     Den hämtade ZIP-filen har rätt format för att publiceras om till din Function-app med hjälp av. zip push-distribution. Portal hämtningen kan också lägga till de filer som behövs för att öppna appen funktion direkt i Visual Studio.

+ **Använda REST-API: er:**

    Använd följande distribution Hämta API för att ladda ned filerna från `<function_app>` projektet: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Inklusive kontrollerar `/site/wwwroot/` att zip-filen bara innehåller programmets projektfiler och inte hela webbplatsen. Om du inte redan har loggat in på Azure blir du ombedd att göra det.  

Du kan också hämta en. zip-fil från en GitHub-lagringsplats. När du laddar ned en GitHub-lagringsplats som en zip-fil lägger GitHub till en extra mappsökväg för grenen. Den här extra mappen innebär att du inte kan distribuera. zip-filen direkt när du laddade ned den från GitHub. Om du använder en GitHub-lagringsplats för att underhålla din Function-app bör du använda [kontinuerlig integrering](functions-continuous-deployment.md) för att distribuera din app.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Skicka en. zip-fil till din Function-app genom att använda kommandot [AZ functionapp Deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) . Om du vill använda det här kommandot måste du använda Azure CLI version 2.0.21 eller senare. Använd kommandot för att se vilken Azure CLI-version du använder `az --version` .

I följande kommando ersätter du `<zip_file_path>` plats hållaren med sökvägen till platsen för. zip-filen. Ersätt också `<app_name>` med det unika namnet på din Function-app och Ersätt `<resource_group>` med namnet på din resurs grupp.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Det här kommandot distribuerar projektfiler från den hämtade ZIP-filen till din Function-app i Azure. Appen startas sedan om. Om du vill visa listan över distributioner för den här Function-appen måste du använda REST-API: erna.

När du använder Azure CLI på den lokala datorn `<zip_file_path>` är sökvägen till. zip-filen på din dator. Du kan också köra Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md). När du använder Cloud Shell måste du först överföra Deployment. zip-filen till det Azure Files konto som är associerat med din Cloud Shell. I så fall `<zip_file_path>` är den lagrings plats som ditt Cloud Shells konto använder. Mer information finns i [Spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Köra funktioner från distributions paketet

Du kan också välja att köra dina funktioner direkt från distributions paket filen. Den här metoden hoppar över distributions steget för att kopiera filer från paketet till `wwwroot` katalogen i din Function-app. I stället monteras paket filen av Functions-körningen och `wwwroot` katalogens innehåll blir skrivskyddat.  

Zip-distributionen integreras med den här funktionen, som du kan aktivera genom att ställa in funktionen app `WEBSITE_RUN_FROM_PACKAGE` på värdet `1` . Mer information finns i [köra funktioner från en distributions paket fil](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
