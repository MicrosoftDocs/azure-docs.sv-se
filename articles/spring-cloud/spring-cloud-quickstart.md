---
title: Snabbstart – distribuera ditt första Azure Spring Cloud-program
description: I den här snabb starten distribuerar vi ett våren Cloud-program till Azure våren-molnet.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 416f997475e35f8e784679ca0826f7af38d756c7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880312"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Snabb start: Distribuera ditt första Azure våren Cloud-program

::: zone pivot="programming-language-csharp"
Den här snabb starten förklarar hur du distribuerar ett enkelt Azure våren Cloud-mikrotjänstprogram som kan köras på Azure.

>[!NOTE]
> Steeltoe support för Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Mer information om stöd för för hands versionerna finns i [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller filen [supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md).

Genom att följa den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Generera ett grundläggande Steeltoe .NET Core-projekt
> * Etablera en Azure våren Cloud Service-instans
> * Bygg och distribuera appen med en offentlig slut punkt
> * Strömma loggar i realtid

Den program kod som används i den här snabb starten är en enkel app som skapats med en .NET Core Web API Project-mall. När du har slutfört det här exemplet är programmet tillgängligt online och kan hanteras via Azure Portal och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Azure våren Cloud Service stöder .NET Core 3,1 och senare versioner.
* [Azure CLI version 2.0.67 eller senare](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Installera Azure CLI-tillägg

Kontrol lera att din Azure CLI-version är 2.0.67 eller senare:

```azurecli
az --version
```

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Logga in på Azure

1. Logga in på Azure CLI

    ```azurecli
    az login
    ```

1. Om du har mer än en prenumeration väljer du den som du vill använda för den här snabb starten.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Generera ett Steeltoe .NET Core-projekt

I Visual Studio skapar du ett ASP.NET Core webb program med namnet "Hello-World" med en mall för API-projekt. Observera att det kommer att finnas en automatiskt genererad WeatherForecastController som kommer att vara vår test slut punkt senare.

1. Skapa en mapp för projekt käll koden och generera projektet.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Navigera till projekt katalogen.

   ```console
   cd hello-world
   ```

1. Redigera *appSettings.jspå* fil för att lägga till följande inställningar:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Ändra också logg nivån för `Microsoft` kategorin från tillappsettings.jspå `Warning` `Information` . Den här ändringen säkerställer att loggar skapas när du visar strömmande loggar i ett senare steg.

   *appsettings.jspå* filen ser nu ut ungefär som i följande exempel:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Lägg till beroenden och en `Zip` uppgift i *. CSPROJ* -filen:

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Paketen är för Steeltoe-tjänst identifiering och klient biblioteket för Azure våren Cloud. `Zip`Uppgiften är för distribution till Azure. När du kör `dotnet publish` kommandot genereras binärfilerna i *publicerings* mappen och den här uppgiften zips i en *. zip* -  fil som du överför till Azure.

3. I *program.cs* -filen lägger du till ett `using` direktiv och kod som använder klient biblioteket för Azure våren Cloud:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. I *startup.cs* -filen lägger du till ett `using` direktiv och kod som använder identifiering av Steeltoe-tjänsten i slutet av `ConfigureServices` `Configure` metoderna och:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Skapa projektet för att se till att det inte finns några kompileringsfel.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Etablera en tjänst instans

Följande procedur skapar en instans av Azure våren Cloud med hjälp av Azure Portal.

1. Öppna [Azure-portalen](https://ms.portal.azure.com/). 

1. Sök efter *Azure våren Cloud* i den översta sökrutan.

1. Välj *Azure våren Cloud* från resultaten.

   ![ASC-ikon start](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. På sidan Azure våren Cloud väljer du **+ Lägg till**.

   ![ASC-ikon Lägg till](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Fyll i formuläret på sidan Azure våren Cloud **create** .  Läs igenom följande riktlinjer:

   * **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.
   * **Resurs grupp**: skapa en ny resurs grupp. Det namn som du anger här kommer att användas i senare steg som **\<resource group name\>** .
   * **Tjänst information/namn**: ange **\<service instance name\>** .  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
   * **Region**: Välj region för din tjänst instans.

   ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Välj **Granska och skapa**.

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

Följande procedur skapar och distribuerar det projekt som du skapade tidigare.

1. Kontrol lera att kommando tolken fortfarande finns i projektmappen.

1. Kör följande kommando för att skapa projektet, publicera binärfilerna och lagra binärfilerna i en *. zip* -fil i projektmappen.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Skapa en app i Azure våren Cloud-instansen med en offentlig slut punkt tilldelad. Använd samma program namn "Hello-World" som du angav i *appsettings.jspå*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Distribuera *. zip* -filen till appen.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry`Alternativet identifierar *DLL* -filen som innehåller programmets start punkt. När tjänsten överför *zip* -filen extraheras alla filer och mappar och försök görs att köra start punkten i *DLL* -filen som anges av `--main-entry` .

   Det tar några minuter att slutföra distributionen av programmet. För att bekräfta att den har distribuerats går du till bladet **appar** i Azure Portal.

## <a name="test-the-app"></a>Testa appen

När distributionen har slutförts öppnar du appen på följande URL:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Appen returnerar JSON-data som liknar följande exempel:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Strömma loggar i realtid

Använd följande kommando för att hämta real tids loggar från appen.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Loggar visas i utdata:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och logg strömmars funktioner.

För avancerade Log Analytics-funktioner går du till fliken **loggar** på menyn för [Azure Portal](https://portal.azure.com/). Loggarna här har en fördröjning på några minuter.
[![Loggar analys ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Den här snabb starten förklarar hur du distribuerar ett enkelt Azure våren Cloud-mikrotjänstprogram som kan köras på Azure. 

Program koden som används i den här självstudien är en enkel app som skapats med våren Initializr. När du har slutfört det här exemplet är programmet tillgängligt online och kan hanteras via Azure Portal.

Den här snabb starten förklarar hur du:

> [!div class="checklist"]
> * Generera ett grundläggande våren Cloud-projekt
> * Etablera en tjänst instans
> * Bygg och distribuera appen med en offentlig slut punkt
> * Strömma loggar i realtid

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* [Installera JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) och Azure våren Cloud-tillägget med kommando: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Skapa ett våren Cloud-projekt

Börja med [våren Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) för att generera ett exempel projekt med rekommenderade beroenden för Azure våren Cloud. Följande bild visar Initializr som kon figurer ATS för det här exempelprojektet.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Sidan Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Klicka på **generera** när alla beroenden har angetts. Ladda ned och packa upp paketet och skapa sedan en webb styrenhet för ett enkelt webb program genom att lägga till följande `src/main/java/com/example/hellospring/HelloController.java` :

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Etablera en instans av Azure våren Cloud

Följande procedur skapar en instans av Azure våren Cloud med hjälp av Azure Portal.

1. Öppna [Azure Portal](https://ms.portal.azure.com/)på en ny flik. 

2. Sök efter *Azure våren Cloud* i den översta sökrutan.

3. Välj *Azure våren Cloud* från resultaten.

    ![ASC-ikon start](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. På sidan Azure våren Cloud klickar du på **+ Lägg till**.

    ![ASC-ikon Lägg till](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure våren Cloud **create** .  Läs igenom följande riktlinjer:
    - **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.
    - **Resurs grupp**: skapa nya resurs grupper för nya resurser är en bra metod. Detta kommer att användas i senare steg som **\<resource group name\>** .
    - **Tjänst information/namn**: ange **\<service instance name\>** .  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - **Plats**: Välj region för din tjänst instans.

    ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på **Granska och skapa**.

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Följande procedur skapar och distribuerar programmet med hjälp av Azure CLI. Kör följande kommando i projektets rot.

1. Bygg projektet med maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Om du inte redan har installerat den) Installera Azure våren Cloud-tillägget för Azure CLI:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Skapa appen med en offentlig slut punkt som tilldelats:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Distribuera jar-filen för appen ( `target\hellospring-0.0.1-SNAPSHOT.jar` i Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Det tar några minuter att slutföra distributionen av programmet. För att bekräfta att den har distribuerats går du till bladet **appar** i Azure Portal. Du bör se status för programmet.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

I följande procedur används IntelliJ-plugin-programmet för Azure våren Cloud för att distribuera exempel appen i IntelliJ-idén.  

### <a name="import-project"></a>Importera projekt

1. Öppna dialog rutan **Välkommen** till IntelliJ och välj **Importera projekt** för att öppna import guiden.
1. Välj `hellospring` mapp.

    ![Importera projekt](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Distribuera appen
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto och välja din prenumeration.  Information om inloggning finns i [installation och inloggning](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ Project Explorer och välj **Azure**  ->  **Deploy till Azure våren Cloud**.

    [![Distribuera till Azure 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Godkänn namnet på appen i fältet **namn** . **Namnet** avser konfigurationen, inte appens namn. Användarna behöver vanligt vis inte ändra det.
1. I text rutan **artefakt** väljer du *hellospring-0.0.1-Snapshot. jar*.
1. I text rutan **prenumeration** verifierar du din prenumeration.
1. I text rutan **fjäder moln** väljer du instansen av Azure våren-molnet som du skapade i [etablera Azure våren Cloud-instansen](./spring-cloud-quickstart-provision-service-instance.md).
1. Ange den **offentliga slut punkten** som ska *aktive ras*.
1. I text rutan **app:** väljer du **skapa app...**.
1. Ange *hellospring* och klicka sedan på **OK**.

    [![Distribuera till Azure OK ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . Plugin-programmet kör kommandot `mvn package` på `hellospring` appen och distribuerar jar som genereras av `package` kommandot.
---

När distributionen är klar kan du komma åt appen på `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Öppna appen från webbläsare ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Strömmande loggar i real tid

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Använd följande kommando för att hämta real tids loggar från appen.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Loggarna visas i resultatet:

[![Strömmande loggar ](media/spring-cloud-quickstart-java/streaming-logs.png)](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och logg strömmars funktioner.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Välj **Azure Explorer** och sedan **våren Cloud**.
1. Högerklicka på appen som körs.
1. Välj **strömmande loggar** från den nedrullningsbara listan.
1. Välj instans.

    [![Välj strömmande loggar ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Direkt uppspelnings loggen visas i fönstret utdata.

    [![Strömmande logg utdata ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

För avancerade loggar analys funktioner går du till fliken **loggar** på menyn för [Azure Portal](https://portal.azure.com/). Loggarna här har en fördröjning på några minuter.

[![Loggar analys ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser som kommer fortsätta att debiteras när de behålls i din prenumeration. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen från portalen eller genom att köra följande kommando i Azure CLI:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Generera ett grundläggande Azure våren Cloud-projekt
> * Etablera en tjänst instans
> * Bygg och distribuera appen med en offentlig slut punkt
> * Strömma loggar i realtid

Om du vill lära dig mer om hur du använder fler Azure våren-funktioner går du vidare till snabb starts serien som distribuerar ett exempel program till Azure våren Cloud:

> [!div class="nextstepaction"]
> [Skapa och kör mikrotjänster](spring-cloud-quickstart-sample-app-introduction.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).