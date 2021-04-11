---
title: 'Snabb start: Distribuera en ASP.NET-webbapp'
description: Lär dig hur du kör Web Apps i Azure App Service genom att distribuera din första ASP.NET-app.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058346"
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

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Snabb start: Distribuera en ASP.NET-webbapp

I den här snabb starten får du lära dig hur du skapar och distribuerar din första ASP.NET-webbapp till [Azure App Service](overview.md). App Service stöder olika versioner av .NET-appar och ger en mycket skalbar och själv korrigering av webb värd tjänst. ASP.NET-webbappar är plattforms oberoende och kan finnas på Linux eller Windows. När du är klar har du en Azure-resurs grupp som består av en App Service värd plan och en App Service med ett distribuerat webb program.

> [!TIP]
> .NET Core 3,1 är den aktuella LTS-versionen av .NET. Mer information finns i [.net support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Förutsättningar

:::zone target="docs" pivot="development-environment-vs"

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> med arbets belastningen **ASP.net och webb utveckling** .

    Om du redan har installerat Visual Studio 2019:

    - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp**  >  **sökning efter uppdateringar**.
    - Lägg till arbets belastningen genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Tillägget <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure-verktyg</a> .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installera den senaste .NET Core 3,1 SDK: n. </a>

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .NET 5,0 SDK: n. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Installera .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> Visual Studio Code är en plattforms oberoende, men .NET Framework inte. Om du utvecklar .NET Framework appar med Visual Studio Code bör du överväga att använda en Windows-dator för att uppfylla build-beroendena.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>.
- .NET SDK (inklusive körning och CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installera den senaste .NET Core 3,1 SDK: n. </a>

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .NET 5,0 SDK: n. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installera den senaste .net 5,0 SDK: n. </a> och <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> [.Net CLI](/dotnet/core/tools) är en plattforms oberoende, men .NET Framework inte. Om du utvecklar .NET Framework appar med .NET CLI bör du överväga att använda en Windows-dator för att uppfylla build-beroenden.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt**, Sök efter och välj **ASP.net Web Core-app** och välj sedan **Nästa**.
1. I **Konfigurera ditt nya projekt** namnger du programmet _MyFirstAzureWebApp_ och väljer sedan **Nästa**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Konfigurera ASP.NET Core 3,1-webbapp" border="true":::

1. Välj **.net Core 3,1 (långsiktig support)**.
1. Se till att **autentiseringstypen** är inställd på **ingen**. Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – Välj .NET Core 3,1 och inget för autentiseringstyp." border="true":::

1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – .NET Core 3,1 Sök lokalt" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt**, Sök efter och välj **ASP.net Web Core-app** och välj sedan **Nästa**.
1. I **Konfigurera ditt nya projekt** namnger du programmet _MyFirstAzureWebApp_ och väljer sedan **Nästa**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – Konfigurera ASP.NET 5,0-webbapp." border="true":::

1. Välj **.net Core 5,0 (aktuell)**.
1. Se till att **autentiseringstypen** är inställd på **ingen**. Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – ytterligare information när du väljer .NET Core 5,0." border="true":::

1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – ASP.NET Core 5,0 som körs lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt**, Sök efter och välj **ASP.net webb program (.NET Framework)** och välj sedan **Nästa**.
1. I **Konfigurera ditt nya projekt** namnger du programmet _MyFirstAzureWebApp_ och väljer sedan **skapa**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – Konfigurera ASP.NET Framework 4,8-webbapp." border="true":::

1. Välj **MVC** -mallen.
1. Kontrol lera att **autentiseringen** är inställd på **Ingen autentisering**. Välj **Skapa**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – Välj MVC-mallen." border="true":::

1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra webbappen lokalt.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio – ASP.NET Framework 4,8 körs lokalt." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Skapa en ny mapp med namnet _MyFirstAzureWebApp_ och öppna den i Visual Studio Code. Öppna <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminalfönstret</a> och skapa en ny .NET-webbapp med hjälp av [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) kommandot.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override`Flaggan är en text ersättning i form av en fritext av målets Ramverks-moniker (TFM) för projektet och ger *inga garantier* för att den stödjande mallen finns eller kompileras. Du kan bara skapa och köra .NET Framework appar i Windows.

---

Kör programmet lokalt med kommandot från **terminalen** i Visual Studio Code [`dotnet run`](/dotnet/core/tools/dotnet-run) .

```dotnetcli
dotnet run
```

Öppna webbläsaren och navigera till appen på `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser mallen ASP.NET Core 3,1-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – kör .NET Core 3,1 i webbläsare lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Du ser mallen ASP.NET Core 5,0-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – kör .NET 5,0 i webbläsare lokalt." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Du ser mallen ASP.NET Framework 4,8-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – kör .NET 4,8 i webbläsare lokalt." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Öppna ett terminalfönster på datorn i en arbets katalog. Skapa en ny .NET-webbapp med [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) kommandot och ändra sedan katalogerna till den nya appen.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override`Flaggan är en text ersättning i form av en fritext av målets Ramverks-moniker (TFM) för projektet och ger *inga garantier* för att den stödjande mallen finns eller kompileras. Du kan bara bygga .NET Framework-appar i Windows.

---

Kör programmet lokalt från samma terminalserversession med [`dotnet run`](/dotnet/core/tools/dotnet-run) kommandot.

```dotnetcli
dotnet run
```

Öppna webbläsaren och navigera till appen på `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser mallen ASP.NET Core 3,1-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 3,1 i lokal webbläsare." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Du ser mallen ASP.NET Core 5,0-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 5,0 i lokal webbläsare." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Du ser mallen ASP.NET Framework 4,8-webbapp som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – ASP.NET Framework 4,8 i lokal webbläsare." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publicera din webbapp

Om du vill publicera din webbapp måste du först skapa och konfigurera en ny App Service som du kan publicera din app till.

Som en del av att konfigurera App Service skapar du:

- En ny [resurs grupp](../azure-resource-manager/management/overview.md#terminology) som innehåller alla Azure-resurser för tjänsten.
- En ny [värd plan](overview-hosting-plans.md) som anger plats, storlek och funktioner för den webb Server grupp som är värd för din app.

Följ de här stegen för att skapa din App Service och publicera din webbapp:

:::zone target="docs" pivot="development-environment-vs"

1. I **Solution Explorer** högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**.
1. I **publicera** väljer du **Azure** och sedan **Nästa**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – publicera webbappen och Azure-målet." border="true":::

1. Vilka alternativ som är tillgängliga beror på om du redan är inloggad på Azure och om du har ett Visual Studio-konto som är länkat till ett Azure-konto. Välj antingen **Lägg till ett konto** eller **Logga** in för att logga in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto som du vill använda.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – Välj Logga in på Azure dialog ruta.":::

1. Välj det **specifika målet**, antingen **Azure App Service (Linux)** eller **Azure App Service (Windows)**.

    > [!IMPORTANT]
    > När du riktar ASP.NET Framework 4,8 använder du **Azure App Service (Windows)**.

1. Till höger om **App Service instanser** väljer du **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – dialog rutan ny App Service app.":::

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.
1. För **resurs grupp** väljer du **nytt**. I **nytt resurs grupp namn** anger du *MyResourceGroup* och väljer **OK**.
1. För **värd plan** väljer du **nytt**.
1. Ange de värden som anges i följande tabell i dialog rutan **värd plan: skapa ny** .

    | Inställning          | Föreslaget värde          | Beskrivning                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Värdplan** | *MyFirstAzureWebAppPlan* | Namnet på App Service-planen.                                         |
    | **Plats**     | *Europa, västra*            | Datacenter som är värd för webbappen.                           |
    | **Storlek**         | *Kostnadsfri*                   | [Prisnivån][app-service-pricing-tier] avgör tillgängliga värdfunktioner. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Skapa ny värd plan":::

1. I **namn** anger du ett unikt namn för appen som bara innehåller giltiga tecken är `a-z` , `A-Z` , `0-9` och `-` . Du kan acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app-name>.azurewebsites.net`, där `<app-name>` är appens namn.
1. Välj **skapa** för att skapa Azure-resurserna.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – dialog rutan skapa app-resurser.":::

   När guiden har slutförts skapas Azure-resurserna åt dig och du är redo att publicera.

1. Stäng guiden genom att klicka på **Slutför** .
1. På sidan **publicera** väljer du **publicera**. Visual Studio skapar, paketerar och publicerar appen till Azure och startar sedan appen i standard webbläsaren.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Du ser att ASP.NET Core 3,1-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 3,1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Du ser att ASP.NET Core 5,0-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 5,0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Du ser ASP.NET Framework 4,8-webbappen som visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – ASP.NET Framework 4,8-webbapp i Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Så här distribuerar du din webbapp med hjälp av Visual Studio Azure tools-tillägget:

1. I Visual Studio Code öppnar du [**kommando-paletten**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) <kbd>CTRL</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>.
1. Sök efter och välj "Azure App Service: Distribuera på webbapp".
1. Svara på prompterna på följande sätt:

    - Välj *MyFirstAzureWebApp* som den mapp som ska distribueras.
    - Välj **Lägg till konfiguration** när du uppmanas till detta.
    - Logga in på ditt befintliga Azure-konto om du uppmanas till det.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code – logga in på Azure." border="true":::

    - Välj din **prenumeration**.
    - Välj **Skapa ny webbapp... Avancerat**.
    - För **Ange ett globalt unikt namn** använder du ett namn som är unikt för alla Azure (*giltiga tecken är `a-z` , `0-9` och `-`*). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
    - Välj **Skapa ny resurs grupp** och ange ett namn som `myResourceGroup` .
    - När du uppmanas att **välja en körnings stack**:
      - För *.net core 3,1* väljer du **.net Core 3,1 (LTS)**
      - För *.net 5,0* väljer du **.net 5**
      - För *.NET Framework 4,8* väljer du **ASP.net v 4.8**
    - Välj ett operativ system (Windows eller Linux).
        - För *.NET Framework 4,8* kommer Windows att väljas implicit.
    - Välj **skapa en ny app service plan**, ange ett namn och välj [pris nivån][app-service-pricing-tier] **F1 kostnads fri** .
    - Välj **hoppa över för tillfället** för Application Insights resursen.
    - Välj en plats nära dig.

1. När publiceringen är klar väljer du **Bläddra webbplats** i meddelandet och väljer **Öppna** när du uppmanas till det.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Du ser att ASP.NET Core 3,1-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 3,1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Du ser att ASP.NET Core 5,0-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 5,0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Du ser ASP.NET Framework 4,8-webbappen som visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – ASP.NET Framework 4,8-webbapp i Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Distribuera koden i din lokala *MyFirstAzureWebApp* -katalog med hjälp av [`az webapp up`](/cli/azure/webapp#az_webapp_up) kommandot:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Om `az` kommandot inte känns igen kontrollerar du att Azure CLI är installerat enligt beskrivningen i [krav](#prerequisites).
- Ersätt `<app-name>` med ett namn som är unikt för alla Azure (*giltiga tecken är `a-z` , `0-9` och `-`*). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
- `--sku F1`Argumentet skapar webb programmet på den **kostnads fria** [pris nivån][app-service-pricing-tier]. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå, vilket innebär en timkostnad.
- Ersätt `<os>` med antingen `linux` eller `windows` . Du måste använda `windows` när du riktar in dig på *ASP.NET Framework 4,8*.
- Du kan också inkludera argumentet `--location <location-name>` där `<location-name>` är en tillgänglig Azure-region. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) kommandot.

Det kan ta några minuter att slutföra kommandot. Under körningen innehåller den meddelanden om att skapa resurs gruppen, App Service plan och värd appen, Konfigurera loggning och sedan utföra ZIP-distribution. Den matar sedan ut ett meddelande med appens URL:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Öppna en webbläsare och gå till URL: en:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser att ASP.NET Core 3,1-webbappen visas på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 3,1-webbapp i Azure.":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Du ser att ASP.NET Core 5,0-webbappen visas på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 5,0-webbapp i Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Du ser ASP.NET Framework 4,8-webbappen som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI-ASP.NET Framework 4,8-webbapp i Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Följ dessa steg för att uppdatera och distribuera om din webbapp:

:::zone target="docs" pivot="development-environment-vs"

1. Öppna *index. cshtml* under projektet i **Solution Explorer**.
1. Ersätt det första `<div>` elementet med följande kod:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Spara ändringarna.

1. Om du vill distribuera om till Azure högerklickar du på projektet **MyFirstAzureWebApp** i **Solution Explorer** och väljer **publicera**.
1. På sidan **publicera** Sammanfattning väljer du **publicera**.

    När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Du ser att den uppdaterade ASP.NET Core 3,1-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – uppdaterad ASP.NET Core 3,1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Du ser att den uppdaterade ASP.NET Core 5,0-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – uppdaterad ASP.NET Core 5,0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Du ser den uppdaterade ASP.NET Framework 4,8-webbappen som visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – uppdaterad ASP.NET Framework 4,8-webbapp i Azure.":::

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

1. Öppna Visual Studio Code- **fältet** och välj **Azure** -ikonen för att expandera dess alternativ.
1. Under **App Service** -noden expanderar du din prenumeration och högerklickar på **MyFirstAzureWebApp**.
1. Välj **distribuera till webbapp...**.
1. Välj **distribuera** när du uppmanas till detta.
1. När publiceringen är klar väljer du **Bläddra webbplats** i meddelandet och väljer **Öppna** när du uppmanas till det.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Du ser att den uppdaterade ASP.NET Core 3,1-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – uppdaterad ASP.NET Core 3,1-webbapp i Azure.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Du ser att den uppdaterade ASP.NET Core 5,0-webbappen visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – uppdaterad ASP.NET Core 5,0-webbapp i Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Du ser den uppdaterade ASP.NET Framework 4,8-webbappen som visas på sidan.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – uppdaterad ASP.NET Framework 4,8-webbapp i Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Öppna filen *index. cshtml* i den lokala katalogen. Ersätt det första `<div>` elementet:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Spara ändringarna och distribuera sedan om appen med `az webapp up` kommandot igen:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 är plattforms oberoende, baserat på din tidigare distribution Ersätt `<os>` med antingen `linux` eller `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

ASP.NET Core 5,0 är plattforms oberoende, baserat på din tidigare distribution Ersätt `<os>` med antingen `linux` eller `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 har Ramverks beroenden och måste finnas i Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Om du är intresse rad av att vara värd för dina .NET-appar på Linux bör du överväga att migrera från [ASP.NET Framework till ASP.net Core](/aspnet/core/migration/proper-to-2x).

---

Det här kommandot använder värden som cachelagras lokalt i *Azure/config* -filen, inklusive app-namn, resurs grupp och App Service plan.

När distributionen är klar går du tillbaka till webbläsarfönstret som öppnades i **Bläddra till app** -steget och trycker på Uppdatera.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Du ser att den uppdaterade ASP.NET Core 3,1-webbappen visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – uppdaterad ASP.NET Core 3,1-webbapp i Azure.":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Du ser att den uppdaterade ASP.NET Core 5,0-webbappen visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – uppdaterad ASP.NET Core 5,0-webbapp i Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Du ser den uppdaterade ASP.NET Framework 4,8-webbappen som visas på sidan.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – uppdaterad ASP.NET Framework 4,8-webbapp i Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Om du vill hantera din webbapp går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal – Välj App Services alternativ.":::

På sidan **app Services** väljer du namnet på din webbapp.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal – App Services sida med ett exempel på en webbapp vald.":::

**Översikts** sidan för din webbapp innehåller alternativ för grundläggande hantering som att bläddra, stoppa, starta, starta om och ta bort. Den vänstra menyn innehåller fler sidor för att konfigurera din app.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal – översikts sidan för App Service.":::

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

I den här snabb starten har du skapat och distribuerat en ASP.NET-webbapp till Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core 3,1-app](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Gå vidare till nästa artikel om du vill lära dig hur du skapar en .NET Core-app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core 5,0-app](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Gå vidare till nästa artikel och lär dig hur du skapar en .NET Framework app och ansluter den till en SQL Database:

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET-app med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Framework-app](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
