---
title: Konversation med flera enheter (för hands version) – tal service
titleSuffix: Azure Cognitive Services
description: Med en konversation med flera enheter är det enkelt att skapa en röst-eller textkonversation mellan flera klienter och koordinera meddelanden som skickas mellan dem.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932416"
---
# <a name="what-is-multi-device-conversation-preview"></a>Vad är en konversation med flera enheter (för hands version)?

**Med en konversation med flera enheter** blir det enkelt att skapa en röst-eller textkonversation mellan flera klienter och koordinera meddelanden som skickas mellan dem.

Med en **konversation med flera enheter** kan du:

- Anslut flera klienter till samma konversation och hantera sändning och mottagning av meddelanden mellan dem.
- Du kan enkelt skriva över ljud från varje klient och skicka avskriften till de andra, med valfri översättning.
- Skicka SMS-meddelanden mellan klienter, med valfri översättning.

Du kan bygga en funktion eller lösning som fungerar över en matris med enheter. Varje enhet kan skicka meddelanden separat (antingen avskrifter av ljud eller snabb meddelanden) till alla andra enheter.

[**Konversations avskrift**](conversation-transcription.md) fungerar på en enda enhet med en mikrofon mat ris för flera kanaler, och en **konversation** med flera enheter lämpar sig för scenarier med flera enheter, var och en med en enda mikrofon.

>[!IMPORTANT]
> Konversationen med flera enheter stöder **inte** sändning av ljudfiler mellan klienter: endast avskrifter och/eller översättning.

## <a name="key-features"></a>Huvudfunktioner

- **Real tids avskrift** – alla får en avskrift av konversationen så att de kan följa efter texten i real tid eller spara den för senare.
- **Real tids översättning** – med fler än 70 [språk som stöds](language-support.md#text-languages) för text översättning kan användare översätta konversationen till deras önskade språk.
- **Läsbara avskrifter** – avskriften och översättningen är enkla att följa, med skiljetecken och menings brytningar.
- **Röst-eller text inmatning** – varje användare kan tala eller skriva på sin egen enhet, beroende på vilka språk stöd som har Aktiver ATS för deltagarens valda språk. Se [språk stöd](language-support.md#speech-to-text).
- **Meddelande relä** – konversations tjänsten för flera enheter distribuerar meddelanden som skickas av en klient till alla andra på de språk som du väljer.
- **Meddelande identifiering** – varje meddelande som användare får i konversationen taggas med smek namnet för den användare som skickade den.

## <a name="use-cases"></a>Användningsfall

### <a name="lightweight-conversations"></a>Lätta samtal

Det är enkelt att skapa och ansluta till en konversation. En användare fungerar som "värd" och skapar en konversation, som genererar en slumpmässig samtals kod för fem brev och en QR-kod. Alla andra användare kan delta i konversationen genom att skriva i konversations koden eller skanna QR-koden. 

Eftersom användare ansluter via konversations koden och inte behöver dela kontakt information, är det enkelt att skapa snabba, på plats-konversationer.

### <a name="inclusive-meetings"></a>Inkluderade möten

Real tids avskrift och översättning kan hjälpa till att göra konversationer tillgängliga för personer som talar olika språk och/eller är döva eller är hörselskadade. Varje person kan också aktivt delta i konversationen genom att tala om vilket språk som helst eller skicka snabb meddelanden.

### <a name="presentations"></a>Presentationer

Du kan också ange bild texter för presentationer och föreläsningar både på skärmen och på mål grupps medlemmarnas egna enheter. När mål gruppen har anslutits till en konversations kod kan de se avskriften på deras önskade språk på sin egen enhet.

## <a name="how-it-works"></a>Så här fungerar det

Alla klienter kommer att använda tal-SDK för att skapa eller ansluta till en konversation. Tal-SDK: n samverkar med konversations tjänsten för flera enheter, som hanterar livs längden för en konversation, inklusive deltagar listan, varje klients valda språk och meddelanden som skickas.  

Varje klient kan skicka ljud-eller snabb meddelanden. Tjänsten använder tal igenkänning för att konvertera ljud till text och skicka snabb meddelanden som de är. Om klienter väljer olika språk kommer tjänsten att översätta alla meddelanden till de angivna språken för varje klient.

![Översikts diagram över flera enheter](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Översikt över konversation, värd och deltagare

En **konversation** är en session som en användare startar för att de andra deltagande användarna ska kunna ansluta. Alla klienter ansluter till konversationen med en **konversations kod** på fem bokstäver.

Varje konversation skapar metadata som innehåller:
-    Tidsstämpel för när konversationen startades och avslutades
-    Lista över alla deltagare i konversationen som innehåller varje användares valda smek namn och primärt språk för tal-eller text indata.


Det finns två typer av användare i en konversation:  **värd** och **deltagare**.

**Värden** är den användare som startar en konversation och som agerar som administratör för den konversationen.
- Varje konversation kan bara ha en värd
- Värden måste vara ansluten till konversationen under konversationens varaktighet. Om värden lämnar konversationen avslutas konversationen för alla andra deltagare.
- Värden har några extra kontroller för att hantera konversationen: 
    - Lås konversationen – förhindra att fler deltagare ansluter sig
    - Stäng av alla deltagare – hindra andra deltagare från att skicka meddelanden till konversationen, oavsett om de är tilldelade från tal eller snabb meddelanden
    - Stänga av enskilda deltagare
    - Stäng av alla deltagare
    - Slå av enskilda deltagare

En **deltagare** är en användare som ansluter till en konversation.
- En deltagare kan när som helst lämna och återansluta till samma konversation utan att avsluta konversationen för andra deltagare.
- Deltagare kan inte låsa konversationen eller stänga av/stänga av andra

> [!NOTE]
> Varje konversation kan ha upp till 100 deltagare, varav 10 kan läsas samtidigt vid en bestämd tidpunkt.

## <a name="language-support"></a>Stöd för språk

När du skapar eller ansluter till en konversation måste varje användare välja ett **primärt språk**: det språk som de ska tala om och skicka snabb meddelanden i, och även det språk som de kommer att se andra användares meddelanden.

Det finns två typer av språk: **tal till text** och **endast text**:
- Om användaren väljer ett **tal-till-text-** språk som primärt språk kommer de att kunna använda både tal-och text indata i konversationen.

- Om användaren väljer ett språk som **bara är text** , kommer de bara att kunna använda text indata och skicka snabb meddelanden i konversationen. Text språk är de språk som stöds för text översättning, men inte tal-till-text. Du kan se tillgängliga språk på sidan [språk stöd](./language-support.md) .

Förutom det primära språket kan varje deltagare också ange ytterligare språk för översättning av konversationen.

Nedan visas en sammanfattning av vad användaren kan göra i en konversation med flera enheter, baserat på deras valda primära språk.


| Vad användaren kan göra i konversationen | Tal till text | Endast text |
|-----------------------------------|----------------|------|
| Använd tal ingångar | ✔️ | ❌ |
| Skicka snabb meddelanden | ✔️ | ✔️ |
| Översätt konversationen | ✔️ | ✔️ |

> [!NOTE]
> Listor över tillgängliga språk för tal till text och text översättning finns i språk som [stöds](./language-support.md).



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översätt konversationer i real tid](quickstarts/multi-device-conversation.md)