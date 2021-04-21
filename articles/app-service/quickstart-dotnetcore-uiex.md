---
title: 'Snabbstart: Skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera din första ASP.NET core-app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769111"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabbstart: Skapa en ASP.NET Core-webbapp i Azure

::: zone pivot="platform-windows"  

I den här snabbstarten lär du dig att skapa och distribuera din första ASP.NET Core-webbapp till <abbr title="En HTTP-baserad tjänst som är värd för webbprogram, REST API:er och mobila serverdelsprogram.">Azure App Service</abbr>. App Service stöder .NET 5.0-appar.

När du är klar har du en Azure <abbr title="En logisk container för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>, som består av en <abbr title="Planen som anger plats, storlek och funktioner för webbservergruppen som är värd för din app.">App Service-plan</abbr> och en <abbr title="Representationen av din webbapp, som innehåller din appkod, DNS-värdnamn, certifikat och relaterade resurser.">App Service-app</abbr> med ett distribuerat ASP.NET Core-program.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

- **Skaffa ett Azure-konto** med en aktiv <abbr title="Den grundläggande organisationsstrukturen där du hanterar resurser i Azure, vanligtvis associerad med en person eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet/)
- **Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 med</a>** arbetsbelastningen ASP.NET och **webbutveckling.**

<details>
<summary>Har du redan Visual Studio 2019?</summary>
Om du redan har installerat Visual Studio 2019:

<ul>
<li><strong>Installera de senaste uppdateringarna i</strong> Visual Studio genom att välja Hjälp <strong>sök</strong> &gt; <strong>efter uppdateringar.</strong> De senaste uppdateringarna innehåller .NET 5.0 SDK.</li>
<li><strong>Lägg till arbetsbelastningen</strong> genom <strong>att välja Verktyg</strong> Hämta verktyg och &gt; <strong>funktioner.</strong></li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Skapa en ASP.NET Core-webbapp

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.

1. I **Skapa ett nytt projekt** väljer du ASP.NET Core Web **Application** och bekräftar att **C#** visas på språken för det valet och väljer **sedan Nästa.**

