---
title: Översikt över Azure Communication Services som anropar klient bibliotek
titleSuffix: An Azure Communication Services concept document
description: Ger en översikt över det anropande klient biblioteket.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e154e43f9e9378d6cccd23e2e5892f2a8ccf9a1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598889"
---
# <a name="calling-client-library-overview"></a>Översikt över klientbiblioteket för samtal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Det finns två olika familjer av att anropa klient bibliotek för *klienter* och *tjänster.* För närvarande är tillgängliga klient bibliotek avsedda för slut användar upplevelser: webbplatser och inbyggda appar.

Tjänst klient biblioteken är inte tillgängliga än och ger till gång till planerna för rå röst och video data som passar för integrering med robotar och andra tjänster.

## <a name="calling-client-library-capabilities"></a>Anropa klient biblioteks funktioner

I följande lista presenteras de funktioner som för närvarande är tillgängliga i Azure Communication Services som anropar klient bibliotek.

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



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Java script anropar klient biblioteks stöd av OS och webbläsare

Följande tabell visar en uppsättning webbläsare som stöds och som är tillgängliga för tillfället. Vi stöder de senaste tre versionerna av webbläsaren om inget annat anges.

|                                  | Chrome | Safari  | Edge (krom) | 
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

## <a name="calling-client-library-streaming-support"></a>Anropar stöd för strömning av klient bibliotek
Kommunikations tjänsterna som anropar klient biblioteket stöder följande strömmande konfigurationer:

|           |Webb | Android/iOS|
|-----------|----|------------|
|**Antal utgående strömmar som kan skickas samtidigt** |1-video-eller 1-skärm delning | 1 video och en skärm delning|
|**Antal inkommande strömmar som kan renderas samtidigt** |1-video-eller 1-skärm delning| 6 video-och 1-bildskärms delning |


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att anropa](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Mer information finns i följande artiklar:
- Bekanta dig med allmänna [samtals flöden](../call-flows.md)
- Läs mer om [samtals typer](../voice-video-calling/about-call-types.md)
- [Planera din PSTN-lösning](../telephony-sms/plan-solution.md)
