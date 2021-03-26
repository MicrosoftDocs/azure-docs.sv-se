---
title: Azure Communication Services – kända problem
description: Läs mer om Azure Communication Services
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566803"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Kända problem: klient bibliotek för Azure Communication Services
Den här artikeln innehåller information om begränsningar och kända problem som rör klient biblioteken för Azure Communication Services.

> [!IMPORTANT]
> Det finns flera faktorer som kan påverka kvaliteten på din anrops upplevelse. Se dokumentationen för **[nätverks krav](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** för att lära dig mer om nätverks konfiguration för kommunikations tjänster och testa bästa praxis.


## <a name="javascript-client-library"></a>JavaScript-klientbibliotek

Det här avsnittet innehåller information om kända problem som är associerade med JavaScript-röst och video som anropar klient bibliotek i Azure Communication Services.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>När du har uppdaterat sidan tas inte användaren bort från samtalet omedelbart 
Om användaren är i ett samtal och bestämmer sig för att uppdatera sidan, kanske kommunikations tjänstens klient bibliotek inte kan meddela tjänsten för kommunikations tjänst mediet att den håller på att kopplas från. Tjänsten kommunikations tjänster media kommer inte att ta bort den här användaren omedelbart från anropet, men den väntar på att en användare ska kunna ansluta igen om det är problem med nätverks anslutningen. Användaren tas bort från anropet när medie tjänsten kommer att vara tids gräns.

Vi uppmuntrar utvecklare att bygga upplevelser som inte kräver att slutanvändare uppdaterar sidan i programmet när de deltar i ett samtal. Om användaren kommer att uppdatera sidan, är det bästa sättet att hantera den för appen att återanvända samma kommunikations tjänst användar-ID för användaren när han återgår till programmet efter uppdateringarna.

För andra deltagare i anropet behålls denna användare i anropet för fördefinierad tids period (1-2 minuter). Om användaren kommer att ansluta till samma kommunikations tjänst användar-ID visas han som samma, befintliga objekt i `remoteParticipants` samlingen.
Om tidigare användare skulle skicka videon `videoStreams` behåller insamlingen tidigare strömmande information tills tjänsten kommer att vara tids gräns och ta bort den. i det här scenariot kan programmet välja att observera eventuella nya strömmar som lagts till i samlingen och återge en med högst `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Det går inte att återge flera för hands vyer från flera enheter på webben
Detta är en känd begränsning. Mer information hittar du i [Översikt över anrop till klient bibliotek](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) .

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Det går inte att räkna upp mikrofon-och högtalar enheter i Safari när programmet körs på iOS eller iPad 
Program kan inte räkna upp/välja MIC/talare-enheter (som Bluetooth) i Safari iOS/iPad. Detta är en känd begränsning för operativ system.

Om du använder Safari på macOS kommer din app inte att kunna räkna upp/välja högtalare via kommunikations tjänsterna Enhetshanteraren. I det här scenariot måste enheter väljas via operativ systemet. Om du använder Chrome på macOS kan appen räkna upp/välja enheter via kommunikations tjänsterna Enhetshanteraren.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Ljud anslutningen försvinner när SMS-meddelanden eller samtal tas emot under ett pågående VoIP-anrop
Mobila webbläsare upprätthåller inte anslutningen i bakgrunds tillstånd. Detta kan leda till en försämrad anrops upplevelse om VoIP-anropet avbröts av textmeddelande eller inkommande PSTN-anrop som pushar ditt program till bakgrunden.

<br/>Klient bibliotek: anropar (Java Script)
<br/>Webbläsare: Safari, Chrome
<br/>Operativ system: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Att växla video enheter flera gånger kan orsaka att uppspelning av video tillfälligt stoppas

Om du växlar mellan video enheter kan video strömmen pausas medan data strömmen hämtas från den valda enheten.

#### <a name="possible-causes"></a>Möjliga orsaker
Strömning från och växling mellan medie enheter är i beräknings intensiva. Att växla ofta kan leda till försämrade prestanda. Utvecklare uppmanas att stoppa en enhets ström innan en ny startas.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Mikrofonen för Bluetooth-headset identifieras därför inte under samtalet på Safari på iOS
Bluetooth-headset stöds inte av Safari på iOS. Din Bluetooth-enhet visas inte i tillgängliga mikrofon alternativ och andra deltagare kommer inte att kunna höra dig om du försöker använda Bluetooth via Safari.

#### <a name="possible-causes"></a>Möjliga orsaker
Detta är en känd OS/iOS/iOS/iPad-begränsning för operativ system. 

Med Safari på **MacOS** och **iOS/iPad** går det inte att räkna upp eller välja högtalar enheter via kommunikations tjänsterna Enhetshanteraren eftersom uppräkningar/markering av högtalare inte stöds av Safari. I det här scenariot ska valet av enhet uppdateras via operativ systemet.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Rotationen av en enhet kan skapa dålig video kvalitet
Användare kan uppleva försämrad video kvalitet när enheterna roteras.

<br/>Enheter som påverkas: Google pixel 5, Google pixel 3a, Apple iPad 8 och Apple iPad X
<br/>Klient bibliotek: anropar (Java Script)
<br/>Webbläsare: Safari, Chrome
<br/>Operativ system: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Kamera växling gör skärmen låst 
När en kommunikation Services-användare ansluter till ett samtal med hjälp av det JavaScript-anrop som anropar klient biblioteket och sedan träffar kamera växelns knapp, kan användar gränssnittet bli helt utan att svara tills programmet uppdateras eller webbläsaren skickas till bakgrunden av användaren.

<br/>Enheter som påverkas: Google pixel 4a
<br/>Klient bibliotek: anropar (Java Script)
<br/>Webbläsare: Chrome
<br/>Operativ system: iOS, Android


#### <a name="possible-causes"></a>Möjliga orsaker
Under undersökning.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Om video signalen stoppades när anropet är i läget "ansluter" kommer videon inte att skickas när samtalet har startats 
Om användarna bestämmer sig för att snabbt aktivera och inaktivera video samtidigt som anropet är i `Connecting` tillstånd – detta kan leda till problem med data ström som erhållits för anropet. Vi uppmuntrar utvecklare att bygga sina appar på ett sätt som inte kräver att video aktive ras eller inaktive ras medan anropet är i `Connecting` läget. Det här problemet kan orsaka försämrade video prestanda i följande scenarier:

 - Om användaren startar med ljud och sedan startar och stoppar video medan anropet är i `Connecting` läget.
 - Om användaren startar med ljud och sedan startar och stoppar video medan anropet är i `Lobby` läget.


#### <a name="possible-causes"></a>Möjliga orsaker
Under undersökning.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Ibland tar det lång tid att rendera deltagares videor
Under ett pågående grupp anrop skickar _användaren en_ video och sedan ansluter _användare B_ till anropet. Ibland kan användare B inte se video från användare A, eller användarens video börjar återges efter en lång fördröjning. Detta kan bero på en nätverks miljö som kräver ytterligare konfiguration. I dokumentationen för [nätverks krav](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) hittar du vägledning för nätverks konfiguration.
