---
title: Konfigurera ASP.NET-appar
description: Lär dig hur du konfigurerar en ASP.NET-app i Azure App Service. I artikeln visas de vanligaste konfigurationsåtgärderna.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92744167"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Konfigurera en ASP.NET-app för Azure App Service

> [!NOTE]
> Mer ASP.NET Core finns i [Konfigurera en ASP.net Core app för Azure App Service](configure-language-dotnetcore.md)

ASP.NET-appar måste distribueras till Azure App Service som kompilerade binärfiler. Verktyget Visual Studio Publishing skapar lösningen och distribuerar sedan de kompilerade binärfilerna direkt, medan App Service distributions motor distribuerar kod databasen först och kompilerar sedan binärfilerna.

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET-utvecklare. Om du aldrig har använt Azure App Service, följ själv studie kursen [ASP.net snabb start](quickstart-dotnet-framework.md) och [ASP.net med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Visa .NET Framework runtime-versioner som stöds

I App Service har Windows-instanser redan alla .NET Framework-versioner som stöds installerade. Om du vill visa de .NET Framework Runtime-och SDK-versioner som är tillgängliga går du till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och kör lämpligt kommando i den webbläsarbaserade konsolen:

För CLR 4 Runtime-versioner (.NET Framework 4 och senare):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

Den senaste versionen av .NET Framework kanske inte är omedelbart tillgänglig.

För CLR 2 Runtime-versioner (.NET Framework 3,5 och senare):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Visa aktuell .NET Framework körnings version

Kör följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Värdet `v4.0` innebär att den senaste CLR 4-versionen (.NET Framework 4. x) används. Värdet innebär att `v2.0` en CLR 2-version (.NET Framework 3,5) används.

## <a name="set-net-framework-runtime-version"></a>Ange .NET Framework körnings version

Som standard använder App Service den senaste .NET Framework versionen som stöds för att köra din ASP.NET-app. Kör appen med .NET Framework 3,5 i stället genom att köra följande kommando i [Cloud Shell](https://shell.azure.com) (v 2.0 indikerar CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange appinställningar](configure-common.md#configure-app-settings) och anslutnings strängar utanför appens kod. Sedan kan du komma åt dem i vilken klass som helst med standard mönstret för ASP.NET:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Om du konfigurerar en app-inställning med samma namn i App Service och i *web.config* har App Service värdet företräde framför *web.config* svärdet. Med det lokala *web.config* -värdet kan du felsöka appen lokalt, men App Service-värdet låter appen köras i produkt med produktions inställningar. Anslutnings strängar fungerar på samma sätt. På så sätt kan du behålla dina program hemligheter utanför din kod lagrings plats och få till gång till lämpliga värden utan att ändra koden.

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar för flera projekt

När en Visual Studio-lösning innehåller flera projekt inkluderar publicerings processen i Visual Studio redan att välja det projekt som ska distribueras. När du distribuerar till App Service distributions motor, t. ex. med git eller med ZIP-distribution, med build-automatisering aktiverat, väljer App Service distributions motor den första webbplatsen eller det webb programs projekt som hittas som App Service-appen. Du kan ange vilket projekt App Service ska använda genom att ange `PROJECT` appens inställning. Kör till exempel följande i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Sidan Hämta detaljerade undantag

När ASP.NET-appen genererar ett undantag i Visual Studio-felsökaren visar webbläsaren en detaljerad undantags sida, men i App Service sidan ersätts av ett allmänt fel meddelande. Om du vill visa sidan detaljerad undantag i App Service öppnar du *Web.config* -filen och lägger till `<customErrors mode="Off"/>` elementet under `<system.web>` elementet. Exempel:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Distribuera om din app med den uppdaterade *Web.config*. Nu bör du se samma detaljerade undantags sida.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

Du kan lägga till diagnostiska meddelanden i din program kod med hjälp av [system. Diagnostics. trace](/dotnet/api/system.diagnostics.trace). Exempel: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skapa en ASP.NET-app i Azure med SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
