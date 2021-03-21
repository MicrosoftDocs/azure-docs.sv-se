---
title: Profil ASP.NET Core Azure Linux-webbappar med Application Insights Profiler | Microsoft Docs
description: En konceptuell översikt och stegvisa anvisningar om hur du använder Application Insights Profiler.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef52e946edb5db8074a9b4e3ce5e4a81ae0bde5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97561060"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Core Azure Linux-webbappar med Application Insights Profiler

Den här funktionen finns för närvarande som en förhandsversion.

Ta reda på hur mycket tid som ägnas åt varje metod i Live-webbprogrammet när du använder [Application Insights](./app-insights-overview.md). Application Insights Profiler är nu tillgängligt för ASP.NET Core webb program som finns i Linux på Azure App Service. Den här guiden innehåller steg-för-steg-instruktioner om hur profiler-spår kan samlas in för ASP.NET Core Linux-webbappar.

När du har slutfört den här genom gången kan din app samla in profiler-spår som de spår som visas i bilden. I det här exemplet indikerar profilerings spårningen att en viss webbegäran är långsam på grund av den tid som krävs. Den frekventa *sökvägen* i koden som sakta appen är markerad med en flamma-ikon. **Om** -metoden i **HomeController** -avsnittet saktar ner webbappen eftersom metoden anropar funktionen **Thread. vilo läge** .

![Profiler-spår](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Förutsättningar
Följande instruktioner gäller för alla Windows-, Linux-och Mac-utvecklings miljöer:

* Installera [.net Core SDK 2.1.2 eller senare](https://dotnet.microsoft.com/download/archives).
* Installera git genom att följa anvisningarna på [komma igång Installera git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurera projektet lokalt

1. Öppna ett kommando tolks fönster på din dator. Följande anvisningar fungerar för alla utvecklings miljöer för Windows, Linux och Mac.

1. Skapa ett ASP.NET Core MVC-webbprogram:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Ändra arbets katalogen till rotmappen för projektet.

1. Lägg till NuGet-paketet för att samla profilerade spår:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Aktivera Application Insights i program. CS:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. Aktivera profiler i Start. CS:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Lägg till en kodrad i avsnittet **HomeController. cs** för att slumpmässigt fördröja några sekunder:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Spara och genomför ändringarna i den lokala lagrings platsen:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Skapa Linux-webbappen som värd för ditt projekt

1. Skapa en webbapp genom att använda App Service på Linux:

    ![Skapa Linux-webbappen](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Skapa autentiseringsuppgifter för distribution:

    > [!NOTE]
    > Registrera ditt lösen ord för att använda senare när du distribuerar din webbapp.

    ![Skapa autentiseringsuppgifter för distribution](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Välj distributions alternativ. Konfigurera en lokal git-lagringsplats i webbappen genom att följa anvisningarna på Azure Portal. En git-lagringsplats skapas automatiskt.

    ![Konfigurera git-lagringsplatsen](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Fler distributions alternativ finns i [App Service-dokumentationen](../../app-service/index.yml).

## <a name="deploy-your-project"></a>Distribuera projektet

1. I kommando tolkens fönster bläddrar du till rotmappen för ditt projekt. Lägg till en git-fjärrlagringsplats för att peka på lagrings platsen på App Service:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Använd det **användar namn** som du använde för att skapa autentiseringsuppgifterna för distributionen.
    * Använd det **app-namn** som du använde för att skapa webbappen genom att använda app service på Linux.

2. Distribuera projektet genom att överföra ändringarna till Azure:

    ```console
    git push azure main
    ```

    Du bör se utdata som liknar följande exempel:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'main'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Lägg till Application Insights för att övervaka dina webb program

1. [Skapa en Application Insights-resurs](./create-new-resource.md).

2. Kopiera **iKey** -värdet för resursen Application Insights och ange följande inställningar i dina webbappar:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    När appens inställningar ändras startar webbplatsen om automatiskt. När de nya inställningarna har tillämpats körs profiler omedelbart i två minuter. Profileraren körs sedan i två minuter varje timma.

3. Generera en del trafik till din webbplats. Du kan generera trafik genom att uppdatera plats **om** sidan några gånger.

4. Vänta två till fem minuter innan händelserna sammanställs till Application Insights.

5. Bläddra till fönstret Application Insights **prestanda** i Azure Portal. Du kan Visa profiler-spår längst ned till höger i fönstret.

    ![Visa profiler spår](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Nästa steg
Om du använder anpassade behållare som finns i Azure App Service följer du anvisningarna i [ aktivera Service profiler för ett behållar ASP.net Core program](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) för att aktivera Application Insights profiler.

Rapportera eventuella problem eller förslag till Application Insights GitHub-databasen: [ApplicationInsights-profilerare-AspNetCore: problem](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).