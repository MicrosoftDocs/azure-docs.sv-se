---
title: Ta bort Application Insights i Visual Studio – Azure Monitor
description: Ta bort Application Insights SDK för ASP.NET och ASP.NET Core i Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704318"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Ta bort Application Insights i Visual Studio

I den här artikeln visas hur du tar bort ASP.NET och ASP.NET Core Application Insights SDK i Visual Studio.

Om du vill ta bort Application Insights måste du ta bort NuGet-paketen och referenserna från API: et i ditt program. Du kan avinstallera NuGet-paket med hjälp av paket hanterings konsolen eller hantera NuGet-lösningen i Visual Studio. I följande avsnitt visas två sätt att ta bort NuGet-paket och vad som lades till automatiskt i projektet. Se till att bekräfta de filer som lagts till och områden med i din egen kod där du har gjort anrop till API: et borttaget.

## <a name="uninstall-using-the-package-management-console"></a>Avinstallera med paket hanterings konsolen

# <a name="net"></a>[.NET](#tab/net)

1. Öppna paket hanterings konsolen genom att välja verktyg > NuGet Package Manager > Package Manager-konsolen på den översta menyn.
     
    ![På den översta menyn klickar du på verktyg > NuGet Package Manager > Package Manager-konsolen](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Om spårnings insamling är aktiverat måste du först avinstallera Microsoft. ApplicationInsights. TraceListener. Ange `Uninstall-package Microsoft.ApplicationInsights.TraceListener` sedan följande steg för att ta bort Microsoft. ApplicationInsights. Web.

1. Ange följande kommando: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    När du har angett kommandot kommer Application Insights-paketet och alla dess beroenden att avinstalleras från projektet.
    
    ![Ange kommando i konsolen](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Öppna paket hanterings konsolen genom att välja verktyg > NuGet Package Manager > Package Manager-konsolen på den översta menyn.

    ![På den översta menyn klickar du på verktyg > NuGet Package Manager > Package Manager-konsolen](./media/remove-application-insights/package-manager.png)

1. Ange följande kommando: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    När du har angett kommandot kommer Application Insights-paketet och alla dess beroenden att avinstalleras från projektet.

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Avinstallera med Visual Studio NuGet-ANVÄNDARGRÄNSSNITTET

# <a name="net"></a>[.NET](#tab/net)

1. I *Solution Explorer* till höger högerklickar du på **lösning** och väljer **Hantera NuGet-paket för lösningen**.

    Sedan visas en skärm som gör att du kan redigera alla NuGet-paket som ingår i projektet.
    
     ![Högerklicka på lösning, gå till Solution Explorer och välj Hantera NuGet-paket för lösningen](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Om spårnings insamling är aktiverat måste du först avinstallera Microsoft. ApplicationInsights. TraceListener utan att ta bort beroenden och sedan följa stegen nedan för att avinstallera Microsoft. ApplicationInsights. Web med ta bort beroenden markerade.

1. Klicka på paketet **Microsoft. ApplicationInsights. Web** . Till höger markerar du kryss rutan bredvid **projekt** för att markera alla projekt.

1. Om du vill ta bort alla beroenden när du avinstallerar väljer du knappen **alternativ** i list rutan under det avsnitt där du har valt projekt.

    Under *avinstallations alternativ* markerar du kryss rutan bredvid *ta bort beroenden*.

1. Välj **Avinstallera**.
    
    ![Skärm bild som visar fönstret Microsoft. ApplicationInsights. Web med ta bort beroenden markerat och avinstallera markerat.](./media/remove-application-insights/uninstall-framework.png)

    En dialog ruta som visar alla beroenden som ska tas bort från programmet visas. Välj **OK** för att avinstallera.
    
    ![Skärm bild som visar en dialog ruta med de beroenden som ska tas bort.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  När allt har avinstallerats kan du fortfarande se "ApplicationInsights.config" och "AiHandleErrorAttribute. CS" i *Solution Explorer*. Du kan ta bort de två filerna manuellt.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. I *Solution Explorer* till höger högerklickar du på **lösning** och väljer **Hantera NuGet-paket för lösningen**.

    Sedan visas en skärm som gör att du kan redigera alla NuGet-paket som ingår i projektet.

    ![Högerklicka på lösning, gå till Solution Explorer och välj Hantera NuGet-paket för lösningen](./media/remove-application-insights/manage-nuget-core.png)

1. Klicka på paketet "Microsoft. ApplicationInsights. AspNetCore". Till höger markerar du kryss rutan bredvid *projekt* för att markera alla projekt och väljer sedan **Avinstallera**.

    ![Markera ta bort beroenden och avinstallera](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Vad som skapas när du lägger till Application Insights

När du lägger till Application Insights i ditt projekt skapas filer och lägger till kod till några av dina filer. Att avinstallera NuGet-paketen kommer inte alltid att ignorera filer och kod. För att helt ta bort Application Insights bör du kontrol lera och manuellt ta bort den tillagda koden eller filerna tillsammans med alla API-anrop som du har lagt till i projektet.

# <a name="net"></a>[.NET](#tab/net)

När du lägger till Application Insights Telemetry i ett Visual Studio ASP.NET-projekt lägger den till följande filer:

- ApplicationInsights.config
- AiHandleErrorAttribute. CS

Följande delar av kod läggs till:

- [Ditt projekts namn]. CSPROJ

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Om projektet har en layout. cshtml-fil läggs koden nedan till.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jspå

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig. CS

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

När du lägger till Application Insights Telemetry i ett projekt i Visual Studio ASP.NET Core mal len lägger du till följande kod:

- [Ditt projekts namn]. CSPROJ

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.jspå:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jspå
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Monitor](../overview.md)
