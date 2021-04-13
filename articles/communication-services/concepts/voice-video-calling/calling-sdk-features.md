---
title: översikt Azure Communication Services ANROPA SDK
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över anropande SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364880"
---
# <a name="calling-sdk-overview"></a>Översikt över anropande SDK

Med anropande SDK kan slutanvändarenheterna använda röst- och videokommunikationsupplevelser. Den här sidan innehåller detaljerade beskrivningar av anropsfunktioner, inklusive information om plattforms- och webbläsarstöd. Om du vill komma igång direkt kan du läsa Anropa [snabbstarter](../../quickstarts/voice-video-calling/getting-started-with-calling.md) eller [Anropa hero-exempel.](../../samples/calling-hero-sample.md) 

När du har börjat utveckla kan du titta på [sidan med kända problem](../known-issues.md) för att hitta buggar som vi arbetar med.

Viktiga funktioner i anropande SDK:

- **Adressering** – Azure Communication Services tillhandahåller allmänna [identiteter som](../identity-model.md) används för att hantera kommunikationsslutpunkter. Klienter använder dessa identiteter för att autentisera till tjänsten och kommunicera med varandra. Dessa identiteter används i Anropa API:er som ger klienter insyn i vem som är ansluten till ett anrop (listan).
- **Kryptering** – Anropande SDK krypterar trafik och förhindrar manipulering av kabeln. 
- **Enhetshantering** och media – Den anropande SDK:n tillhandahåller funktioner för bindning till ljud- och videoenheter, kodar innehåll för effektiv överföring över kommunikationsdataplanen och återger innehåll till utdataenheter och vyer som du anger. API:er tillhandahålls också för skärm- och programdelning.
- **PSTN** – Den anropande SDK:n kan ta emot och initiera röstsamtal med det traditionella offentligt växlade telefonisystemet med hjälp av telefonnummer som du får [i Azure Portal](../../quickstarts/telephony-sms/get-phone-number.md) eller programmatiskt.
- **Teams-möten** – Den anropande [SDK:n kan ansluta till Teams-möten](../../quickstarts/voice-video-calling/get-started-teams-interop.md) och interagera med Teams röst- och videodataplan. 
- **Meddelanden** – Den anropande SDK:n tillhandahåller API:er som gör att klienter kan meddelas om ett inkommande anrop. I situationer där din app inte körs i förgrunden är mönster tillgängliga för att skicka [popup-meddelanden ("popup-meddelanden")](../notifications.md) för att informera slutanvändarna om ett inkommande samtal. 

## <a name="detailed-capabilities"></a>Detaljerade funktioner 

I följande lista visas den uppsättning funktioner som för närvarande är tillgängliga i Azure Communication Services AnropaR.

| Grupp med funktioner | Funktion                                                                                                          | Js  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Kärnfunktioner | Placera ett en-till-ett-anrop mellan två användare                                                                           | ✔️   | ✔️            | ✔️
|                   | Placera ett gruppanrop med fler än två användare (upp till 350 användare)                                                       | ✔️   | ✔️            | ✔️
|                   | Höja upp ett en-till-ett-anrop med två användare i ett gruppsamtal med fler än två användare                                 | ✔️   | ✔️            | ✔️
|                   | Delta i ett gruppsamtal när det har startat                                                                              | ✔️   | ✔️            | ✔️
|                   | Bjud in en annan VoIP-deltagare att delta i ett pågående gruppanrop                                                       | ✔️   | ✔️            | ✔️
|  Mid-anropskontroll | Aktivera/inaktivera videon                                                                                              | ✔️   | ✔️            | ✔️
|                   | Stänga av/slå på ljudet på mic                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Växla mellan kameror                                                                                              | ✔️   | ✔️            | ✔️
|                   | Håll/av håll lokalt                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktiv talare                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Välja talare för anrop                                                                                            | ✔️   | ✔️            | ✔️
|                   | Välj mikrofon för anrop                                                                                         | ✔️   | ✔️            | ✔️
|                   | Visa status för en deltagare<br/>*Inaktiv, Tidig media, Ansluta, Ansluten, Håller på, InTr, Frånkopplad*         | ✔️   | ✔️            | ✔️
|                   | Visa status för ett anrop<br/>*Tidig media, inkommande, ansluta, ringning, ansluten, hålla, koppla från, frånkopplad* | ✔️   | ✔️            | ✔️
|                   | Visa om en deltagare är avstängd                                                                                      | ✔️   | ✔️            | ✔️
|                   | Visa orsaken till att en deltagare lämnade ett samtal                                                                       | ✔️   | ✔️            | ✔️
| Skärmdelning    | Dela hela skärmen inifrån programmet                                                                 | ✔️   | ❌            | ❌
|                   | Dela ett specifikt program (från listan över program som körs)                                                | ✔️   | ❌            | ❌
|                   | Dela en webbläsarflik från listan med öppna flikar                                                                  | ✔️   | ❌            | ❌
|                   | Deltagare kan visa fjärrskärmsresurs                                                                            | ✔️   | ✔️            | ✔️
| Deltagarlistan            | Visa en lista över deltagare                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Ta bort en deltagare                                                                                                | ✔️   | ✔️            | ✔️
| Pstn              | Placera ett ett-till-ett-anrop med en PSTN-deltagare                                                                     | ✔️   | ✔️            | ✔️
|                   | Ringa ett gruppsamtal med PSTN-deltagare                                                                           | ✔️   | ✔️            | ✔️
|                   | Höja upp ett ett-till-ett-anrop med en PSTN-deltagare i ett gruppanrop                                                 | ✔️   | ✔️            | ✔️
|                   | Uppringning från ett gruppsamtal som en PSTN-deltagare                                                                    | ✔️   | ✔️            | ✔️
| Allmänt           | Testa din mic, talare och kamera med en ljudtestningstjänst (tillgänglig genom att anropa 8:echo123)                   | ✔️   | ✔️            | ✔️
| Enhetshantering | Be om behörighet att använda ljud och/eller video                                                                       | ✔️   | ✔️            | ✔️
|                   | Hämta kameralista                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Ställ in kamera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Hämta vald kamera                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Hämta mikrofonlista                                                                                                 | ✔️   | ❌           |❌  
|                   | Ange mikrofon                                                                                                      | ✔️   | ❌           | ❌  
|                   | Hämta vald mikrofon                                                                                             | ✔️   | ❌           | ❌  
|                   | Hämta talarlista                                                                                                   | ✔️   | ❌           | ❌  
|                   | Ange talare                                                                                                         | ✔️   | ❌           | ❌  
|                   | Hämta vald talare                                                                                                | ✔️   | ❌           | ❌  
| Videoåtergivning   | Rendera en video på många platser (lokal kamera eller fjärrström)                                                  | ✔️   | ✔️            | ✔️
|                   | Ange/uppdatera skalningsläge                                                                                           | ✔️   | ✔️            | ✔️
|                   | Rendera fjärrvideoström                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Anropa STÖD för SDK-strömning
Den Communication Services Anropa SDK stöder följande konfigurationer för direktuppspelning:

