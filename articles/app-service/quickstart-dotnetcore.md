---
title: 'Snabb start: skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör Web Apps i Azure App Service genom att distribuera din första ASP.NET Core-app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperfq1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 7aeadaf7b52440504abf541fe6c29a537a92bc08
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937278"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabb start: skapa en ASP.NET Core webbapp i Azure

::: zone pivot="platform-windows"  

I den här snabb starten får du lära dig hur du skapar och distribuerar din första ASP.NET Core-webbapp till [Azure App Service](overview.md). App Service stöder .NET 5,0-appar.

När du är klar har du en Azure-resurs grupp som består av en App Service värd plan och en App Service med ett distribuerat webb program.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

  Om du redan har installerat Visual Studio 2019:

  - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp**  >  **sökning efter uppdateringar**. De senaste uppdateringarna innehåller .NET 5,0 SDK.
  - Lägg till arbets belastningen genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.


## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa en ASP.NET Core webbapp i Visual Studio genom att följa dessa steg:

# <a name="net-core-31"></a>[.NET Core 3,1](#tab/netcore31)

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** väljer du **ASP.net Core webb program** och bekräftar att **C#** visas på de språk som du väljer. Välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du ditt webb program projekt *MyFirstAzureWebApp* och väljer **skapa**.

   ![Konfigurera ditt webbapp](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Du kan distribuera vilken typ av ASP.NET Core webbapp som helst till Azure, men i den här snabb starten väljer du mallen för **webb program** . Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering** och att inget annat alternativ har valts. Välj sedan **Skapa**.

   ![Skapa en ny ASP.NET Core webbapp](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra din webbapp lokalt.

   ![Webbapp som körs lokalt](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** väljer du **ASP.net Core webb program** och bekräftar att **C#** visas på de språk som du väljer. Välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du ditt webb program projekt *MyFirstAzureWebApp* och väljer **skapa**.

   ![Konfigurera ditt webbapp](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. För en .NET 5,0-app väljer du **ASP.NET Core 5,0** i list rutan.

1. Du kan distribuera vilken typ av ASP.NET Core webbapp som helst till Azure, men i den här snabb starten väljer du mallen **ASP.net Core webbapp** . Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering** och att inget annat alternativ har valts. Välj sedan **Skapa**.

   ![Skapa en ny ASP.NET Core webbapp](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra din webbapp lokalt.

   ![Webbapp som körs lokalt](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera din webbapp måste du först skapa och konfigurera en ny App Service som du kan publicera din app till. 

Som en del av att konfigurera App Service skapar du:

- En ny [resurs grupp](../azure-resource-manager/management/overview.md#terminology) som innehåller alla Azure-resurser för tjänsten.
- En ny [värd plan](./overview-hosting-plans.md) som anger plats, storlek och funktioner för den webb Server grupp som är värd för din app.

Följ de här stegen för att skapa din App Service och publicera din webbapp:

1. I **Solution Explorer** högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**. 

1. I **publicera** väljer du **Azure** och klickar på **Nästa**.

1. Vilka alternativ som är tillgängliga beror på om du redan är inloggad på Azure och om du har ett Visual Studio-konto som är länkat till ett Azure-konto. Välj antingen **Lägg till ett konto** eller **Logga** in för att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

   ![Logga in på Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Till höger om **App Service-instanser** klickar du på **+** .

   ![Ny App Service-app](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. För **resurs grupp** väljer du **nytt**. I **nytt resurs grupp namn** anger du *MyResourceGroup* och väljer **OK**. 

1. För **värd plan** väljer du **nytt**. 

1. Ange de värden som anges i följande tabell i dialog rutan **värd plan: skapa ny** .

   | Inställning  | Föreslaget värde | Beskrivning |
   | -------- | --------------- | ----------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* | Namnet på App Service-planen. |
   | **Plats**      | *Europa, västra* | Datacenter som är värd för webbappen. |
   | **Storlek**          | *Kostnadsfri* | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |
   
   ![Skapa ny värd plan](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. I **namn** anger du ett unikt namn för appen som bara innehåller giltiga tecken är `a-z` , `A-Z` , `0-9` och `-` . Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn.

2. Välj **skapa** för att skapa Azure-resurserna.

   ![Skapa app-resurser](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   När guiden har slutförts skapas Azure-resurserna åt dig och du är redo att publicera.

3. Stäng guiden genom att klicka på **Slutför** .

1. På sidan **publicera** klickar du på **publicera**. Visual Studio skapar, paketerar och publicerar appen till Azure och startar sedan appen i standard webbläsaren.

   ![Publicerad ASP.NET-webbapp som körs i Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Grattis!** Din ASP.NET Core-webbapp körs Live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Följ dessa steg för att uppdatera och distribuera om din webbapp:

1. Öppna  **Pages**  >  **index. cshtml** under ditt projekt i Solution Explorer.

1. Ersätt hela `<div>` taggen med följande kod:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **publicera** Sammanfattning väljer du **publicera**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

    ![Uppdaterade ASP.NET-webbappen som körs i Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera din webbapp går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

![Välj App Services](./media/quickstart-dotnetcore/app-services.png)

På sidan **app Services** väljer du namnet på din webbapp.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Skärm bild av App Services sidan med ett exempel på en webbapp vald.":::

**Översikts** sidan för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller fler sidor för att konfigurera din app.

![App Service i Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Visual Studio för att skapa och distribuera en ASP.NET Core-webbapp till Azure App Service.

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Med [App Service i Linux](overview.md#app-service-on-linux) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här snabbstarten visar hur du skapar en [.NET Core](/aspnet/core/)-app med App Service på Linux. Du skapar appen med [Azure CLI](/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera .NET Core-koden till appen.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Du kan följa stegen i den här artikeln på en Mac-, Windows- eller Linux-dator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

# <a name="net-core-31"></a>[.NET Core 3,1](#tab/netcore31)

För att slutföra den här snabbstarten behöver du:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .net Core 3,1 SDK: n</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Installera den senaste versionen av Azure CLI</a>.

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

För att slutföra den här snabbstarten behöver du:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Installera den senaste .net 5,0 SDK: n</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Installera den senaste versionen av Azure CLI</a>.

---

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Skapa appen lokalt

I ett terminalfönster på datorn skapar du en katalog med namnet `hellodotnetcore` och ändrar katalogen till den.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Skapa en ny .NET Core-app.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. 

```bash
dotnet run
```

Öppna webbläsaren och navigera till appen på `http://localhost:5000`.

Nu kan du se **Hello World**-meddelandet från exempelappen på sidan.

![Testa med webbläsare](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Logga in på Azure
Logga in på Azure med följande kommando i terminalfönstret:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Distribuera appen

Distribuera koden i din lokala mapp (*hellodotnetcore*) med hjälp av `az webapp up` kommandot:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type linux
```

- Om `az` kommandot inte känns igen kontrollerar du att Azure CLI är installerat enligt beskrivningen i [Konfigurera din första miljö](#set-up-your-initial-environment).
- Ersätt `<app-name>` med ett namn som är unikt för alla Azure (*giltiga tecken är `a-z` , `0-9` och `-`*). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
- `--sku F1`Argumentet skapar webb programmet på den kostnads fria pris nivån. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå, vilket innebär en timkostnad.
- Du kan också inkludera argumentet `--location <location-name>` där `<location-name>` är en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) kommandot.

Det kan ta några minuter att slutföra kommandot. Under körningen innehåller den meddelanden om att skapa resurs gruppen, App Service plan och värd appen, Konfigurera loggning och sedan utföra ZIP-distribution. Det ger meddelandet "du kan starta appen på http:// &lt; App-Name &gt; . azurewebsites.net", som är appens URL på Azure.

# <a name="net-core-31"></a>[.NET Core 3,1](#tab/netcore31)

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen via webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

.NET Core-exempelkoden körs i App Service på Linux med en inbyggd avbildning.

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Grattis!** Nu har du distribuerat din första .NET Core-app till App Service i Linux.

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>Uppdatera och distribuera om koden

Öppna filen _Startup.cs_ i den lokala katalogen. Göra en mindre ändring i texten i metodanropet `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Spara ändringarna och distribuera sedan om appen med `az webapp up` kommandot igen:

```azurecli
az webapp up --os-type linux
```

Det här kommandot använder värden som cachelagras lokalt i *Azure/config* -filen, inklusive app-namn, resurs grupp och App Service plan.

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades i **Bläddra till app** -steget och trycker på Uppdatera.

![Uppdaterad exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Hantera din nya Azure-app

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade.

I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Skärm bild av sidan App Services som visar att ett exempel på en Azure-app har valts.":::

Nu visas översiktssidan för din app. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-sidan på Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end
