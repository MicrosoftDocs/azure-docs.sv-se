---
title: Vad är Azure Notification Hubs?
description: Lär dig hur du lägger till funktioner för push-meddelanden med Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: ba5a329d12735fbddc86ff2e3725a1e7de6d9d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546473"
---
# <a name="what-is-azure-notification-hubs"></a>Vad är Azure Notification Hubs?

Azure Notification Hubs tillhandahåller en lättanvänd och skalad push-motor som gör att du kan skicka meddelanden till valfri plattform (iOS, Android, Windows osv.) från valfri Server del (moln eller lokalt). Notification Hubs fungerar utmärkt för både företags- och konsumentscenarier. Här följer några exempel:

- Skicka meddelanden med senaste nytt till miljontals användare med låg latens.
- Skicka platsbaserade kuponger till intresserade användarsegment.
- Skicka händelserelaterade meddelanden till användare eller grupper med media-/sport-/ekonomi-/spelprogram.
- Skicka innehåll med erbjudanden till program engagera och marknadsföra gentemot kunder.
- Meddela användare om företags händelser som nya meddelanden och arbets objekt.
- Skicka koder för multifaktorautentisering.

## <a name="what-are-push-notifications"></a>Vad är push-meddelanden?

Push-meddelanden är en form av app-till-användare-kommunikation där användare av mobilappar meddelas om viss önskad information, vanligt vis i ett popup-fönster eller i en dialog ruta på en mobil enhet. Användare väljer vanligt vis att visa eller stänga meddelandet. Om du väljer den tidigare öppnar du det mobil program som kommunicerade meddelandet. Vissa meddelanden är tyst levererade i bakgrunden för att appen ska kunna bearbeta och bestämma vad som ska göras.

Push-meddelanden är viktiga för konsumentapparna när det gäller att öka engagemanget och användningen, och för företagsapparna när det gäller att kommunicera uppdaterad affärsinformation. Det är den bästa kommunikationen mellan appar och användare eftersom det är energi snålt för mobila enheter, flexibelt för meddelande avsändare och tillgänglig när motsvarande program inte är aktiva.

> [!NOTE]
> Azure Notification Hubs har inte officiellt stöd för röst över Internet Protocol (VOIP) push-meddelanden. [i den här artikeln beskrivs dock hur du kan använda APN VoIP-meddelanden](voip-apns.md) via Azure Notification Hubs.

Mer information om push-meddelanden för några populära plattformar finns i följande avsnitt:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Hur fungerar push-meddelanden?

Push-meddelanden levereras via plattformsspecifika infrastrukturer som kallas för *plattformsspecifika meddelandesystem* (Platform Notification Systems, PNS). De erbjuder grundläggande Push-funktioner för att leverera ett meddelande till en enhet med en angiven referens och har inget gemensamt gränssnitt. Om du vill skicka ett meddelande till alla kunder över Android-, iOS-och Windows-versionerna av en app måste utvecklaren arbeta separat med Apple Push Notification Service (APN), Firebase Cloud Messaging (FCM) och Windows Notification Service (WNS).

På hög nivå fungerar push-tekniken så här:

1. Ett program vill ta emot ett meddelande, så den kontaktar PNS för den mål plattform där appen körs och begär en unik och tillfällig push-referens. Referens typen är beroende av systemet (till exempel WNS använder URI: er när APN använder token).
2. Klient programmet lagrar den här referensen i appens Server del eller provider.
3. För att skicka ett push-meddelande kontaktar appens Server del PNS med referensen för att rikta en speciell klient app.
4. PNS-systemet vidarebefordrar meddelandet till den enhet som anges av handtaget.

