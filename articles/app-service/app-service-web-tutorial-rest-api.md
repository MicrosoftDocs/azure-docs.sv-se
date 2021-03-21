---
title: 'Självstudie: värd-RESTful-API med CORS'
description: 'Lär dig hur Azure App Service hjälper dig att vara värd för dina RESTful-API:er med CORS-stöd. App Service kan vara värd för både frontend-webbappar och Server dels-API: er.'
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 9481b6d2740d27b8c3d1309e205edda6017868fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005765"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Självstudie: Vara värd för en RESTful-API med CORS i Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. Dessutom har App Service ett inbyggt stöd för [CORS (Cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) för RESTful-API:er. Den här självstudien visar hur du distribuerar en ASP.NET Core API-app till App Service med CORS-stöd. Du konfigurerar appen med hjälp av kommandoradsverktyg och distribuerar appen med Git. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa App Service-resurser med Azure CLI
> * Distribuera en RESTful-API till Azure med Git
> * Aktivera stöd för CORS i App Service

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* <a href="https://git-scm.com/" target="_blank">Installera Git</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .NET Core 3,1 SDK</a>

## <a name="create-local-aspnet-core-app"></a>Skapa en lokal ASP.NET Core-app

I det här steget konfigurerar du det lokala ASP.NET Core-projektet. App Service stöder samma arbetsflöde för API:er som skrivits på andra datorspråk.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.  

Klona exempellagringsplatsen med följande kommando. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Den här lagringsplatsen innehåller ett program som baseras på följande självstudie: [Hjälpsidor för ASP.NET Cores webb-API med Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). En Swagger-generator används för att hantera [Swagger-användargränssnittet](https://swagger.io/swagger-ui/) och Swagger JSON-slutpunkten.

### <a name="run-the-application"></a>Kör programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Gå till `http://localhost:5000/swagger` i en webbläsare för att testa användargränssnittet för Swagger.

![ASP.NET Core-API som körs lokalt](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Gå till `http://localhost:5000/api/todo` och se en lista med ToDo JSON-objekt.

Gå till `http://localhost:5000` och testa med webbläsarappen. Senare kommer du att peka med webbläsarappen på en fjärr-API i App Service för att testa CORS-funktionerna. Koden för webbläsarappen finns i lagringsplatsens katalog _wwwroot_.

Du kan när som helst stoppa ASP.NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Gå till `http://<app_name>.azurewebsites.net/swagger` i en webbläsare och testa användargränssnittet för Swagger.

![ASP.NET Core-API som körs i Azure App Service](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Gå till `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` för att se _swagger.json_ för din distribuerade API.

Gå till `http://<app_name>.azurewebsites.net/api/todo` för att se om din distribuerade API fungerar.

## <a name="add-cors-functionality"></a>Lägga till CORS-funktioner

Därefter aktiverar du det inbyggda CORS-stödet i App Service för din API.

### <a name="test-cors-in-sample-app"></a>Testa CORS i exempelappen

Öppna _wwwroot/index.html_ på din lokala lagringsplats.

På rad 51 anger du `apiEndpoint`-variabeln till URL:en för din distribuerade API (`http://<app_name>.azurewebsites.net`). Ersätt _\<appname>_ med namnet på appen i App Service.

Kör exempelappen igen i ditt lokala terminalfönster.

```bash
dotnet run
```

Gå till webbläsarappen på `http://localhost:5000`. Öppna fönstret utvecklarverktyg i webbläsaren ( `Ctrl` + `Shift` + `i` i Chrome för Windows) och granska fliken **konsol** . Du bör nu se fel meddelandet `No 'Access-Control-Allow-Origin' header is present on the requested resource` .

![CORS-fel i webbläsarklienten](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

På grund av domänmatchningsfel mellan webbläsarappen (`http://localhost:5000`) och fjärresursen (`http://<app_name>.azurewebsites.net`), samt det faktum att din API i App Service inte skickar `Access-Control-Allow-Origin`-huvudet, har din webbläsare förhindrat att innehåll från flera domäner blir inlästa i din webbläsarapp.

Webbläsarappen bör ha en offentlig URL i stället för en localhost-URL i produktionsmiljö, men sättet att aktivera CORS till en localhost-URL är detsamma som en offentlig URL.

### <a name="enable-cors"></a>Aktivera CORS 

I Cloud Shell aktiverar du CORS till din klients URL med hjälp av [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) kommandot. Ersätt _&lt; appens namn>_ plats hållaren.

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

Du kan ange fler än en klient-URL i `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`). Du kan också aktivera alla klient-URL:er med `"['*']"`.

> [!NOTE]
> Om din app kräver att autentiseringsuppgifter såsom cookies eller autentiseringstoken skickas kan webbläsaren kräva huvudet `ACCESS-CONTROL-ALLOW-CREDENTIALS` i svaret. Om du vill aktivera detta i App Service anger `properties.cors.supportCredentials` du `true` i CORS-konfigurationen. Detta kan inte aktive ras när `allowedOrigins` inkluderar `'*'` .

### <a name="test-cors-again"></a>Testa CORS igen

Uppdatera webbläsarappen i `http://localhost:5000`. Felmeddelandet i fönstret **Konsol** är nu borta och du kan se data från den distribuerade API:n samt interagera med den. Fjärr-API:n har nu stöd för CORS i webbläsarappen som körs lokalt. 

![CORS finns nu i webbläsarklienten](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Grattis! Du kör en API i Azure App Service med CORS-stöd.

## <a name="app-service-cors-vs-your-cors"></a>App Services CORS jämfört med din CORS

Du kan använda dina egna CORS-verktyg i stället för App Services CORS om du vill ha mer flexibilitet. Du kanske vill ange olika tillåtna ursprung för olika vägar eller metoder. Eftersom du med App Services CORS kan ange en uppsättning godkända ursprung för alla API-vägar och metoder, kan du använda din egna CORS-kod. Se hur ASP.NET Core gör detta i [Aktivera CORS (Cross-Origin Requests)](/aspnet/core/security/cors).

> [!NOTE]
> Försök inte att använda App Services CORS och din egen CORS-kod tillsammans. När de används tillsammans har App Services CORS företräde och din egen CORS-kod fyller ingen funktion.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Skapa App Service-resurser med Azure CLI
> * Distribuera en RESTful-API till Azure med Git
> * Aktivera stöd för CORS i App Service

Gå vidare till nästa självstudie för att lära dig att autentisera och auktorisera användare.

> [!div class="nextstepaction"]
> [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt](tutorial-auth-aad.md)
