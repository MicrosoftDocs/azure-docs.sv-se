---
title: Skicka push-meddelanden med Azure Notification Hubs och Node.js
description: Lär dig hur du använder Notification Hubs för att skicka push-meddelanden från ett Node.js program.
keywords: push-meddelande, push-meddelanden, node.js push, iOS-push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: eb41593938c670199be38140118f276142ceed43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453231"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Skicka push-meddelanden med Azure Notification Hubs och Node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan du skapa ett kostnads fritt utvärderings konto på bara några minuter via den [kostnads fria utvärderings versionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Den här guiden visar hur du skickar push-meddelanden med hjälp av Azure Notification Hubs direkt från ett [Node.js](https://nodejs.org) program.

Scenarierna som omfattas inkluderar sändning av push-meddelanden till program på följande plattformar:

- Android
- iOS
- Universell Windows-plattform
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs tillhandahåller en lättanvänd, skalbar infrastruktur med flera plattformar som du kan använda för att skicka push-meddelanden till mobila enheter. Mer information om tjänst infrastrukturen finns på sidan [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)) .

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js program

Det första steget i den här självstudien är att skapa ett nytt tomt Node.js-program. Anvisningar om hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till Azure Web Site][nodejswebsite], [Node.js moln tjänst][Node.js Cloud Service] med Windows PowerShell eller [webbplats med WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurera programmet så att det använder Notification Hubs

Om du vill använda Azure Notification Hubs måste du hämta och använda Node.js [Azure-paketet](https://www.npmjs.com/package/azure), som innehåller en inbyggd uppsättning hjälp bibliotek som kommunicerar med rest-tjänsterna för push-meddelanden.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)

1. Använd ett kommando rads gränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **bash** (Linux) och navigera till mappen där du skapade det tomma programmet.
2. Kör `npm install azure-sb` i kommando fönstret.
3. Du kan köra `ls` kommandot eller manuellt `dir` för att kontrol lera att en `node_modules` mapp har skapats.
4. I den mappen hittar du **Azure** -paketet som innehåller de bibliotek som du behöver för att komma åt Notification Hub.

> [!NOTE]
> Du kan lära dig mer om hur du installerar NPM på den officiella [NPM-bloggen](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importera modulen
Använd en text redigerare och Lägg till följande överst i `server.js` filen i programmet:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Konfigurera en Azure Notification Hub-anslutning

`NotificationHubService`Med objektet kan du arbeta med Notification Hub. Följande kod skapar ett `NotificationHubService` objekt för Notification Hub med namnet `hubname` . Lägg till det nära `server.js` filens överkant efter instruktionen att importera Azure-modulen:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Hämta anslutnings `connectionstring` värdet från [Azure portalen] genom att utföra följande steg:

1. Klicka på **Bläddra** i det vänstra navigerings fönstret.
2. Välj **Notification Hubs** och leta upp hubben som du vill använda för exemplet. Du kan referera till [Windows Store komma igång-kursen](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) om du behöver hjälp med att skapa en ny Notification Hub.
3. Välj **inställningar**.
4. Klicka på **åtkomst principer**. Du ser både delade och fullständiga åtkomst anslutnings strängar.

![Azure Portal-Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Du kan också hämta anslutnings strängen med hjälp av **Get-AzureSbNamespace** -cmdleten som tillhandahålls av [Azure PowerShell](/powershell/azure/) eller **Azure SB-namnrymden Visa** med [Azure Command-Line Interface (Azure CLI)](/cli/azure/install-classic-cli).

## <a name="general-architecture"></a>Allmän arkitektur

`NotificationHubService`Objektet visar följande objekt instanser för att skicka push-meddelanden till vissa enheter och program:

- **Android** – Använd `GcmService` objektet, som finns på `notificationHubService.gcm`
- **iOS** – Använd `ApnsService` objektet som är tillgängligt på `notificationHubService.apns`
- **Windows Phone** – Använd `MpnsService` objektet som finns på `notificationHubService.mpns`
- **Universell Windows-plattform** – Använd `WnsService` objektet som finns på `notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Gör så här: skicka push-meddelanden till Android-program

`GcmService`Objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till Android-program. `send`Metoden accepterar följande parametrar:

- **Taggar** – tag-ID. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nytto Last** – meddelandets JSON eller rå data nytto Last.
- **Motringning** – funktionen motringning.

Mer information om nytto Last formatet finns i [nytto Last dokumentationen](https://payload.readthedocs.io/en/latest/).

I följande kod används den `GcmService` instans som exponeras av `NotificationHubService` för att skicka ett push-meddelande till alla registrerade klienter.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Gör så här: skicka push-meddelanden till iOS-program

På samma sätt som med Android-program som beskrivs ovan `ApnsService` tillhandahåller objektet en `send` metod som kan användas för att skicka push-meddelanden till iOS-program. `send`Metoden accepterar följande parametrar:

- **Taggar** – tag-ID. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nytto Last** – meddelandets JSON-eller sträng nytto Last.
- **Motringning** – funktionen motringning.

Mer information om nytto Last formatet finns i avsnittet **meddelande innehåll** i [guiden UserNotifications](https://developer.apple.com/documentation/usernotifications).

I följande kod används den `ApnsService` instans som exponeras av `NotificationHubService` för att skicka ett aviserings meddelande till alla klienter:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Gör så här: skicka push-meddelanden till Windows Phone program

`MpnsService`Objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till Windows Phone program. `send`Metoden accepterar följande parametrar:

- **Taggar** – tag-ID. Om ingen tagg anges skickas meddelandet till alla klienter.
- **Nytto Last** – meddelandets XML-nytto Last.
-   -  TargetName `toast` för popup-meddelanden. `token` för panel meddelanden.
- **NotificationClass** – meddelandets prioritet. Se avsnittet **http-huvud element** i [push-meddelanden från ett Server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) dokument för giltiga värden.
- **Alternativ** – valfria begärandehuvuden.
- **Motringning** – funktionen motringning.

En lista över giltiga `TargetName` `NotificationClass` alternativ och rubrik alternativ finns i [push-meddelanden från en server](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41)) sida.

Följande exempel kod använder den `MpnsService` instans som exponeras av `NotificationHubService` för att skicka ett push-meddelande för popup:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Gör så här: skicka push-meddelanden till Universell Windows-plattform-program (UWP)

`WnsService`Objektet innehåller en `send` metod som kan användas för att skicka push-meddelanden till universell Windows-plattform program.  `send`Metoden accepterar följande parametrar:

- **Taggar** – tag-ID. Om ingen tagg anges skickas meddelandet till alla registrerade klienter.
- **Nytto Last** – nytto lasten i XML-meddelandet.
- **Typ** – meddelande typen.
- **Alternativ** – valfria begärandehuvuden.
- **Motringning** – funktionen motringning.

En lista över giltiga typer och begärandehuvuden finns i begäran om [Push Notification Service och svars rubriker](/previous-versions/windows/apps/hh465435(v=win.10)).

I följande kod används den `WnsService` instans som exponeras av `NotificationHubService` för att skicka ett push-meddelande med popup-meddelanden till en UWP-app:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Nästa steg

Med exempel kodfragmenten ovan kan du enkelt skapa tjänst infrastruktur för att leverera push-meddelanden till en mängd olika enheter. Nu när du har lärt dig grunderna för att använda Notification Hubs med node.js kan du följa dessa länkar för att lära dig mer om hur du kan utöka dessa funktioner ytterligare.

- Se MSDN-referensen för [Azure Notification Hubs](/previous-versions/azure/azure-services/jj927170(v=azure.100)).
- Besök [Azure SDK för Node] -lagringsplatsen på GitHub för fler exempel och implementerings information.

[Azure SDK för Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