![Arbetsflöde för push-meddelanden](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Utmaningarna med push-meddelanden

PNS:er är kraftfulla. Det krävs ändå mycket arbete av apputvecklaren för att implementera även de mest grundläggande scenarierna för push-meddelanden, som att sända push-meddelanden till olika användarsegment.

Sändning av push-meddelanden kräver komplex infrastruktur som inte är relaterad till programmets huvudsakliga affärs logik. Några av infrastruktur utmaningarna är:

- **Plattformsberoende**
  - Server delen kräver komplex och hårt bevarad plattforms beroende logik för att skicka meddelanden till enheter på olika plattformar, eftersom PNSes inte är enhetliga.
- **Skalning**
  - Per PNS-rikt linjer måste du uppdatera enhetens token på varje app-start. Server delen hanterar en stor mängd trafik och databas åtkomst för att hålla token uppdaterade. När antalet enheter växer till hundratals, tusentals eller miljon tals är kostnaden för att skapa och underhålla infrastrukturen massiv.
  - Merparten av PNS-systemen stöder inte sändning av meddelanden till flera enheter. En enkel sändning till en miljon enheter resulterar i en miljon anrop till PNS-systemen. Att skala den här mängden trafik med minimal svarstid är en intrikat uppgift.
- **Routning**
  - Även om PNSes tillhandahåller ett sätt att skicka meddelanden till enheter, är de flesta app-meddelanden riktade till användare eller intresse grupper. Server delen måste ha ett register för att associera enheter med intresse grupper, användare, egenskaper osv. Den här kostnaden ökar tiden för marknads-och underhållskostnaderna för en app.

## <a name="why-use-azure-notification-hubs"></a>Varför ska man använda Azure Notification Hubs?

Notification Hubs eliminerar all komplexitet som är associerad med att skicka push-meddelanden från din app server del. Dess utskalade multiplattform för en push-meddelandeinfrastruktur reducerar den push-relaterade kodningen och förenklar serverdelen. Med Notification Hubs är enheterna enbart ansvariga för att registrera sina PNS-handtag hos en hubb, medan serverdelen skickar meddelanden till användare eller intressegrupper, så som visas på följande bild:

![Meddelandehubbsdiagram](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs är din färdiga push-motor med följande fördelar:

- **Plattformsoberoende**
  - Stöd för alla större push-plattformar.
  - Ett gemensamt gränssnitt för alla plattformar i plattformsspecifika eller plattformsoberoende format med icke plattformsspecifikt arbete.
  - Hantering av enhetshandtag på ett och samma ställe.
- **Serverdelsoberoende**
  - Molnet eller lokalt.
  - .NET, Node.js, Java, python osv.
- **Stor uppsättning av leveransmönster**
  - Skicka till en eller flera plattformar: du kan direkt sända till miljon tals enheter på olika plattformar med ett enda API-anrop.
  - Skicka till enhet: du kan rikta meddelanden till enskilda enheter.
  - Push till användare: Taggar och mallar hjälper dig att komma åt alla plattforms oberoende enheter för en användare.
  - Push to segment med dynamiska Taggar: med funktionen taggar kan du segmentera enheter och skicka dem till dem utifrån dina behov, oavsett om du skickar till ett segment eller ett segment av segment (t. ex. aktiva och finns i Seattle inte ny användare). I stället för att vara begränsad till publicering – prenumerera kan du uppdatera enhets Taggar var som helst och när som helst.
  - Lokaliserad push: funktionen mallar hjälper till att få lokalisering utan att påverka backend-koden.
  - Tyst push: du kan aktivera push-pull-mönster genom att skicka tysta meddelanden till enheter och utlösa dem när du vill slutföra vissa hämtningar eller åtgärder.
  - Schemalagd push: du kan schemalägga meddelanden som ska skickas när som helst.
  - Direkt push: du kan hoppa över att registrera enheter med Notification Hubs-tjänsten och direkt batchpusha till en lista över enhetshandtag.
  - Personligt anpassad push: enhets-push-variabler hjälper dig att skicka enhetsspecifika personliga push-meddelanden med anpassade nyckel/värde-par.
- **Omfattande telemetri**
  - Telemetri för allmänna push-meddelanden, enheter, fel och åtgärder är tillgängliga både i Azure Portal och program mässigt.
  - Telemetri per meddelande spårar varje push från ditt första begär ande anrop till Notification Hubs tjänsten skickar push-meddelandena.
  - Plattformsspecifikt meddelandesystem feedback kommunicerar all feedback från PNSes för att hjälpa till med fel sökning.
- **Skalbarhet**
  - Skicka snabba meddelanden till miljon tals enheter utan att skapa om eller enhets horisontell partitionering.
- **Säkerhet**
  - SAS (Shared Access Secret) eller federerad autentisering.

## <a name="next-steps"></a>Nästa steg

Kom igång med att skapa och använda en meddelandehubb genom att följa [Självstudier: Skicka meddelanden till mobila program](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
