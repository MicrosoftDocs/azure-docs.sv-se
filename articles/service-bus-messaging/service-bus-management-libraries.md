---
title: Skapa Azure Service Bus entiteter program mässigt | Microsoft Docs
description: Den här artikeln förklarar hur du använder dynamiskt eller program mässigt etablera Service Bus namn områden och entiteter.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 97d89db17af9cde3afadee430b3d0c2a434e12c9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210145"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Etablera Service Bus namn områden och entiteter dynamiskt 
Azure Service Bus hanterings bibliotek kan etablera Service Bus namn områden och entiteter dynamiskt. Detta möjliggör komplexa distributioner och meddelande scenarier och gör det möjligt att program mässigt avgöra vilka entiteter som ska etableras. Dessa bibliotek är för närvarande tillgängliga för .NET.

## <a name="supported-functionality"></a>Funktioner som stöds

* Skapa namnrymd, uppdatera, ta bort
* Skapa kö, uppdatera, ta bort
* Att skapa, uppdatera och ta bort ämnen
* Skapa prenumeration, uppdatera, ta bort

## <a name="azuremessagingservicebusadministration-recommended"></a>Azure. Messaging. Service Bus. administration (rekommenderas)
Du kan använda klassen [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) i namn området [Azure. Messaging. Service Bus. administration](/dotnet/api/azure.messaging.servicebus.administration) för att hantera namn områden, köer, ämnen och prenumerationer. Här är exempel koden. Ett fullständigt exempel finns i [CRUD-exempel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. Service Bus. Management 
Du kan använda [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) -klassen i namn området [Microsoft. Azure. Service Bus. Management](/dotnet/api/microsoft.azure.servicebus.management) för att hantera namn områden, köer, ämnen och prenumerationer. Här är exempel koden: 

> [!NOTE]
> Vi rekommenderar att du använder `ServiceBusAdministrationClient` klassen från `Azure.Messaging.ServiceBus.Administration` biblioteket, som är den senaste SDK: n. Mer information finns i det [första avsnittet](#azuremessagingservicebusadministration-recommended). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Det här biblioteket är en del av det Azure Resource Manager-baserade kontroll plan SDK: n. 

### <a name="prerequisites"></a>Förutsättningar

För att komma igång med det här biblioteket måste du autentisera med tjänsten Azure Active Directory (Azure AD). Azure AD kräver att du autentiseras som tjänstens huvud namn, vilket ger åtkomst till dina Azure-resurser. Information om hur du skapar ett huvud namn för tjänsten finns i någon av följande artiklar:  

* [Använd Azure Portal för att skapa Active Directory program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Använd Azure CLI för att skapa tjänstens huvudnamn för att få åtkomst till resurser](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

De här självstudierna ger dig ett `AppId` (klient-ID), `TenantId` och `ClientSecret` (autentiseringsnyckel) som används för autentisering av hanterings biblioteken. Du måste ha minst [**Azure Service Bus data ägare**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) eller [**deltagar**](../role-based-access-control/built-in-roles.md#contributor) behörighet för resurs gruppen som du vill köra.

### <a name="programming-pattern"></a>Programmerings mönster

Mönstret för att manipulera eventuella Service Bus resurser följer ett gemensamt protokoll:

1. Hämta en token från Azure AD med hjälp av **Microsoft. IdentityModel. clients. ActiveDirectory** -biblioteket:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Skapa `ServiceBusManagementClient` objektet:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ange de `CreateOrUpdate` angivna värdena som parametrar:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Kör anropet:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>Slutför kod för att skapa en kö
Här är exempel koden för att skapa en Service Bus kö. Ett fullständigt exempel finns i [.net Management-exemplet på GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>Fluent-bibliotek
Ett exempel på hur du använder ett Fluent-bibliotek för att hantera Service Bus entiteter finns i [det här exemplet](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Nästa steg
Se följande referens avsnitt: 

- [Azure. Messaging. Service Bus. administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. Service Bus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Flytande](/dotnet/api/microsoft.azure.management.servicebus.fluent)