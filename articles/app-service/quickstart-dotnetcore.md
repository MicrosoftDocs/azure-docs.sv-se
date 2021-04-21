---
title: 'Snabbstart: Distribuera en ASP.NET-webbapp'
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera din första ASP.NET app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762469"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Snabbstart: Distribuera en ASP.NET-webbapp

I den här snabbstarten lär du dig att skapa och distribuera din första ASP.NET webbapp till [Azure App Service](overview.md). App Service stöder olika versioner av .NET-appar och ger en mycket skalbar och självkorrigering av webbvärdtjänsten. ASP.NET webbappar är plattformsoberoende och kan finnas i Linux eller Windows. När du är klar har du en Azure-resursgrupp som består av en App Service värdplan och en App Service med en distribuerad webbapp.

> [!TIP]
> .NET Core 3.1 är den aktuella LTS-versionen (long-term support) av .NET. Mer information finns i [.NET-supportprincip .](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

## <a name="prerequisites"></a>Förutsättningar

:::zone target="docs" pivot="development-environment-vs"

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019 med</a> **arbetsbelastningen ASP.NET webbutveckling.**

    Om du redan har installerat Visual Studio 2019:

    - Installera de senaste uppdateringarna i Visual Studio genom att **välja Hjälp sök** efter  >  **uppdateringar.**
    - Lägg till arbetsbelastningen genom **att välja Verktyg** Hämta verktyg och  >  **funktioner.**

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools-tillägget.</a>

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installera den senaste SDK:n för .NET Core 3.1. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .NET 5.0 SDK:n. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Installera .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> Visual Studio Code är plattformsoberoende, men .NET Framework är det inte. Om du utvecklar appar .NET Framework med Visual Studio Code bör du överväga att använda en Windows-dator för att uppfylla byggberoendena.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/dotnet)
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>.
- .NET SDK (inklusive runtime och CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installera den senaste SDK:n för .NET Core 3.1. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .NET 5.0 SDK:n. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .NET 5.0 SDK:n. </a> och <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> [.NET CLI är](/dotnet/core/tools) dock plattformsoberoende, .NET Framework inte. Om du utvecklar appar .NET Framework .NET CLI bör du överväga att använda en Windows-dator för att uppfylla byggberoendena.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Öppna Visual Studio och välj sedan **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt,** leta upp och välj ASP.NET Web Core **App** och välj sedan **Nästa.**
1. I **Konfigurera det nya projektet** ger du programmet namnet _MyFirstAzureWebApp_ och väljer sedan **Nästa.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Konfigurera ASP.NET Core 3.1-webbapp" border="true":::

1. Välj **.NET Core 3.1 (långsiktigt stöd).**
1. Kontrollera att **Autentiseringstyp** är inställt på **Ingen.** Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – Välj .NET Core 3.1 och Ingen som Autentiseringstyp." border="true":::

1. På Visual Studio väljer du **Felsök**  >  **Starta utan felsökning för att** köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – .NET Core 3.1 bläddra lokalt" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Öppna Visual Studio och välj sedan **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt,** leta upp och välj ASP.NET Web Core **App** och välj sedan **Nästa.**
1. I **Konfigurera det nya projektet** ger du programmet namnet _MyFirstAzureWebApp_ och väljer sedan **Nästa.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – Konfigurera ASP.NET 5.0-webbapp." border="true":::

1. Välj **.NET Core 5.0 (aktuell).**
1. Kontrollera att **Autentiseringstyp är** inställt på **Ingen.** Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – Ytterligare information när du väljer .NET Core 5.0." border="true":::

1. På Visual Studio väljer du **Felsök**  >  **Starta utan felsökning för att** köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – ASP.NET Core 5.0 körs lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Öppna Visual Studio och välj sedan **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt,** leta upp och välj ASP.NET **webbprogram (.NET Framework)** och välj sedan **Nästa.**
1. I **Konfigurera det nya projektet** ger du programmet namnet _MyFirstAzureWebApp_ och väljer sedan **Skapa.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – Konfigurera ASP.NET Framework 4.8-webbapp." border="true":::

1. Välj **MVC-mallen.**
1. Kontrollera att **Autentisering är** inställt på **Ingen autentisering**. Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – Välj MVC-mallen." border="true":::

1. På Visual Studio väljer du **Felsök Starta** utan felsökning för att  >   köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio – ASP.NET Framework 4.8 körs lokalt." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Skapa en ny mapp med _namnet MyFirstAzureWebApp_ och öppna den i Visual Studio Code. Öppna <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminalfönstret</a> och skapa en ny .NET-webbapp med [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) kommandot .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> Flaggan är en friformstextersättning av `--target-framework-override` målramverkets moniker (TFM) för projektet och garanterar inte att stödmallen finns eller  kompileras. Du kan bara skapa och köra .NET Framework i Windows.

---

Från **Terminal** i Visual Studio Code kör du programmet lokalt med [`dotnet run`](/dotnet/core/tools/dotnet-run) kommandot .

```dotnetcli
dotnet run
```

Öppna webbläsaren och navigera till appen på `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser mallen för ASP.NET Core 3.1-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – kör .NET Core 3.1 i webbläsaren lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Du ser mallen för ASP.NET Core 5.0-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – kör .NET 5.0 i webbläsaren lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Mallen för webbappen ASP.NET Framework 4.8 visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – kör .NET 4.8 i webbläsaren lokalt." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Öppna ett terminalfönster på datorn till en arbetskatalog. Skapa en ny .NET-webbapp med [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) kommandot och ändra sedan katalogerna till den nyligen skapade appen.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> Flaggan är en friformstextersättning av `--target-framework-override` målramverkets moniker (TFM) för projektet och garanterar inte att stödmallen finns eller  kompileras. Du kan bara skapa .NET Framework i Windows.

---

Kör programmet lokalt från samma terminalsession med [`dotnet run`](/dotnet/core/tools/dotnet-run) kommandot .

```dotnetcli
dotnet run
```

Öppna webbläsaren och navigera till appen på `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser mallen för ASP.NET Core 3.1-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 3.1 i den lokala webbläsaren." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Du ser mallen för ASP.NET Core 5.0-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 5.0 i en lokal webbläsare." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Du ser mallen för ASP.NET Framework 4.8-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – ASP.NET Framework 4.8 i en lokal webbläsare." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera webbappen måste du först skapa och konfigurera en App Service som du kan publicera appen till.

Som en del av App Service skapar du:

- En ny [resursgrupp](../azure-resource-manager/management/overview.md#terminology) som ska innehålla alla Azure-resurser för tjänsten.
- En ny [värdplan](overview-hosting-plans.md) som anger plats, storlek och funktioner för webbservergruppen som är värd för din app.

Följ dessa steg för att skapa App Service och publicera webbappen:

:::zone target="docs" pivot="development-environment-vs"

1. I **Solution Explorer** högerklickar du på **projektet MyFirstAzureWebApp** och väljer **Publicera.**
1. I **Publicera** väljer du **Azure** och sedan **Nästa.**

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – Publicera webbappen och rikta in dig på Azure." border="true":::

1. Alternativen beror på om du redan är inloggad på Azure och om du har ett Visual Studio till ett Azure-konto. Välj **antingen Lägg till ett konto** eller Logga in **för** att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – Välj dialogrutan Logga in på Azure.":::

1. Välj specifikt **mål,** **antingen Azure App Service (Linux)** **eller Azure App Service (Windows)**.

    > [!IMPORTANT]
    > När du ASP.NET Framework 4.8 använder du **Azure App Service (Windows).**

1. Till höger om **App Service instanser väljer** du **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – dialogrutan App Service ny app.":::

1. För **Prenumeration** godkänner du prenumerationen som visas eller väljer en ny i listrutan.
1. För **Resursgrupp** väljer du **Ny.** I **Namn på ny resursgrupp** anger du *myResourceGroup* och väljer **OK.**
1. För **Värdplan** väljer du **Ny.**
1. I dialogrutan **Värdplan: Skapa ny** anger du de värden som anges i följande tabell:

    | Inställning          | Föreslaget värde          | Beskrivning                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Värdplan** | *MyFirstAzureWebAppPlan* | Namnet på App Service-planen.                                         |
    | **Plats**     | *Europa, västra*            | Datacenter som är värd för webbappen.                           |
    | **Storlek**         | *Kostnadsfri*                   | [Prisnivån][app-service-pricing-tier] avgör tillgängliga värdfunktioner. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Skapa en ny värdplan":::

1. I **Namn** anger du ett unikt appnamn som endast innehåller giltiga tecken: `a-z` , , och `A-Z` `0-9` `-` . Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn.
1. Välj **Skapa** för att skapa Azure-resurserna.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – Dialogrutan Skapa appresurser.":::

   När guiden har slutförts skapas Azure-resurserna åt dig och du är redo att publicera.

1. Stäng **guiden** genom att välja Slutför.
1. På sidan **Publicera** väljer du **Publicera.** Visual Studio skapar, paketerar och publicerar appen till Azure och startar sedan appen i standardwebbläsaren.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Du ser ASP.NET Core 3.1-webbappen på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 3.1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Du ser ASP.NET Core 5.0-webbappen på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 5.0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Webbappen ASP.NET Framework 4.8 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – ASP.NET Framework 4.8-webbapp i Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Så här distribuerar du webbappen med Visual Studio Azure Tools-tillägget:

1. I Visual Studio Code öppnar du [**kommandopaletten**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd> + <kbd>Skift</kbd> + <kbd>P</kbd>.
1. Sök efter och välj "Azure App Service: Distribuera till webbapp".
1. Svara på anvisningarna på följande sätt:

    - Välj *MyFirstAzureWebApp som* den mapp som ska distribueras.
    - Välj **Lägg till konfiguration när** du uppmanas att göra det.
    - Logga in på ditt befintliga Azure-konto om du uppmanas till det.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code – Logga in på Azure." border="true":::

    - Välj din **prenumeration.**
    - Välj **Skapa ny webbapp... Avancerat**.
    - För **Ange ett globalt unikt namn** använder du ett namn som är unikt i hela Azure ( giltiga tecken är ,*`a-z` `0-9` och `-`*). Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.
    - Välj **Skapa ny resursgrupp** och ange ett namn som `myResourceGroup` .
    - När du uppmanas att **välja en körningsstack:**
      - För *.NET Core 3.1* väljer **du .NET Core 3.1 (LTS)**
      - För *.NET 5.0* väljer du **.NET 5**
      - För *.NET Framework 4.8* väljer **du ASP.NET V4.8**
    - Välj ett operativsystem (Windows eller Linux).
        - För *.NET Framework 4.8* väljs Windows implicit.
    - Välj **Skapa en App Service plan,** ange ett namn och välj **prisnivån F1 Kostnadsfri.** [][app-service-pricing-tier]
    - Välj **Hoppa över för tillfället** för Application Insights resurs.
    - Välj en plats nära dig.

1. När publiceringen är klar väljer **du Bläddra på webbplatsen** i meddelandet och väljer Öppna **när** du tillfrågas.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Webbappen ASP.NET Core 3.1 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 3.1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Webbappen ASP.NET Core 5.0 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 5.0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Webbappen ASP.NET Framework 4.8 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – ASP.NET Framework 4.8-webbapp i Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Distribuera koden i din lokala *MyFirstAzureWebApp-katalog* med [`az webapp up`](/cli/azure/webapp#az_webapp_up) kommandot :

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Om kommandot inte känns igen måste du ha Azure CLI installerat enligt `az` beskrivningen [i Krav.](#prerequisites)
- Ersätt `<app-name>` med ett namn som är unikt för hela Azure ( giltiga tecken är ,*`a-z` `0-9` och `-`*). Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.
- Argumentet `--sku F1` skapar webbappen på **den kostnadsfria** [prisnivån][app-service-pricing-tier]. Utelämna det här argumentet för att använda en snabbare premiumnivå, vilket medför en kostnad per timme.
- Ersätt `<os>` med antingen `linux` eller `windows` . Du måste använda `windows` när du riktar ASP.NET Framework *4.8*.
- Du kan även inkludera argumentet där är `--location <location-name>` `<location-name>` en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) kommandot .

Det kan ta några minuter att slutföra kommandot. När den körs får du meddelanden om att skapa resursgruppen, App Service plan värdappen, konfigurera loggning och sedan utföra ZIP-distribution. Den matar sedan ut ett meddelande med appens URL:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Öppna en webbläsare och gå till URL:en:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser ASP.NET Core 3.1-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 3.1-webbapp i Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Du ser ASP.NET Core 5.0-webbappen på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 5.0-webbapp i Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Webbappen ASP.NET Framework 4.8 visas på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI – ASP.NET Framework 4.8-webbapp i Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Följ dessa steg för att uppdatera och distribuera om webbappen:

:::zone target="docs" pivot="development-environment-vs"

1. I **Solution Explorer** du *index.cshtml under projektet.*
1. Ersätt det första `<div>` elementet med följande kod:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Spara ändringarna.

1. Om du vill distribuera om till Azure högerklickar du på **projektet MyFirstAzureWebApp** **i Solution Explorer** väljer **Publicera.**
1. På sidan **Publicera** sammanfattning väljer du **Publicera.**

    När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Den uppdaterade webbappen ASP.NET Core 3.1 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – Uppdaterad ASP.NET Core 3.1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Den uppdaterade webbappen ASP.NET Core 5.0 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – Uppdaterad ASP.NET Core 5.0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Den uppdaterade webbappen ASP.NET Framework 4.8 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – Uppdaterad ASP.NET Framework 4.8-webbapp i Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Öppna *Index.cshtml*.
1. Ersätt det första `<div>` elementet med följande kod:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Spara ändringarna.

1. Öppna det Visual Studio **kodfältet och** välj **Azure-ikonen för** att expandera dess alternativ.
1. Under **noden APP SERVICE** expanderar du din prenumeration och högerklickar på **MyFirstAzureWebApp.**
1. Välj Distribuera **till webbapp...**.
1. Välj **Distribuera när** du tillfrågas.
1. När publiceringen är klar väljer **du Bläddra på webbplatsen** i meddelandet och väljer Öppna **när** du tillfrågas.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Den uppdaterade webbappen ASP.NET Core 3.1 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – Uppdaterad ASP.NET Core 3.1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Den uppdaterade webbappen ASP.NET Core 5.0 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – Uppdaterad ASP.NET Core 5.0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Den uppdaterade webbappen ASP.NET Framework 4.8 visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – uppdaterad ASP.NET Framework 4.8-webbapp i Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Öppna filen *Index.cshtml i den lokala* katalogen. Ersätt det första `<div>` elementet:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Spara ändringarna och distribuera sedan appen igen med `az webapp up` kommandot igen:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 är plattformsoberoende, baserat på din tidigare distributions ersätt `<os>` med antingen `linux` eller `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 är plattformsoberoende, baserat på din tidigare distributions ersätt `<os>` med antingen `linux` eller `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 har ramverksberoenden och måste finnas i Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Om du är intresserad av att vara värd för dina .NET-appar i Linux kan du migrera [från ASP.NET Framework till ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Det här kommandot använder värden som cachelagras lokalt i *.azure/config-filen,* inklusive appnamn, resursgrupp och App Service plan.

När distributionen är klar växlar du tillbaka till webbläsarfönstret som öppnades i steget **Bläddra till** appen och trycker på Uppdatera.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Den uppdaterade webbappen ASP.NET Core 3.1 visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – Uppdaterad ASP.NET Core 3.1-webbapp i Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Den uppdaterade webbappen ASP.NET Core 5.0 visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – uppdaterad ASP.NET Core 5.0-webbapp i Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Den uppdaterade webbappen ASP.NET Framework 4.8 visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – uppdaterad ASP.NET Framework 4.8-webbapp i Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera webbappen går [du till Azure Portal](https://portal.azure.com)och söker efter och väljer **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal – Välj App Services alternativ.":::

På **App Services** väljer du namnet på webbappen.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal – App Services med en exempelwebbapp vald.":::

**Översiktssidan** för webbappen innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller ytterligare sidor för att konfigurera din app.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal – App Service översiktssida.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade och distribuerade du en ASP.NET-webbapp till Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core-app med SQL-databas](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core 3.1-app](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core-app med SQL-databas](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core 5.0-app](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Gå vidare till nästa artikel om du vill lära dig hur du skapar .NET Framework app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET-app med SQL-databas](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Framework-app](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
