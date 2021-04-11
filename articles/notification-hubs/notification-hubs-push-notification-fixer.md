---
title: Diagnostisera ignorerade aviseringar i Azure Notification Hubs
description: Lär dig hur du diagnostiserar vanliga problem med borttagna meddelanden i Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455033"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostisera ignorerade aviseringar i Azure Notification Hubs

En vanlig fråga om Azure Notification Hubs är hur du felsöker när meddelanden från ett program inte visas på klient enheter. Kunder vill veta var och varför meddelanden släpptes och hur du åtgärdar problemet. Den här artikeln visar varför meddelanden kan tas bort eller inte tas emot av enheter. Det förklarar också hur du fastställer rotor saken.

Det är viktigt att först förstå hur Notification Hubs Push-meddelanden till en enhet.

![Notification Hubs arkitektur][0]

I ett typiskt skicka meddelande flöde skickas meddelandet från *Server delen* till Notification Hubs. Notification Hubs bearbetar alla registreringar. Det tar hänsyn till de konfigurerade taggarna och tagg uttryck för att fastställa mål. Mål är de registreringar som behöver ta emot push-meddelanden. Dessa registreringar kan omfatta någon av våra plattformar som stöds: Android, Baidu (Android-enheter i Kina), Fire OS (Amazon) iOS, Windows och Windows Phone.

När målen har upprättats Notification Hubs Push-meddelanden till *Push Notification Service* för enhets plattformen. Exempel på detta är APNs (Apple Push Notification Service) för iOS och macOS och Firebase Cloud Messaging (FCM) för Android-enheter. Notification Hubs Push-meddelanden delas över flera batchar med registreringar. Den autentiserar med respektive Push Notification Service, baserat på de autentiseringsuppgifter som du angav i Azure Portal under **Konfigurera Notification Hub**. Push Notification Service vidarebefordrar sedan meddelandena till respektive *klient enheter*.

Det sista benet med meddelande leverans är mellan plattformens Push Notification Service och enheten. Meddelande leveransen kan inte utföras i någon av de fyra stegen i Push Notification-processen (klient, programserverprogram, Notification Hubs och plattformens Push Notification Service). Mer information om Notification Hubs arkitekturen finns i [Notification Hubs översikt].

Ett fel vid leverans av aviseringar kan inträffa under den första fasen för test/mellanlagring. Borttagna meddelanden i det här skedet kan tyda på ett konfigurations problem. Om det uppstår ett fel med att leverera meddelanden i produktion, kan vissa eller alla meddelanden släppas. Ett problem med en djupare program-eller meddelande mönster anges i det här fallet.

Nästa avsnitt tittar på scenarier där meddelanden kan släppas, från vanliga till sällsynta.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs felaktig konfiguration

Om du vill skicka meddelanden till respektive Push Notification Service måste Notification Hubs autentisera sig självt i program kontexten. Du måste skapa ett Developer-konto med mål plattformens meddelande tjänst (Microsoft, Apple, Google osv.). Sedan måste du registrera ditt program med operativ systemet där du får en token eller nyckel som du använder för att arbeta med mål-PNS.

Du måste lägga till autentiseringsuppgifter för plattformen i Azure Portal. Om inga meddelanden når enheten är det första steget att se till att rätt autentiseringsuppgifter konfigureras i Notification Hubs. Autentiseringsuppgifterna måste matcha det program som har skapats under ett plattformsspecifik utvecklings konto.

Stegvisa instruktioner för att slutföra den här processen finns i [Kom igång med Azure Notification Hubs].

Här följer några vanliga felkonfigurationer att söka efter:

### <a name="notification-hub-name-location"></a>Namn plats för Notification Hub

Kontrol lera att namnet på meddelande hubben (utan skrivfel) är detsamma på var och en av dessa platser:

* Var du registrerar från klienten
* Där du skickar meddelanden från Server delen
* Där du konfigurerade autentiseringsuppgifterna för Push Notification Service

Se till att du använder rätt konfigurations strängar för signaturer för delad åtkomst på klienten och Server delen. I allmänhet måste du använda **DefaultListenSharedAccessSignature** på klienten och **DefaultFullSharedAccessSignature** på Server sidan. Detta ger behörighet att skicka meddelanden till Notification Hubs.

### <a name="apn-configuration"></a>Konfiguration av APN

Du måste ha två olika hubbar: ett för produktion och en för testning. Du måste överföra det certifikat du använder i en sandbox-miljö till ett separat nav än det certifikat/den hubb som du kommer att använda i produktionen. Försök inte att överföra olika typer av certifikat till samma hubb. Aviserings fel kan uppstå.

