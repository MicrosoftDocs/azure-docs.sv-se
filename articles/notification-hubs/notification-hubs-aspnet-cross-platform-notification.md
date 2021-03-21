---
title: Skicka meddelanden mellan plattformar till användare med Azure Notification Hubs (ASP.NET)
description: Lär dig hur du använder Notification Hubs mallar för att skicka, i en enskild begäran, ett oberoende meddelande som är avsett för alla plattformar.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313889"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Skicka aviseringar mellan plattformar med Azure Notification Hubs

Den här självstudien bygger på föregående självstudie och [skickar meddelanden till specifika användare med hjälp av Azure Notification Hubs]. I den här självstudien beskrivs hur du skickar meddelanden till alla enheter som är registrerade för en speciell autentiserad användare. Den metoden kräver flera förfrågningar för att skicka ett meddelande till varje klient plattform som stöds. Azure Notification Hubs stöder mallar, där du kan ange hur en speciell enhet vill ta emot meddelanden. Den här metoden fören klar sändningen av plattforms oberoende meddelanden.

Den här artikeln visar hur du kan dra nytta av mallar för att skicka ett meddelande som är riktat mot alla plattformar. Den här artikeln använder en enskild begäran om att skicka en plattforms oberoende avisering. Mer detaljerad information om mallar finns i [Notification Hubs översikt][Templates].

> [!IMPORTANT]
> Windows Phone Project 8,1 och tidigare stöds inte i Visual Studio 2019. Mer information finns i [plattforms anpassning och kompatibilitet för Visual Studio 2019-plattformen](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Med Notification Hubs kan en enhet registrera flera mallar med samma tagg. I det här fallet skickas ett inkommande meddelande som riktar sig till taggen i flera meddelanden som skickas till enheten, en för varje mall. Med den här processen kan du visa samma meddelande i flera visuella meddelanden, till exempel båda som en skylt och som ett popup-meddelande i en Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Skicka meddelanden mellan plattformar med mallar

I det här avsnittet används den exempel kod som du skapade i [skicka meddelanden till vissa användare med hjälp av Azure Notification Hubs] själv studie kursen. Du kan [Hämta hela exemplet från GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Om du vill skicka meddelanden mellan plattformar med hjälp av mallar gör du följande:

1. I Visual Studio i **Solution Explorer** expanderar du mappen **kontrollanter** och öppnar sedan filen *RegisterController. cs* .

1. Leta upp kod blocket i `Put` -metoden som skapar en ny registrering och ersätt sedan `switch` innehållet med följande kod:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Den här koden anropar den plattformsspecifika metoden för att skapa en mall registrering i stället för en intern registrering. Eftersom mallens registreringar härleds från interna registreringar behöver du inte ändra befintliga registreringar.

1. Öppna filen **NotificationsController. cs** i **Solution Explorer** i mappen **kontrollanter** . Ersätt metoden `Post` med följande kod:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Den här koden skickar ett meddelande till alla plattformar på samma tid. Du har inte angett någon intern nytto Last. Notification Hubs skapar och levererar rätt nytto Last till varje enhet med det angivna taggnamnet, som anges i de registrerade mallarna.

1. Publicera om ditt webb-API-projekt.

1. Kör klient programmet igen för att kontrol lera att registreringen har slutförts.

1. Du kan också distribuera klient programmet till en andra enhet och sedan köra appen. Ett meddelande visas på varje enhet.

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört den här självstudien får du lära dig mer om Notification Hubs och mallar i de här artiklarna:

* Ett annat scenario för att använda mallar finns i självstudierna [push-meddelanden till vissa Windows-enheter som kör universell Windows-plattform program][Use Notification Hubs to send breaking news] .
* Mer detaljerad information om mallar finns i [Notification Hubs översikt][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Skicka push-meddelanden till specifika användare med hjälp av Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)