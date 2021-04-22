---
title: Utveckla med ASP.NET – Azure SignalR Service
description: En snabbstart för att använda Azure SignalR Service för att skapa ett chattrum med ASP.NET ramverket.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 0ac17de3f73424994fc39ef0044d17ef83b501b7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866151"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Snabbstart: Skapa ett chattrum med ASP.NET och SignalR Service

Azure SignalR Service baseras på [SignalR för ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), som inte **är** 100 % kompatibelt med ASP.NET SignalR. Azure SignalR Service implementerat ett ASP.NET SignalR-dataprotokoll baserat på de senaste ASP.NET Core-teknikerna. När du använder Azure SignalR Service för ASP.NET SignalR stöds inte längre vissa ASP.NET SignalR-funktioner, till exempel spelar Inte Azure SignalR upp meddelanden igen när klienten återansluter. Dessutom stöds inte Alltid ram-transport och JSONP. Vissa kodändringar och rätt version av beroende bibliotek krävs för att ASP.NET SignalR-programmet ska fungera SignalR Service.

Se dokumentet om [versionsskillnader för](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) en fullständig lista över funktionsjämförelser mellan ASP.NET SignalR och ASP.NET Core SignalR.

I den här snabbstarten lär du dig att komma igång med ASP.NET och Azure SignalR Service för ett liknande [chattrumsprogram](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET Framework 4.6.1](https://dotnet.microsoft.com/download/dotnet-framework/net461)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Serverlöst* läge stöds inte för ASP.NET SignalR-program. Använd alltid *Standard* eller *Klassisk* för den Azure SignalR Service instansen.

Du kan också skapa Azure-resurser som används i den här [snabbstarten med Skapa SignalR Service skript](scripts/signalr-cli-create-service.md).

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsnet)

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Medan tjänsten distribueras tar vi och arbetar med koden. Klona [exempelappen från GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), ange SignalR Service-anslutningssträngen och kör programmet lokalt.

1. Öppna ett git-terminalfönster. Byt till en mapp där du vill klona exempelprojektet.

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsnet)

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurera och köra webbappen för chattrum

1. Starta Visual Studio och öppna lösningen i mappen *aspnet-samples/ChatRoom/på* den klonade lagringsplatsen.

1. I webbläsaren där Azure Portal öppnas, hittar och väljer du den instans som du skapade.

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

1. Ange nu anslutningssträngen i *web.configfilen.*

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. I *Startup.cs* måste du, i stället för att anropa , anropa och skicka in anslutningssträngen för att programmet ska ansluta till tjänsten i stället för att vara värd `MapSignalR()` för `MapAzureSignalR({YourApplicationName})` SignalR på egen hand. Ersätt `{YourApplicationName}` med namnet på ditt program. Det här namnet är ett unikt namn för att skilja det här programmet från dina andra program. Du kan använda `this.GetType().FullName` som värde.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Du måste också referera till tjänst-SDK innan du använder dessa API:er. Öppna **verktygs-| NuGet Package Manager-| Package Manager-konsolen** och kör kommandot:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Förutom dessa ändringar förblir allt annat detsamma. Du kan fortfarande använda hubbgränssnittet som du redan är bekant med för att skriva affärslogik.

    > [!NOTE]
    > I implementeringen exponeras `/signalr/negotiate` en slutpunkt för förhandling av Azure SignalR Service SDK. Den returnerar ett särskilt förhandlingssvar när klienter försöker ansluta och omdirigera klienter till tjänstslutpunkten som definierats i anslutningssträngen.

1. Tryck <kbd>på F5</kbd> för att köra projektet i felsökningsläge. Du kan se att programmet körs lokalt. I stället för att vara värd för en SignalR-körning av själva programmet, ansluter den nu till Azure SignalR Service.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filter by name...** (Filtrera efter namn...). Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

![Ta bort](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure SignalR Service resurs och använde den med en ASP.NET webbapp. Härnäst får du lära dig hur du utvecklar realtidsprogram med Azure SignalR Service med ASP.NET Core.

> [!div class="nextstepaction"]
> [Azure SignalR Service med ASP.NET Core](./signalr-quickstart-dotnet-core.md)
