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
ms.openlocfilehash: b9ed71a8fc9346ecd454eba98dcbb3b13186eba2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276050"
---
# <a name="known-issues-azure-communication-services-calling-sdks"></a>Kända problem: Azure Communication Services som anropar SDK: er
Den här artikeln innehåller information om begränsningar och kända problem som rör Azure Communication Services som anropar SDK: er.

> [!IMPORTANT]
> Det finns flera faktorer som kan påverka kvaliteten på din anrops upplevelse. Se dokumentationen för **[nätverks krav](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** för att lära dig mer om nätverks konfiguration för kommunikations tjänster och testa bästa praxis.


## <a name="javascript-sdk"></a>JavaScript SDK

Det här avsnittet innehåller information om kända problem som är associerade med Azure Communication Services Java scripts röst-och video samtals-SDK: er.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Att uppdatera en sida tar inte omedelbart bort användaren från deras anrop

Om en användare finns i ett samtal och bestämmer sig för att uppdatera sidan, tar tjänsten för kommunikations tjänster inte bort den här användaren omedelbart från anropet. Det väntar på att användaren ska ansluta igen. Användaren tas bort från samtalet när medie tjänsten har nått sin tids gräns.

Det är bäst att bygga användar upplevelser som inte kräver att slutanvändare uppdaterar sidan i programmet under ett samtal. Om en användare uppdaterar sidan återanvänder du samma kommunikations tjänst användar-ID när de återgår till programmet.

Användaren kommer att behållas i anropet under tiden (1-2 minuter) från andra deltagares perspektiv. Om användaren återansluter till samma kommunikations tjänst användar-ID visas de som samma, befintliga objekt i `remoteParticipants` samlingen.

Om användaren skulle skicka videon före uppdateringen `videoStreams` behåller samlingen den tidigare data ström informationen tills tjänsten har nått sin tids gräns och tar bort den. I det här scenariot kan programmet välja att observera eventuella nya strömmar som lagts till i samlingen och återge en med högst `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Det går inte att återge flera för hands vyer från flera enheter på webben
Detta är en känd begränsning. Mer information finns i [Översikt över anrop till SDK](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Det går inte att räkna upp enheter i Safari när programmet körs på iOS eller iPad

Program kan inte räkna upp/välja MIC/talare-enheter (som Bluetooth) i Safari iOS/iPad. Detta är en känd begränsning för operativ system.

Om du använder Safari på macOS kommer din app inte att kunna räkna upp/välja högtalare via kommunikations tjänsterna Enhetshanteraren. I det här scenariot måste enheter väljas via operativ systemet. Om du använder Chrome på macOS kan appen räkna upp/välja enheter via kommunikations tjänsterna Enhetshanteraren.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Ljud anslutningen försvinner när SMS-meddelanden eller samtal tas emot under ett pågående VoIP-anrop
Det här problemet kan uppstå på grund av flera orsaker:

- Vissa mobila webbläsare upprätthåller inte anslutningen i bakgrunds tillstånd. Detta kan leda till en försämrad anrops upplevelse om VoIP-anropet avbröts av en händelse som skickar programmet till bakgrunden. 
- Ibland fångar ett SMS-eller PSTN-samtal ljud ljudet och släpper inte tillbaka ljudet till VoIP-anropet. Apple har åtgärdat det här problemet i iOS-versioner 14.4.1 +. 

<br/>Klient bibliotek: anropar (Java Script)
<br/>Webbläsare: Safari, Chrome
<br/>Operativ system: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Att växla video enheter flera gånger kan orsaka att uppspelning av video tillfälligt stoppas

Om du växlar mellan video enheter kan video strömmen pausas medan data strömmen hämtas från den valda enheten.

#### <a name="possible-causes"></a>Möjliga orsaker
Växling mellan enheter kan ofta leda till försämrade prestanda. Utvecklare uppmanas att stoppa en enhets ström innan en ny startas.

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
När en kommunikation Services-användare ansluter till ett samtal med hjälp av JavaScript-anrop SDK och sedan träffar kamera växel knappen, kan användar gränssnittet sluta svara tills programmet uppdateras eller webbläsaren skickas till bakgrunden av användaren.

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

### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>Räkna upp/komma åt enheter för Safari på MacOS och iOS 
Om åtkomst till enheter beviljas återställs enhets behörigheter efter en viss tid. Safari på MacOS och på iOS behåller inte behörigheter under lång tid om det inte finns någon data ström som förvärv ATS. Det enklaste sättet att undvika detta är att anropa API för DeviceManager. askDevicePermission () innan du anropar enhets hanterarens API: er för enhets uppräkning (DeviceManager. getCameras (), DeviceManager. getSpeakers () och DeviceManager. getMicrophones ()). Om behörigheterna finns där kommer användaren inte att se något, om inte, kommer att frågas igen.

<br/>Enheter som påverkas: iPhone
<br/>Klient bibliotek: anropar (Java Script)
<br/>Webbläsare: Safari
<br/>Operativ system: iOS

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Ibland tar det lång tid att rendera deltagares videor
Under ett pågående grupp anrop skickar _användaren en_ video och sedan ansluter _användare B_ till anropet. Ibland kan användare B inte se video från användare A, eller användarens video börjar återges efter en lång fördröjning. Det här problemet kan bero på en nätverks miljö som kräver ytterligare konfiguration. I dokumentationen för [nätverks krav](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) hittar du vägledning för nätverks konfiguration.
