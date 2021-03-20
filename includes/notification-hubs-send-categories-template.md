---
title: ta med fil
description: ta med fil
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96028198"
---
I det här avsnittet skickar du de senaste nyheterna som taggade mallmeddelanden från en .NET-konsolapp.

1. Skapa en ny Visual C#-konsolapp i Visual Studio:
    1. På menyn väljer du **fil**  >  **nytt**  >  **projekt**.
    1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# i listan över mallar och väljer **sedan nästa**.
    1. Ange ett namn för appen.
    1. För **lösning** väljer **du Lägg till i lösning** och väljer **skapa** för att skapa projektet.

1. Välj **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen** och kör sedan följande kommando i konsol fönstret:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Den här åtgärden lägger till en referens till Azure Notification Hubs SDK med hjälp av [Microsoft. Azure. NotificationHubs] -paketet.

1. Öppna filen *program. cs* och Lägg till följande- `using` instruktion:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. I `Program`-klassen, lägger du till följande metod eller ersätter den om den redan finns:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Den här koden skickar ett mallmeddelande för var och en av de sex taggarna i strängmatrisen. Användning av taggar garanterar att enheter endast får meddelanden för registrerade kategorier.

1. I föregående kod, ersätter du `<hub name>`- och `<connection string with full access>`-platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultFullSharedAccessSignature* från instrumentpanelen i din meddelandehubb.

1. I `Main()` -metoden lägger du till följande rader:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Skapa konsolappen.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft. Azure. NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/