---
title: Översikt över Azure Communication Services som anropar SDK
titleSuffix: An Azure Communication Services concept document
description: 'Innehåller en översikt över den anropande SDK: n.'
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ac9cef77569dffe461f7711195c5638e831aa218
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110112"
---
# <a name="calling-sdk-overview"></a>Översikt över samtals-SDK

Med hjälp av den anropande SDK: n kan slutanvändare köra upplevelsen av röst-och video kommunikation. Den här sidan innehåller detaljerade beskrivningar av hur du anropar funktioner, inklusive information om plattform och webb läsar stöd. Kom igång direkt genom att titta på [anropa snabb starter](../../quickstarts/voice-video-calling/getting-started-with-calling.md) eller [anropa ett hjälte exempel](../../samples/calling-hero-sample.md). 

När du har börjat utveckla tittar du på [sidan kända problem](../known-issues.md) för att hitta de buggar vi arbetar med.

Viktiga funktioner i samtals-SDK:

- **Addressing** – Azure Communication Services tillhandahåller allmänna [identiteter](../identity-model.md) som används för att adressera kommunikations slut punkter. Klienterna använder dessa identiteter för att autentisera till tjänsten och kommunicera med varandra. Dessa identiteter används för att anropa API: er som ger klienter insyn i som är anslutna till ett samtal (listan).
- **Kryptering** – den anropande SDK: n krypterar trafiken och förhindrar manipulering av kabeln. 
- **Enhets hantering och media** – det anropande SDK: n tillhandahåller funktioner för bindning till ljud-och video enheter, kodar innehåll för effektiv överföring via dataplanen och återger innehåll för att få ut enheter och vyer som du anger. API: er tillhandahålls också för skärm-och program delning.
- **PSTN** – samtals-SDK kan ta emot och initiera röst samtal med det traditionella offentliga telefoni systemet, [med hjälp av telefonnummer som du hämtar i Azure Portal](../../quickstarts/telephony-sms/get-phone-number.md) eller program mässigt.
- **Team möten** – samtals-SDK kan [delta i team möten](../../quickstarts/voice-video-calling/get-started-teams-interop.md) och interagera med team röst-och video dataplanen. 
- **Meddelanden** – API: t för anrop innehåller API: er som gör det möjligt för klienter att meddelas om inkommande samtal. I situationer där appen inte körs i förgrunden är mönster tillgängliga för att [utlösa popup-meddelanden](../notifications.md) ("popups") för att informera slutanvändarna om ett inkommande samtal. 

## <a name="detailed-capabilities"></a>Detaljerade funktioner 

I följande lista presenteras de funktioner som för närvarande är tillgängliga i Azure Communication Services som anropar SDK: er.

| Grupp med funktioner | Funktion                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Kärn funktioner | Placera ett ett-till-ett-samtal mellan två användare                                                                           | ✔️   | ✔️            | ✔️
|                   | Placera ett grupp samtal med fler än två användare (upp till 350 användare)                                                       | ✔️   | ✔️            | ✔️
|                   | Marknadsför ett ett-till-ett-samtal med två användare i ett grupp samtal med fler än två användare                                 | ✔️   | ✔️            | ✔️
|                   | Delta i ett grupp anrop efter att det har startat                                                                              | ✔️   | ✔️            | ✔️
|                   | Bjud in en annan VoIP-deltagare för att ansluta till ett pågående grupp anrop                                                       | ✔️   | ✔️            | ✔️
|  Kontroll av Mid-anrop | Aktivera/inaktivera din video                                                                                              | ✔️   | ✔️            | ✔️
|                   | Tyst/avljud av MIC                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Växla mellan kameror                                                                                              | ✔️   | ✔️            | ✔️
|                   | Lokalt undantag/ej Behåll                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktiv talare                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Välj högtalare för samtal                                                                                            | ✔️   | ✔️            | ✔️
|                   | Välj mikrofon för samtal                                                                                         | ✔️   | ✔️            | ✔️
|                   | Visa status för en deltagare<br/>*Inaktiv, tidig media, anslutning, ansluten, stoppad, i lobbyn, frånkopplat*         | ✔️   | ✔️            | ✔️
|                   | Visa status för ett samtal<br/>*Tidig media, inkommande, anslutning, ringa, ansluten, stoppa, koppla från, frånkopplad* | ✔️   | ✔️            | ✔️
|                   | Visa om en deltagare är avstängd                                                                                      | ✔️   | ✔️            | ✔️
|                   | Visa orsaken till att en deltagare lämnat ett samtal                                                                       | ✔️   | ✔️            | ✔️
| Skärm delning    | Dela hela skärmen inifrån programmet                                                                 | ✔️   | ❌            | ❌
|                   | Dela ett enskilt program (från listan över program som körs)                                                | ✔️   | ❌            | ❌
|                   | Dela en webbläsare-flik från listan med öppna flikar                                                                  | ✔️   | ❌            | ❌
|                   | Deltagare kan visa fjärrskärms resurs                                                                            | ✔️   | ✔️            | ✔️
| Tjänstgöring            | Lista deltagare                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Ta bort en deltagare                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | Placera ett ett-till-ett-samtal med en PSTN-deltagare                                                                     | ✔️   | ✔️            | ✔️
|                   | Placera ett grupp samtal med PSTN-deltagare                                                                           | ✔️   | ✔️            | ✔️
|                   | Marknadsför ett ett-till-ett-samtal med en PSTN-deltagare i ett grupp anrop                                                 | ✔️   | ✔️            | ✔️
|                   | Ringa ut från ett grupp anrop som en PSTN-deltagare                                                                    | ✔️   | ✔️            | ✔️
| Allmänt           | Testa din mikrofon, talare och kamera med en ljud test tjänst (tillgänglig genom att anropa 8: echo123)                   | ✔️   | ✔️            | ✔️
| Enhetshantering | Be om tillåtelse att använda ljud och/eller video                                                                       | ✔️   | ✔️            | ✔️
|                   | Hämta kamera lista                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Ange kamera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Hämta vald kamera                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Hämta mikrofon lista                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Ange mikrofon                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Hämta vald mikrofon                                                                                             | ✔️   | ✔️            | ✔️
|                   | Hämta högtalar lista                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Ange högtalare                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Hämta vald talare                                                                                                | ✔️   | ✔️            | ✔️
| Video åter givning   | Rendera en enkel video på många platser (lokal kamera eller fjärrström)                                                  | ✔️   | ✔️            | ✔️
|                   | Ange/uppdatera skalnings läge                                                                                           | ✔️   | ✔️            | ✔️
|                   | Rendera fjärran sluten video ström                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Stöd för att anropa SDK-direktuppspelning
Kommunikations tjänsterna som anropar SDK har stöd för följande strömmande konfigurationer:

| Gräns          |Webb | Android/iOS|
|-----------|----|------------|
|**Antal utgående strömmar som kan skickas samtidigt** |1-video-eller 1-skärm delning | 1 video och en skärm delning|
|**Antal inkommande strömmar som kan renderas samtidigt** |1-video-eller 1-skärm delning| 6 video-och 1-bildskärms delning |

## <a name="calling-sdk-timeouts"></a>Anropar SDK-tidsgräns

Följande tids gränser gäller för kommunikations tjänsterna som anropar SDK: er:

| Action           | Tidsgräns i sekunder |
| -------------- | ---------- |
| Deltagare för att ansluta/ta bort | 120 |
| Lägg till eller ta bort ny spärr från ett samtal (starta/stoppa video eller skärm delning) | 40 |
| Tids gräns för överföring av anrops åtgärd | 60 |
| 1:1 för anrops etablering | 85 |
| Timeout för etablering av grupp anrop | 85 |
| Tids gräns för PSTN-anrop | 115 |
| Befordra 1:1-anrop till en timeout för grupp anrop | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Java script-anropar SDK-stöd från OS och webbläsare

Följande tabell visar en uppsättning webbläsare som stöds och som är tillgängliga för tillfället. Vi stöder de senaste tre versionerna av webbläsaren om inget annat anges.

| Plattform                         | Chrome | Safari  | Edge (krom) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Safari-versionerna 13.1 + stöds, 1:1-anrop stöds inte i Safari.

* * Safari 14 +/macOS 11 + krävs för stöd för utgående video.

Utgående skärm delning stöds endast på Skriv bords plattformar (Windows, macOS och Linux), oavsett webbläsarens version, och stöds inte på någon mobil plattform (Android, iOS, iPad och surfplattor).

En iOS-app i Safari kan inte räkna upp/välja MIC-och högtalar enheter (till exempel Bluetooth). Detta är en begränsning i operativ systemet och det finns alltid en enhet.


## <a name="calling-client---browser-security-model"></a>Anropa klientens webbläsare säkerhets modell

### <a name="user-webrtc-over-https"></a>Användarens WebRTC över HTTPS

WebRTC-API: er som `getUserMedia` kräver att den app som anropar dessa API: er hanteras via https.

För lokal utveckling kan du använda `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Bädda in kommunikations tjänsterna som anropar SDK i en iframe

En ny [behörighets princip (kallas även en funktions princip)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) antas av olika webbläsare. Den här principen påverkar anrops scenarier genom att styra hur program kan komma åt en enhets kamera och mikrofon genom ett iframe-element mellan ursprung.

Om du vill använda en iframe som värd för en del av appen från en annan domän måste du lägga till `allow` attributet med rätt värde i iframe.

Denna IFRAME tillåter till exempel både kamera-och mikrofon åtkomst:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [samtals flöden](../call-flows.md)
- Läs mer om [samtals typer](../voice-video-calling/about-call-types.md)
- [Planera din PSTN-lösning](../telephony-sms/plan-solution.md)