Om du råkar överföra olika typer av certifikat till samma hubb bör du ta bort hubben och börja om med en ny hubb. Om du av någon anledning inte kan ta bort navet måste du minst ta bort alla befintliga registreringar från hubben.

### <a name="fcm-configuration"></a>FCM-konfiguration

1. Kontrol lera att *Server nyckeln* du hämtade från Firebase matchar Server nyckeln du registrerade i Azure Portal.

   ![Firebase-server nyckel][3]

2. Se till att du har konfigurerat **projekt-ID** på klienten. Du kan hämta värdet för **projekt-ID** från Firebase-instrumentpanelen.

   ![Projekt-ID för Firebase][1]

## <a name="application-issues"></a>Program problem

### <a name="tags-and-tag-expressions"></a>Taggar och tagg uttryck

Om du använder taggar eller tagg uttryck för att segmentera din mål grupp, är det möjligt att det inte går att hitta något mål när du skickar meddelandet. Det här felet baseras på de angivna taggarna eller tagg uttrycken i ditt sändnings anrop.

Granska dina registreringar för att se till att taggarna matchar när du skickar ett meddelande. Verifiera sedan meddelande kvittot från enbart de klienter som har dessa registreringar.

Anta till exempel att alla dina registreringar med Notification Hubs använder taggen "politiken". Om du sedan skickar ett meddelande med taggen "idrotts" skickas meddelandet inte till någon enhet. Ett komplext ärende kan omfatta tagg uttryck där du har registrerat med hjälp av "tag A" *eller* "tag b", men du riktade "tag A && tagg b." I avsnittet om själv diagnos tips senare i artikeln visas hur du granskar dina registreringar och deras taggar.

### <a name="template-issues"></a>Mal lin ställningar

Om du använder mallar, se till att du följer rikt linjerna som beskrivs i [mallar].

### <a name="invalid-registrations"></a>Ogiltiga registreringar

Om Notification Hub har kon figurer ATS korrekt och taggar eller tagg uttryck användes korrekt, hittas giltiga mål. Meddelanden ska skickas till dessa mål. Notification Hubs utlöses sedan flera bearbetnings grupper parallellt. Varje batch skickar meddelanden till en uppsättning registreringar.

> [!NOTE]
> Eftersom Notification Hubs bearbetar batchar parallellt, garanteras inte i vilken ordning meddelanden levereras.

Notification Hubs är optimerad för meddelande leverans modellen "högst en gång". Vi försöker deduplicera, så att inga meddelanden levereras mer än en gång till en enhet. Registreringarna kontrol leras för att säkerställa att endast ett meddelande skickas per enhets identifierare innan det skickas till Push Notification Service.

Varje batch skickas till Push Notification Service, som i sin tur godkänner och validerar registreringarna. Under den här processen är det möjligt att Push Notification Service identifierar ett fel med en eller flera registreringar i en batch. Push Notification Service returnerar sedan ett fel för att Notification Hubs och processen stoppas. Push Notification Service släpper batchen helt. Detta gäller särskilt för APN, som använder ett TCP-Stream-protokoll.

I det här fallet tas den felaktiga registreringen bort från databasen. Sedan försöker vi leverera meddelandet igen för resten av enheterna i batchen.