| Gräns          |Webb | Android/iOS|
|-----------|----|------------|
|**Antal utgående dataströmmar som kan skickas samtidigt** |1 video eller 1 skärmdelning | 1 video + 1 skärmdelning|
|**Antal inkommande dataströmmar som kan renderas samtidigt** |1 video eller 1 skärmdelning| 6 video + 1 skärmdelning |

## <a name="calling-sdk-timeouts"></a>Anropa SDK-tidsgränser

Följande tidsgränser gäller för Communication Services ANROPAR:

| Action           | Tidsgräns i sekunder |
| -------------- | ---------- |
| Återanslutning/borttagning av deltagare | 120 |
| Lägga till eller ta bort nya funktioner från ett anrop (Starta/stoppa video eller skärmdelning) | 40 |
| Tidsgräns för anropsöverföringsåtgärd | 60 |
| 1:1 Timeout för anropsetablering | 85 |
| Tidsgräns för gruppanropsetablering | 85 |
| Tidsgräns för PSTN-anropsetablering | 115 |
| Höja upp 1:1-anrop till en tidsgräns för gruppanrop | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Stöd för Sdk för JavaScript-anrop av operativsystem och webbläsare

Följande tabell representerar den uppsättning webbläsare som stöds och som för närvarande är tillgängliga. Vi stöder de senaste tre versionerna av webbläsaren om inget annat anges.

| Plattform                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS**                         |  ✔️    | ✔️**   | ❌             |
| Windows.                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*Safari version 13.1+ stöds, 1:1-anrop stöds inte i Safari.

**Safari 14+/macOS 11+ behövs för utgående videosupport.

Utgående skärmdelning stöds endast på skrivbordsplattformar (Windows, macOS och Linux), oavsett webbläsarversion och stöds inte på någon mobil plattform (Android, iOS, iPad och surfplattor).

En iOS-app i Safari kan inte räkna upp/välja mic- och talarenheter (till exempel Bluetooth). Det här är en begränsning i operativsystemet och det finns alltid bara en enhet.


## <a name="calling-client---browser-security-model"></a>Anropa klienten – webbläsarsäkerhetsmodell

### <a name="user-webrtc-over-https"></a>AnvändarwebRTC via HTTPS

WebRTC-API:er `getUserMedia` som kräver att appen som anropar dessa API:er betjänas via HTTPS.

För lokal utveckling kan du använda `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Bädda in Communication Services Anropa SDK i en iframe

En ny [behörighetsprincip (kallas även en funktionsprincip)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) används av olika webbläsare. Den här principen påverkar anropsscenarier genom att styra hur program kan komma åt en enhets kamera och mikrofon via ett iframe-element med korsande ursprung.

Om du vill använda en iframe som värd för en del av appen från en annan domän måste du lägga till attributet med rätt `allow` värde i din iframe.

Till exempel tillåter den här iframe både kamera- och mikrofonåtkomst:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [anropsflöden](../call-flows.md)
- Läs mer om [anropstyper](../voice-video-calling/about-call-types.md)
- [Planera din PSTN-lösning](../telephony-sms/plan-solution.md)
