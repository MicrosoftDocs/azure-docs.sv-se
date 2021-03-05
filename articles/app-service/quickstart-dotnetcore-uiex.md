---
title: 'Snabb start: skapa en C# ASP.NET Core-app'
description: Lär dig hur du kör Web Apps i Azure App Service genom att distribuera din första ASP.NET Core-app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2a789b4ca1261c79e8e6eb93a4ed44e7e8e9272e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214243"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snabb start: skapa en ASP.NET Core webbapp i Azure

::: zone pivot="platform-windows"  

I den här snabb starten får du lära dig hur du skapar och distribuerar din första ASP.NET Core-webbapp till <abbr title="En HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila backend-program.">Azure App Service</abbr>. App Service stöder .NET 5,0-appar.

När du är klar har du en Azure <abbr title="En logisk behållare för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>, bestående av en <abbr title="Den plan som anger plats, storlek och funktioner i webb server gruppen som är värd för din app.">App Service-plan</abbr> och en <abbr title="En representation av din webbapp, som innehåller din app-kod, DNS-värdnamn, certifikat och relaterade resurser.">App Service-app</abbr> med ett distribuerat exempel ASP.NET Core program.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

- **Skaffa ett Azure-konto** med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- **Installera <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** med arbets belastningen **ASP.net och webb utveckling** .

<details>
<summary>Har du redan Visual Studio 2019?</summary>
Om du redan har installerat Visual Studio 2019:

<ul>
<li><strong>Installera de senaste uppdateringarna</strong> i Visual Studio genom att välja <strong>Hjälp</strong> &gt; <strong>sökning efter uppdateringar</strong>. De senaste uppdateringarna innehåller .NET 5,0 SDK.</li>
<li><strong>Lägg till arbets belastningen</strong> genom att välja <strong>verktyg</strong> &gt; <strong>Hämta verktyg och funktioner</strong>.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. skapa en ASP.NET Core webbapp

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** väljer du **ASP.net Core webb program** och bekräftar att **C#** visas på de språk som du väljer. Välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du ditt webb program projekt *MyFirstAzureWebApp* och väljer **skapa**.

   ![Konfigurera ditt webbapp](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. För en .NET 5,0-app väljer du **ASP.NET Core 5,0** i list rutan. Annars använder du standardvärdet.

1. Du kan distribuera vilken typ av ASP.NET Core webbapp som helst till Azure, men i den här snabb starten väljer du mallen **ASP.net Core webbapp** . Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering** och att inget annat alternativ har valts. Välj sedan **Skapa**.

   ![Skapa en ny ASP.NET Core webbapp](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra din webbapp lokalt.

   ![Webbapp som körs lokalt](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. publicera din webbapp

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

   | Inställning  | Föreslaget värde |
   | -------- | --------------- |
   | **Värdplan**  | *myFirstAzureWebAppPlan* |
   | **Plats**      | *Europa, västra* |
   | **Storlek**          | *Kostnadsfri* |
   
   ![Skapa ny värd plan](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. I **namn** anger du ett unikt namn för appen.

    <details>
        <summary>Vilka tecken kan jag använda?</summary>
        Giltiga tecken är a-z, A-Z, 0-9 och-. Du kan acceptera det automatiskt genererade unika namnet. URL: en för webbappen är http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , där <code>&lt;app-name&gt;</code> är namnet på appen.
    </details>

1. Välj **skapa** för att skapa Azure-resurserna. 

   ![Skapa app-resurser](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Vänta tills guiden har slutfört skapandet av Azure-resurser. Stäng guiden genom att klicka på **Slutför** .

1. På sidan **publicera** klickar du på **publicera** för att distribuera projektet. 

    <details>
        <summary>Vad gör Visual Studio?</summary>
        Visual Studio skapar, paketerar och publicerar appen till Azure och startar sedan appen i standard webbläsaren.
    </details>

   ![Publicerad ASP.NET-webbapp som körs i Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. uppdatera appen och distribuera om

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

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Hantera Azure-appen

1. Gå till [Azure Portal](https://portal.azure.com)och Sök efter och välj **app Services**.

    ![Välj App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. På sidan **app Services** väljer du namnet på din webbapp.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Skärm bild av App Services sidan med ett exempel på en webbapp vald.":::

1. **Översikts** sidan för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller fler sidor för att konfigurera din app.

    ![App Service i Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Rensa resurser

1. Från Azure Portal-menyn eller **Start** sidan väljer du **resurs grupper**. På sidan **resurs grupper** väljer du sedan **myResourceGroup**.

1. På sidan **myResourceGroup** kontrollerar du att de listade resurserna är de som du vill ta bort.

1. Välj **ta bort resurs grupp**, Skriv **myResourceGroup** i text rutan för att bekräfta och välj sedan **ta bort**.

<hr/> 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

- [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Den här snabb starten visar hur du skapar en [.net Core](/aspnet/core/) -app på <abbr title="Med App Service i Linux får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux.">App Service på Linux</abbr>. Du skapar appen med [Azure CLI](/cli/azure/get-started-with-azure-cli) och använder Git för att distribuera .NET Core-koden till appen.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

- **Skaffa ett Azure-konto** med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet/).
- **Installera** den senaste <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.net Core 3,1 sdk</a> eller <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.net 5,0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installera den senaste versionen av Azure CLI</a>**.

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. skapa appen lokalt

1. Kör `mkdir hellodotnetcore` för att skapa katalogen.

    ```bash
    mkdir hellodotnetcore
    ```

1. Kör `cd hellodotnetcore` för att ändra katalogen. 

    ```bash
    cd hellodotnetcore
    ```

1. Kör `dotnet new web` för att skapa en ny .net Core-app.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. kör appen lokalt

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

## <a name="5-deploy-the-app"></a>5. distribuera appen

1. **Kör** `az webapp up` i din lokala mapp. **Ersätt** <App-Name> med ett globalt unikt namn.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Felsökning</summary>
    <ul>
    <li>Om <code>az</code> kommandot inte känns igen kontrollerar du att Azure CLI är installerat enligt beskrivningen i <a href="#1-prepare-your-environment">förbereda din miljö</a>.</li>
    <li>Ersätt <code>&lt;app-name&gt;</code> med ett namn som är unikt för alla Azure ( <em> giltiga tecken är <code>a-z</code> , <code>0-9</code> och <code>-</code> </em> ). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.</li>
    <li><code>--sku F1</code>Argumentet skapar webb programmet på den kostnads fria pris nivån. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå, vilket innebär en timkostnad.</li>
    <li>Du kan också inkludera argumentet <code>--location &lt;location-name&gt;</code> där <code>&lt;location-name&gt;</code> är en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> kommandot.</li>
    </ul>
    </details>
    
1. Vänta tills kommandot har slutförts. Det kan ta några minuter och slutar med "du kan starta appen på http:// &lt; App-Name &gt; . azurewebsites.net".

    <details>
    <summary>Vad <code>az webapp up</code> gör jag?</summary>
    <p>Kommandot <code>az webapp up</code> utför följande åtgärder:</p>
    <ul>
    <li>Skapa en standardresursgrupp.</li>
    <li>Skapa en standard App Service plan.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Skapa en app service-app</a> med det angivna namnet.</li>
    <li><a href="/azure/app-service/deploy-zip">Zip-distribuera</a> filer från den aktuella arbetskatalogen till appen.</li>
    <li>Under körningen innehåller den meddelanden om att skapa, logga och ZIP-distribution.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Bläddra till appen

**Bläddra till det distribuerade programmet** med hjälp av webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

![Exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. uppdatera och distribuera om koden

1. **Öppna filen _startup.cs_** i den lokala katalogen. 

1. **Gör en liten ändring** i texten i metod anropet `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Spara ändringarna**.

1. **Kör** `az webapp up` för att distribuera om:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Vad <code>az webapp up</code> gör den här gången?</summary>
    Första gången du körde kommandot sparades appens namn, resurs grupp och App Service plan i <i>Azure/config</i> -filen från projekt roten. När du kör det igen från projekt roten används värdena som sparats i <i>. Azure/config</i>, identifierar att de App Service resurserna redan finns och utför zip-distribution igen.
    </details>
    
1. När distributionen är klar klickar du på **Uppdatera** i webbläsarfönstret som du öppnade tidigare.

    ![Uppdaterad exempelapp som körs i Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. hantera din nya Azure-App

1. Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

1. I den vänstra menyn, klickar du på **App Services** och därefter på namnet på din Azure-app.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Skärm bild av sidan App Services som visar att ett exempel på en Azure-app har valts.":::

1. På sidan Översikt kan du utföra grundläggande hanterings uppgifter som att bläddra, stoppa, starta, starta om och ta bort. Menyn till vänster innehåller olika sidor för att konfigurera appen. 

    ![App Service-sidan på Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Rensa resurser

**Kör** `az group delete --name myResourceGroup` för att ta bort resurs gruppen.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Har du problem? Berätta för oss.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Nästa steg

- [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)

::: zone-end
