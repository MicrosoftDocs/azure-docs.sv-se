---
title: Konfigurera ASP.NET Core appar
description: Lär dig hur du konfigurerar en ASP.NET Core-app i de interna Windows-instanserna eller i en fördefinierad Linux-behållare i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5e4351529fb7b6a66f554182a195bc26f79c0e2b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889492"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Konfigurera en ASP.NET Core app för Azure App Service

> [!NOTE]
> Information om ASP.NET i .NET Framework finns i [Konfigurera en ASP.net-app för Azure App Service](configure-language-dotnet-framework.md)

ASP.NET Core appar måste distribueras till Azure App Service som kompilerade binärfiler. Verktyget Visual Studio Publishing skapar lösningen och distribuerar sedan de kompilerade binärfilerna direkt, medan App Service distributions motor distribuerar kod databasen först och kompilerar sedan binärfilerna.

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET Core utvecklare. Om du aldrig har använt Azure App Service, följer du anvisningarna [ASP.net Core snabb start](quickstart-dotnetcore.md) och [ASP.net Core med SQL Database själv studie kursen](tutorial-dotnetcore-sqldb-app.md) först.

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Visa de .NET Core runtime-versioner som stöds

I App Service har Windows-instanser redan alla .NET Core-versioner som stöds installerade. Om du vill visa de .NET Core Runtime och SDK-versioner som är tillgängliga går du till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och kör följande kommando i den webbläsarbaserade konsolen:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>Visa .NET Core-version

Om du vill visa den aktuella .NET Core-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla .NET Core-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>Ange .NET Core-version

::: zone pivot="platform-windows"  

Ange mål ramverket i projekt filen för ditt ASP.NET Core-projekt. Mer information finns i [Välj den .net Core-version som ska användas](/dotnet/core/versions/selection) i .net Core-dokumentationen.

::: zone-end

::: zone pivot="platform-linux"

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ange .net Core-versionen till 3,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Anpassa Bygg automatisering

Om du distribuerar din app med hjälp av git-eller zip-paket med build-automatisering aktiverat, App Service bygga automatiserings steg i följande ordning:

1. Kör anpassat skript om det anges av `PRE_BUILD_SCRIPT_PATH` .
1. Kör `dotnet restore` för att återställa NuGet-beroenden.
1. Kör `dotnet publish` för att skapa en binär för produktion.
1. Kör anpassat skript om det anges av `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Definiera för att köra kommandon för att skapa för bygge `PRE_BUILD_COMMAND` . Definiera för att köra kommandon efter kompilering `POST_BUILD_COMMAND` .

I följande exempel anges de två variablerna för en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa Bygg automatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service kör och skapar ASP.NET Core appar i Linux finns i [Oryx-dokumentation: hur .net Core Apps identifieras och skapas](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ange appinställningar](configure-common.md#configure-app-settings) utanför din appkod. Sedan kan du komma åt dem i valfri klass med hjälp av standard mönstret för ASP.NET Core beroende inmatning:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Om du konfigurerar en app-inställning med samma namn i App Service och i *appsettings.jspå*, till exempel, har App Service värdet företräde framför *appsettings.js* svärdet. Med värdet Local *appsettings.json* kan du felsöka appen lokalt, men App Service-värdet låter appen köras i produktion med produktions inställningar. Anslutnings strängar fungerar på samma sätt. På så sätt kan du behålla dina program hemligheter utanför din kod lagrings plats och få till gång till lämpliga värden utan att ändra koden.

> [!NOTE]
> Observera att [hierarkiska konfigurations data](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) i *appsettings.jspå* nås med hjälp av den `:` avgränsare som är standard för .net Core. Om du vill åsidosätta en viss hierarkisk konfigurations inställning i App Service anger du namnet på appens inställning med samma avgränsat format i nyckeln. Du kan köra följande exempel i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar för flera projekt

När en Visual Studio-lösning innehåller flera projekt inkluderar publicerings processen i Visual Studio redan att välja det projekt som ska distribueras. När du distribuerar till App Service distributions motor, t. ex. med git eller med ZIP-distribution, med build-automatisering aktiverat, väljer App Service distributions motor den första webbplatsen eller det webb programs projekt som hittas som App Service-appen. Du kan ange vilket projekt App Service ska använda genom att ange `PROJECT` appens inställning. Kör till exempel följande i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

ASP.NET Core tillhandahåller en [inbyggd Logging-Provider för App Service](/aspnet/core/fundamentals/logging/#azure-app-service). I *program. cs* för ditt projekt lägger du till providern i programmet via `ConfigureLogging` tilläggs metoden, som du ser i följande exempel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Du kan sedan konfigurera och generera loggar med [standard mönstret för .net Core](/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Mer information om hur du felsöker ASP.NET Core appar i App Service finns i [felsöka ASP.net Core på Azure App service och IIS](/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Sidan Hämta detaljerade undantag

När din ASP.NET Core-app genererar ett undantag i Visual Studio-felsökaren, visar webbläsaren en detaljerad undantags sida, men i App Service sidan ersätts av ett allmänt **HTTP 500-** fel eller **så uppstod ett fel när din begäran bearbetades.** som meddelande. Om du vill visa sidan detaljerad undantag i App Service lägger du till `ASPNETCORE_ENVIRONMENT` appens inställning i din app genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din app-logik behöver veta om användar förfrågningarna är krypterade eller inte, konfigurerar du de vidarebefordrade rubrikerna mellan program i *startup. cs*:

- Konfigurera mellanprogram med [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) för att vidarebefordra- `X-Forwarded-For` och- `X-Forwarded-Proto` rubrikerna i `Startup.ConfigureServices` .
- Lägg till privata IP-adressintervall i de kända nätverken så att mellanprogram kan lita på App Service belastningsutjämnare.
- Anropa metoden [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) i `Startup.Configure` innan du anropar andra mellanprogram.

Om du placerar alla tre element tillsammans ser koden ut som i följande exempel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Mer information finns i [konfigurera ASP.net Core att arbeta med proxyservrar och belastningsutjämnare](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Öppna en SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service på Linux](faq-app-service-linux.md)

::: zone-end
