---
title: Integrera Azure App konfiguration med en kontinuerlig integrering och leverans-pipeline
description: Lär dig att implementera kontinuerlig integrering och leverans med Azure App konfiguration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: d076bdf09626ec9ed08fcf43b95fc63d2f4a7dd7
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928457"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

Den här artikeln förklarar hur du använder data från Azure App konfiguration i en kontinuerlig integrering och ett kontinuerligt distributions system.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Använd app-konfiguration i din Azure DevOps-pipeline

Om du har en Azure DevOps-pipeline kan du hämta nyckel värden från App-konfigurationen och ange dem som variabler för aktiviteter. [Tillägget Azure App Configuration DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) är en modul för tillägg som tillhandahåller den här funktionen. Följ anvisningarna för att använda tillägget i en aktivitetssekvens för att bygga eller släppa.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuera konfigurations data för appar med ditt program

Programmet kanske inte kan köras om det är beroende av Azure App konfiguration och inte kan komma åt det. Förbättra återhämtningen av ditt program genom att paketera konfigurations data i en fil som distribueras med programmet och läsas in lokalt när programmet startas. Den här metoden garanterar att ditt program har standardinställnings värden vid start. Dessa värden skrivs över av eventuella nyare ändringar i konfigurations arkivet för appar när det är tillgängligt.

Med hjälp av [export](./howto-import-export-data.md#export-data) funktionen i Azure App konfiguration kan du automatisera processen med att hämta aktuella konfigurations data som en enda fil. Du kan sedan bädda in den här filen i ett build-eller distributions steg i en pipeline för kontinuerlig integrering och distribution (CI/CD).

I följande exempel visas hur du inkluderar konfigurations data för appar som ett build-steg för webbappen som introduceras i snabb starterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

### <a name="prerequisites"></a>Krav

Om du skapar lokalt kan du hämta och installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte redan gjort det.

Om du vill göra en moln version kan du till exempel se till att [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) är installerat i versions systemet med Azure DevOps.

### <a name="export-an-app-configuration-store"></a>Exportera ett konfigurations Arkiv för appen

1. Öppna din *. CSPROJ* -fil och Lägg till följande skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Öppna *program.cs* och uppdatera `CreateWebHostBuilder` metoden för att använda den exporterade JSON-filen genom att anropa `config.AddJsonFile()` metoden.  Lägg `System.Reflection` även till namn området.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **ConnectionString** och ange den till åtkomst nyckeln till appens konfigurations arkiv. 
    Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```console
     dotnet build
    ```

3. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```console
     dotnet run
    ```

4. Öppna ett webbläsarfönster och gå till `http://localhost:5000` , vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du exporterat Azure App konfigurations data som ska användas i en distributions pipeline. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig?view=azure-cli-latest)