1. I **Konfigurera det nya projektet ger** du webbappsprojektet namnet *myFirstAzureWebApp* och väljer **Skapa**.

   ![Konfigurera webbappsprojektet](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. För en .NET 5.0-app **väljer ASP.NET Core 5.0** i listrutan. Annars använder du standardvärdet.

1. Du kan distribuera valfri typ av ASP.NET Core-webbapp till Azure, men för den här snabbstarten väljer **du mallen ASP.NET Core Web App.** Kontrollera att **Autentisering** är inställt **på Ingen** autentisering och att inget annat alternativ har valts. Välj sedan **Skapa**.

   ![Skapa en ny ASP.NET Core-webbapp](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. På Visual Studio väljer du **Felsök**  >  **Starta utan felsökning för att** köra webbappen lokalt.

   ![Webbapp som körs lokalt](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Publicera din webbapp

1. I **Solution Explorer** högerklickar du på **projektet myFirstAzureWebApp** och väljer **Publicera.** 

1. I **Publicera** väljer du **Azure och** klickar på **Nästa.**

1. Alternativen beror på om du redan är inloggad på Azure och om du har ett Visual Studio till ett Azure-konto. Välj **antingen Lägg till ett konto** eller Logga in **för** att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

   ![Logga in på Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Till höger om App Service **instanser klickar** du på **+** .

   ![Ny App Service app](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. För **Prenumeration** godkänner du prenumerationen som visas eller väljer en ny i listrutan.

1. För **Resursgrupp** väljer du **Ny.** I **Namn på ny resursgrupp** anger du *myResourceGroup* och väljer **OK.** 

1. För **Värdplan** väljer du **Ny.** 

1. I dialogrutan **Värdplan: Skapa ny** anger du de värden som anges i följande tabell:

   | Inställning  | Föreslaget värde |
   | -------- | --------------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* |
   | **Plats**      | *Europa, västra* |
   | **Storlek**          | *Kostnadsfri* |
   
   ![Skapa en ny värdplan](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. I **Namn** anger du ett unikt appnamn.

    <details>
        <summary>Vilka tecken kan jag använda?</summary>
        Giltiga tecken är a–z, A–Z, 0–9 och -. Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , där är namnet på din <code>&lt;app-name&gt;</code> app.
    </details>

1. Välj **Skapa** för att skapa Azure-resurserna. 

   ![Skapa appresurser](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Vänta tills guiden har skapat Azure-resurser. Stäng **guiden** genom att välja Slutför.

1. På sidan **Publicera** klickar du på **Publicera för** att distribuera projektet. 

    <details>
        <summary>Vad gör Visual Studio?</summary>
        Visual Studio skapar, paketerar och publicerar appen till Azure och startar sedan appen i standardwebbläsaren.
    </details>

   ![Publicerad ASP.NET webbapp som körs i Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Uppdatera appen och distribuera om

1. I **Solution Explorer** du pages   >  **index.cshtml** under projektet.

1. Ersätt hela `<div>` taggen med följande kod:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

1. På sidan **Publicera** sammanfattning väljer du **Publicera.**

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

    ![Uppdaterad ASP.NET webbapp som körs i Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Hantera Azure-appen

1. Gå till [Azure Portal](https://portal.azure.com)och sök efter och välj **App Services**.

    ![Välj App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. På **App Services** väljer du namnet på webbappen.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Skärmbild av sidan App Services med en exempelwebbapp vald.":::

1. **Översiktssidan** för webbappen innehåller alternativ för grundläggande hantering, till exempel bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller ytterligare sidor för att konfigurera din app.

    ![App Service i Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Rensa resurser

1. På Azure Portal eller på **startsidan** väljer du **Resursgrupper.** På sidan **Resursgrupper väljer** du **sedan myResourceGroup.**

1. På sidan **myResourceGroup** kontrollerar du att resurserna i listan är de som du vill ta bort.

1. Välj **Ta bort resursgrupp,** **skriv myResourceGroup** i textrutan för att bekräfta och välj sedan Ta **bort.**

<hr/> 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

- [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurera ASP.NET Core-app](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Den här snabbstarten visar hur du skapar [en .NET Core-app](/aspnet/core/) på <abbr title="Med App Service i Linux får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux.">App Service på Linux</abbr>. Du skapar appen med [Azure CLI](/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera .NET Core-koden till appen.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

- **Skaffa ett Azure-konto** med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet/)
- **Installera** den senaste <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.NET Core 3.1 SDK</a> eller <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5.0 SDK.</a>
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installera den senaste versionen av Azure CLI.</a>**

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Skapa appen lokalt

1. Kör `mkdir hellodotnetcore` för att skapa katalogen.

    ```bash
    mkdir hellodotnetcore
    ```

1. Kör `cd hellodotnetcore` för att ändra katalogen. 

    ```bash
    cd hellodotnetcore
    ```

1. Kör `dotnet new web` för att skapa en ny .NET Core-app.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Kör appen lokalt

1. Kör `dotnet run` för att se hur det ser ut när du distribuerar det till Azure.

    ```bash
    dotnet run
    ```
    
1. **Öppna en webbläsare** och gå till appen på `http://localhost:5000` .

![Testa med webbläsare](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Logga in på Azure

Kör `az login` för att logga in på Azure.

```azurecli
az login
```

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. Distribuera appen

1. **Kör** `az webapp up` i din lokala mapp. **Ersätt** <appnamn med> globalt unikt namn.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Felsökning</summary>
    <ul>
    <li>Om kommandot inte känns igen kontrollerar du att du har Installerat Azure CLI enligt <code>az</code> beskrivningen i <a href="#1-prepare-your-environment">Förbereda din miljö.</a></li>
    <li>Ersätt <code>&lt;app-name&gt;</code> med ett namn som är unikt för hela Azure ( giltiga tecken är , och <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.</li>
    <li>Argumentet <code>--sku F1</code> skapar webbappen på prisnivån Kostnadsfri. Utelämna det här argumentet för att använda en snabbare premiumnivå, vilket medför en kostnad per timme.</li>
    <li>Du kan även inkludera argumentet där är <code>--location &lt;location-name&gt;</code> <code>&lt;location-name&gt;</code> en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> kommandot .</li>
    </ul>
    </details>
    
1. Vänta tills kommandot har slutförts. Det kan ta några minuter och slutar med "Du kan starta appen http:// &lt; appnamnet &gt; .azurewebsites.net".

    <details>
    <summary>Vad <code>az webapp up</code> gör?</summary>
    <p>Kommandot <code>az webapp up</code> utför följande åtgärder:</p>
    <ul>
    <li>Skapa en standardresursgrupp.</li>
    <li>Skapa en App Service plan.</li>
    <li><a href="/cli/azure/webapp#az_webapp_create">Skapa en App Service med</a> det angivna namnet.</li>
    <li><a href="/azure/app-service/deploy-zip">Zip-distribuera</a> filer från den aktuella arbetskatalogen till appen.</li>
    <li>När den körs visas meddelanden om resursskapande, loggning och ZIP-distribution.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Exempel på utdata från kommandot az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Exempel på utdata från kommandot az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Bläddra till appen

**Bläddra till det distribuerade programmet** i webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. Uppdatera och distribuera om koden

1. **Öppna filen _Startup.cs_ i** den lokala katalogen. 

1. **Gör en liten ändring** i texten i metod anropa `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Spara ändringarna**.

1. **Kör** `az webapp up` omdistribuera:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Vad gör <code>az webapp up</code> den här gången?</summary>
    Första gången du körde kommandot sparade det appnamnet, resursgruppen och App Service plan i <i>.azure/config-filen</i> från projektroten. När du kör den igen från projektroten använder den de värden som sparats i <i>.azure/config,</i>identifierar att App Service-resurserna redan finns och utför zip-distributionen igen.
    </details>
    
1. När distributionen är klar **trycker du på uppdatera** i webbläsarfönstret som öppnades tidigare.

    ![Uppdaterad exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Hantera din nya Azure-app

1. Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

1. I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Skärmbild av sidan App Services ett exempel på en Azure-app som valts.":::

1. På sidan Översikt kan du utföra grundläggande hanteringsuppgifter som att bläddra, stoppa, starta, starta om och ta bort. Menyn till vänster innehåller olika sidor för att konfigurera appen. 

    ![App Service-sidan på Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Rensa resurser

**Kör** `az group delete --name myResourceGroup` för att ta bort resursgruppen.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Nästa steg

- [Självstudie: ASP.NET Core-app med SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurera ASP.NET Core-app](configure-language-dotnetcore.md)

::: zone-end
