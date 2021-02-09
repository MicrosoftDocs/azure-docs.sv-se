---
title: 'Självstudie: Använd dynamisk konfiguration med push-uppdatering i en .NET Core-app'
titleSuffix: Azure App Configuration
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för .NET Core-appar med push-uppdatering
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 553c5081947ad784a8cdae6ad0eb92fc3e2a2c85
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982262"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Självstudie: Använd dynamisk konfiguration med push-uppdatering i en .NET Core-app

App Configuration .NET Core klient bibliotek stöder uppdatering av konfiguration på begäran utan att ett program startas om. Ett program kan konfigureras för att identifiera ändringar i app-konfigurationen med hjälp av en eller båda av följande två metoder.

1. Avsöknings modell: Detta är standard beteendet som använder avsökning för att identifiera ändringar i konfigurationen. När det cachelagrade värdet för en inställning förfaller, kan nästa anrop till `TryRefreshAsync` eller `RefreshAsync` skicka en begäran till servern för att kontrol lera om konfigurationen har ändrats och hämta den uppdaterade konfigurationen om det behövs.

1. Push-modell: detta använder [konfigurations händelser för appar](./concept-app-configuration-event.md) för att identifiera ändringar i konfigurationen. När app-konfigurationen har ställts in för att skicka nyckel värdes ändrings händelser till Azure Event Grid kan programmet använda dessa händelser för att optimera det totala antalet begär Anden som krävs för att hålla konfigurationen uppdaterad. Program kan välja att prenumerera på dessa antingen direkt från Event Grid eller en av de [händelse hanterare som stöds](https://docs.microsoft.com/azure/event-grid/event-handlers) , till exempel en webhook, en Azure Function eller ett Service Bus ämne.

Program kan välja att prenumerera på dessa händelser antingen direkt från Event Grid eller via en webhook eller genom att vidarebefordra händelser till Azure Service Bus. Azure Service Bus SDK tillhandahåller ett API för att registrera en meddelande hanterare som fören klar processen för program som inte har någon HTTP-slutpunkt eller som inte vill avsöka händelse rutnätet efter ändringar kontinuerligt.

Den här självstudien visar hur du kan implementera dynamiska konfigurations uppdateringar i din kod med push-uppdatering. Den bygger på den app som introducerades i snabb starterna. Innan du fortsätter måste du först [skapa en .net Core-app med app-konfigurationen](./quickstart-dotnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera en prenumeration för att skicka konfigurations ändrings händelser från App-konfigurationen till ett Service Bus ämne
> * Konfigurera din .NET Core-app så att den uppdaterar konfigurationen som svar på ändringar i app-konfigurationen.
> * Använd den senaste konfigurationen i ditt program.

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här själv studie kursen installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Konfigurera Azure Service Bus ämne och prenumeration

I den här självstudien används Service Bus-integrering för Event Grid för att förenkla identifieringen av konfigurations ändringar för program som inte vill söka efter ändringar kontinuerligt i appen. Azure Service Bus SDK tillhandahåller ett API för att registrera en meddelande hanterare som kan användas för att uppdatera konfigurationen när ändringar identifieras i appens konfiguration. Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och en prenumeration](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal) för att skapa ett Service Bus-namnområde, ett ämne och en prenumeration.

När resurserna har skapats lägger du till följande miljövariabler. Dessa kommer att användas för att registrera en händelse hanterare för konfigurations ändringar i program koden.

| Tangent | Värde |
|---|---|
| ServiceBusConnectionString | Anslutnings sträng för Service Bus-namnområdet |
| ServiceBusTopic | Namn på Service Bus ämnet |
| ServiceBusSubscription | Namnet på Service Bus-prenumerationen |

## <a name="set-up-event-subscription"></a>Konfigurera händelse prenumeration

1. Öppna konfigurations resursen för appen i Azure Portal och klicka sedan på `+ Event Subscription` i `Events` fönstret.

    ![Konfigurations händelser för app](./media/events-pane.png)

1. Ange ett namn för `Event Subscription` och `System Topic` .

    ![Skapa händelse prenumeration](./media/create-event-subscription.png)

1. Välj `Endpoint Type` som `Service Bus Topic` , Välj Service Bus avsnittet och klicka sedan på `Confirm Selection` .

    ![Service Bus-slutpunkt för händelse prenumeration](./media/event-subscription-servicebus-endpoint.png)

1. Klicka på på `Create` för att skapa händelse prenumerationen.

1. Klicka på på `Event Subscriptions` i `Events` fönstret för att verifiera att prenumerationen har skapats.

    ![Händelse prenumerationer för app Configuration](./media/event-subscription-view.png)

> [!NOTE]
> När du prenumererar på konfigurations ändringar kan ett eller flera filter användas för att minska antalet händelser som skickas till ditt program. Dessa kan konfigureras antingen som [Event Grid prenumerations filter](https://docs.microsoft.com/azure/event-grid/event-filtering) eller [Service Bus prenumerations filter](https://docs.microsoft.com/azure/service-bus-messaging/topic-filters). Ett prenumerations filter kan till exempel användas för att endast prenumerera på händelser för ändringar i en nyckel som börjar med en viss sträng.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registrera händelse hanterare för att läsa in data från App-konfigurationen igen

Öppna *program.cs* och uppdatera filen med följande kod.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

Metoden [SetDirty](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) används för att ange det cachelagrade värdet för nyckel värden registrerade för att uppdatera som smutsig. Detta säkerställer att nästa anrop till `RefreshAsync` eller `TryRefreshAsync` omverifierar de cachelagrade värdena med app-konfigurationen och uppdaterar dem om det behövs.

En slumpmässig fördröjning läggs till innan det cachelagrade värdet markeras som smutsig för att minska eventuella begränsningar om flera instanser uppdateras samtidigt. Den största standard fördröjningen innan det cachelagrade värdet markeras som smutsig är 30 sekunder, men kan åsidosättas genom att en valfri `TimeSpan` parameter skickas till- `SetDirty` metoden.

> [!NOTE]
> För att minska antalet förfrågningar till app-konfigurationen när du använder push-uppdatering är det viktigt att anropa `SetCacheExpiration(TimeSpan cacheExpiration)` med lämpligt värde för `cacheExpiration` parametern. Detta kontrollerar cachens förfallo tid för pull-uppdatering och kan användas som säkerhets nät om det uppstår problem med händelse prenumerationen eller Service Bus prenumerationen. Det rekommenderade värdet är `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **AppConfigurationConnectionString** och ange den som åtkomst nyckel till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Kör följande kommando för att skapa konsol programmet:

    ```console
     dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra appen lokalt:

    ```console
     dotnet run
    ```

    ![Push-uppdatering körs före uppdatering](./media/dotnet-core-app-pushrefresh-initial.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **Configuration Explorer** och uppdatera värdena för följande nycklar:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

1. Vänta i 30 sekunder för att händelsen ska bearbetas och konfigurationen ska uppdateras.

    ![Kör push-uppdatering efter uppdaterat](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat din .NET Core-app för dynamisk uppdatering av konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