Om du vill ha mer information om det misslyckade leverans försöket vid en registrering kan du använda Notification Hubs REST-API [: er per meddelande telemetri: Hämta telemetri för aviseringar](/rest/api/notificationhubs/get-notification-message-telemetry) och [PNS feedback](/previous-versions/azure/reference/mt705560(v=azure.100)). Exempel kod finns i [Skicka rest-exemplet](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problem med Push Notification Service

När Push Notification Service tar emot meddelandet, skickar den meddelandet till enheten. I det här läget har Notification Hubs ingen kontroll över leveransen av meddelandet till enheten.

Eftersom plattforms meddelande tjänsterna är robusta är det bara för aviseringar att kontakta enheterna på några sekunder. Om Push Notification Service är begränsad använder Notification Hubs en exponentiell back av-strategi. Om Push Notification-tjänsten inte går att komma åt i 30 minuter, finns det en princip på plats för att gå ut och ta bort meddelandena permanent.

Om en Push Notification-tjänst försöker leverera ett meddelande men enheten är offline, lagras meddelandet av Push Notification Service. Den lagras bara under en begränsad tids period. Meddelandet levereras till enheten när enheten blir tillgänglig.

Varje app lagrar bara ett senaste meddelande. Om flera meddelanden skickas medan en enhet är offline, kommer varje nytt meddelande att ta bort den sista. Att endast behålla den senaste aviseringen kallas *sammanslagning* i APN och *Komprimera* i FCM. (FCM använder en komprimerad nyckel.) När enheten är offline under en längre tid, ignoreras meddelanden som lagrats för enheten. Mer information finns i [Översikt över APN] och [om FCM-meddelanden].

Med Notification Hubs kan du skicka en sammanslagnings nyckel via ett HTTP-huvud med hjälp av det allmänna SendNotification-API: et. För .NET SDK skulle du till exempel använda `SendNotificationAsync` . SendNotification-API: et tar också emot HTTP-huvuden som skickas som är till respektive Push Notification Service.

## <a name="self-diagnosis-tips"></a>Tips om själv diagnos

Här följer sökvägar för att diagnosticera rotor saken till de borttagna meddelandena i Notification Hubs.

### <a name="verify-credentials"></a>Verifiera autentiseringsuppgifter

#### <a name="push-notification-service-developer-portal"></a>Push Notification Service Developer-portalen

Verifiera autentiseringsuppgifter i respektive Push Notification Service Developer-portal (APN, FCM, Windows Notification Service osv.). Mer information finns i [Självstudier: skicka meddelanden till universell Windows-plattform appar med hjälp av Azure Notification Hubs](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>Azure Portal

Om du vill granska och matcha autentiseringsuppgifterna med de som du fick från Push Notification Service Developer-portalen går du till fliken **åtkomst principer** i Azure Portal.

![Azure Portal åtkomst principer][4]

### <a name="verify-registrations"></a>Verifiera registreringar

#### <a name="visual-studio"></a>Visual Studio

I Visual Studio kan du ansluta till Azure via Server Explorer för att visa och hantera flera Azure-tjänster, inklusive Notification Hubs. Den här genvägen är främst användbar för din utvecklings-/test miljö.

![Visual Studio-Server Explorer][9]

![Server Explorer](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Du kan visa och hantera alla registreringar i hubben. Registreringarna kan kategoriseras efter plattform, intern eller postmall registrering, tagg, Push Notification Service-identifierare, registrerings-ID och förfallo datum. Du kan också redigera en registrering på den här sidan. Det är särskilt användbart för att redigera taggar.

Högerklicka på din Notification Hub i **Server Explorer** och välj **diagnostisera**. 

![Visual Studio-Server Explorer: diagnosticera menyn](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Följande sida visas:

![Visual Studio: diagnostisera sida](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Växla till sidan **enhets registreringar** :

![Visual Studio: enhets registreringar](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Du kan använda **test sidan skicka** för att skicka ett test meddelande:

![Visual Studio: testa skicka](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Använd Visual Studio för att redigera registreringar endast under utveckling/testning och med ett begränsat antal registreringar. Om du behöver redigera dina registreringar i bulk bör du använda funktionen Exportera och importera registrering som beskrivs i [How to: export och ändring av registreringar i bulk](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Många kunder använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) för att visa och hantera sina Notification Hub. Service Bus Explorer är ett projekt med öppen källkod. 

### <a name="verify-message-notifications"></a>Verifiera meddelande aviseringar

#### <a name="azure-portal"></a>Azure Portal

Om du vill skicka ett test meddelande till dina klienter utan att ha en tjänst Server del igång, går du till **support + fel sökning** och väljer **testa skicka**.

![Testa sändnings funktionen i Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Du kan också skicka test meddelanden från Visual Studio.

![Testa sändnings funktionen i Visual Studio][10]

Mer information om hur du använder Notification Hubs med Visual Studio Server Explorer finns i följande artiklar:

* [Så här visar du enhets registreringar för Notification Hub](/previous-versions/windows/apps/dn792122(v=win.10))
* [Djupgående spel: Visual Studio 2013 uppdatering 2 RC och Azure SDK 2,3]
* [Vi presenterar lanseringen av Visual Studio 2013 Update 3 och Azure SDK 2,4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Felsöka misslyckade meddelanden och granska meddelande resultatet

#### <a name="enabletestsend-property"></a>EnableTestSend-egenskap

När du skickar ett meddelande via Notification Hubs placeras meddelandet först i kö. Notification Hubs fastställer rätt mål och skickar sedan meddelandet till Push Notification Service. Om du använder REST API eller någon av klient-SDK: erna, innebär det att sändnings anropet returneras endast att meddelandet står i kö med Notification Hubs. Det ger inte insikter om vad som hände när Notification Hubs slutligen skickar meddelandet till Push Notification Service.

Om meddelandet inte kommer till klient enheten kan ett fel uppstå när Notification Hubs försökte leverera det till Push Notification Service. Nytto Last storleken kan till exempel överskrida det högsta tillåtna antalet i Push Notification Service, eller så kan autentiseringsuppgifterna som kon figurer ATS i Notification Hubs vara ogiltiga.

Du kan använda egenskapen [EnableTestSend] för att få insikter om fel i Push Notification Service. Den här egenskapen aktive ras automatiskt när du skickar test meddelanden från portalen eller Visual Studio-klienten. Du kan använda den här egenskapen för att se detaljerad fel söknings information och även via API: er. För närvarande kan du använda den i .NET SDK. Den kommer att läggas till i alla klient-SDK: er slutligen.

Om du vill använda `EnableTestSend` egenskapen med rest-anropet lägger du till en frågesträngparametern med namnet *test* i slutet av ditt sändnings anrop. Exempel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK-exempel

Här är ett exempel på hur du använder .NET SDK för att skicka ett inbyggt popup-meddelande (popup):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

I slutet av körningen har du `result.State` bara tillstånd `Enqueued` . Resultaten ger inte några insikter om vad som hände med push-meddelandet.

Sedan kan du använda den `EnableTestSend` booleska egenskapen. Använd `EnableTestSend` egenskapen när du initierar `NotificationHubClient` för att få en detaljerad status om Push Notification Service-fel som inträffar när meddelandet skickas. Sändnings anropet tar ytterligare tid att returnera eftersom det först behöver Notification Hubs för att leverera meddelandet till Push Notification Service.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Exempelutdata

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Det här meddelandet anger att autentiseringsuppgifterna som kon figurer ATS i Notification Hubs är ogiltiga eller att det är problem med registreringarna i hubben. Ta bort den här registreringen och låt klienten återskapa registreringen innan meddelandet skickas.

> [!NOTE]
> Användningen av `EnableTestSend` egenskapen är kraftigt begränsad. Använd bara det här alternativet i en utvecklings-/test miljö och med en begränsad uppsättning registreringar. Fel söknings meddelanden skickas till 10 enheter. Det finns också en gräns för att bearbeta fel söknings sändningar, vid 10 per minut.

### <a name="review-telemetry"></a>Granska telemetri

#### <a name="azure-portal"></a>Azure Portal

I portalen kan du få en snabb överblick över all aktivitet i Notification Hub.

1. På fliken **Översikt** kan du se en sammanställd vy över registreringar, meddelanden och fel per plattform.

   ![Instrument panel för Notification Hubs översikt][5]

2. På fliken **övervaka** kan du lägga till många andra plattformsspecifika mått för ett djupare utseende. Du kan titta närmare på fel som returneras när Notification Hubs försöker skicka meddelandet till Push Notification Service.

   ![Azure Portal aktivitets logg][6]

3. Börja med att granska **inkommande meddelanden**, **registrerings åtgärder** och **lyckade aviseringar**. Gå sedan till fliken per plattform för att granska fel som är specifika för Push Notification Service.

4. Om autentiseringsinställningarna för Notification Hub är felaktiga visas meddelandet **PNS autentiseringsfel** . Det är en korrekt uppgift att kontrol lera autentiseringsuppgifterna för Push Notification Service.

#### <a name="programmatic-access"></a>Programmässig åtkomst

Mer information om programmerings åtkomst finns i [program mässig åtkomst](/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Flera funktioner för telemetri, t. ex. export och import av registreringar och telemetri via API: er, är bara tillgängliga på standard tjänst nivån. Om du försöker använda dessa funktioner från den kostnads fria eller grundläggande tjänst nivån får du ett undantags meddelande om du använder SDK: n. Du får ett HTTP 403-fel (förbjudet) om du använder funktionerna direkt från REST-API: erna.
>
> Om du vill använda telemetri-relaterade funktioner måste du först se till att du använder standard tjänst nivån i Azure Portal.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Översikt över Notification Hubs]: notification-hubs-push-notification-overview.md
[Kom igång med Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Mallar]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[APN-översikt]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Om FCM-meddelanden]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Djupgående spel: Visual Studio 2013 uppdatering 2 RC och Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Vi presenterar lanseringen av Visual Studio 2013 Update 3 och Azure SDK 2,4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
