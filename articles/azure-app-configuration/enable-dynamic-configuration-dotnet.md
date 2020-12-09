---
title: '.NET Framework själv studie kurs: dynamisk konfiguration i Azure App konfiguration'
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för .NET Framework appar med hjälp av Azure App konfiguration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932769"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Självstudie: Använd dynamisk konfiguration i en .NET Framework app

App Configuration .NET-klient biblioteket stöder uppdatering av en uppsättning konfigurations inställningar på begäran utan att ett program startas om. Detta kan implementeras genom att först hämta en instans av `IConfigurationRefresher` från alternativen för konfigurationsprovidern och sedan anropa den `TryRefreshAsync` instansen var som helst i din kod.

För att hålla inställningarna uppdaterade och undvika för många anrop till konfigurations arkivet används ett cacheminne för varje inställning. Tills det cachelagrade värdet för en inställning har gått ut uppdateras inte värdet, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder, men den kan åsidosättas om det behövs.

Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den app som introducerades i snabb starterna. Innan du fortsätter måste du först [skapa en .NET Framework-app med app-konfigurationen](./quickstart-dotnet-app.md) .

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera din .NET Framework-app för att uppdatera konfigurationen som svar på ändringar i ett konfigurations lager för appar.
> * Mata in den senaste konfigurationen i ditt program.
## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 eller senare](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **Configuration Explorer**  >  **+ skapa**  >  **nyckel värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

8. Välj **Använd**.

## <a name="create-a-net-framework-console-app"></a>Skapa en .NET Framework-konsol-app

1. Starta Visual Studio och välj **fil**  >  **nytt**  >  **projekt**.

1. I **skapa ett nytt projekt** filtrerar du på projekt typen **konsol** och klickar på **konsol program (.NET Framework)**. Klicka på **Nästa**.

1. Ange ett projekt namn i **Konfigurera ditt nya projekt**. Under **ramverk** väljer du **.NET Framework 4.7.1** eller högre. Klicka på **Skapa**.

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration
1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du och lägger till *Microsoft.Extensions.Configuration. AzureAppConfiguration* NuGet-paket till ditt projekt. Om du inte hittar det markerar du kryss rutan **Inkludera för hands version** .

1. Öppna *program.cs* och Lägg till en referens till .net Core app Configuration-providern.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Lägg till två variabler för att lagra konfigurations relaterade objekt.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Uppdatera `Main` metoden för att ansluta till app-konfigurationen med de angivna uppdaterings alternativen.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    `ConfigureRefresh`Metoden används för att ange inställningarna som används för att uppdatera konfigurations data med appens konfigurations Arkiv när en uppdatering aktive ras. En instans av `IConfigurationRefresher` kan hämtas genom att anropa `GetRefresher` metoden på de alternativ som ges till `AddAzureAppConfiguration` metoden, och `TryRefreshAsync` metoden i den här instansen kan användas för att utlösa en uppdaterings åtgärd var som helst i koden.

    > [!NOTE]
    > Standard-cachens förfallo tid för en konfigurations inställning är 30 sekunder, men kan åsidosättas genom `SetCacheExpiration` att anropa metoden på Options-initieraren som skickades som ett argument till `ConfigureRefresh` metoden.

1. Lägg till en metod `PrintMessage()` med namnet som utlöser en manuell uppdatering av konfigurations data från App-konfigurationen.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **ConnectionString** och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Starta om Visual Studio om du vill att ändringen ska börja gälla. 

1. Tryck på Ctrl + F5 för att skapa och köra-konsol programmet.

    ![App-starta lokalt](./media/dotnet-app-run.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **Configuration Explorer** och uppdatera värdena för följande nycklar:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

1. Gå tillbaka till det program som körs, tryck på RETUR-tangenten för att utlösa en uppdatering och skriv ut det uppdaterade värdet i kommando tolken eller PowerShell-fönstret.

    ![Uppdatera lokala appar](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Eftersom cachens förfallo tid var 10 sekunder med `SetCacheExpiration` metoden när konfigurationen för uppdaterings åtgärden angavs, uppdateras värdet för konfigurations inställningen endast om minst 10 sekunder har förflutit sedan den senaste uppdateringen för den inställningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat .NET Framework-appen för dynamisk uppdatering av konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
