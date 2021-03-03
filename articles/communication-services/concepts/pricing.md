---
title: Prissättnings scenarier för att anropa (röst/video) och chatta
titleSuffix: An Azure Communication Services concept document
description: Läs mer om pris modell för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bf255837a08a4810321f2f03cb97838e31a7cddc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657765"
---
# <a name="pricing-scenarios"></a>Prissättnings scenarier

Priserna för Azure Communication Services baseras vanligt vis på en modell där du betalar per användning. Priserna i följande exempel är i exempel syfte och kanske inte återspeglar de senaste priserna för Azure.

## <a name="voicevideo-calling-and-screen-sharing"></a>Röst-/video samtal och skärm delning

Azure Communication Services gör det möjligt att lägga till röst-/video samtal och skärm delning till dina program. Du kan bädda in upplevelsen i dina program med hjälp av Java Script, mål-C (Apple), Java (Android) eller .NET-klient bibliotek. Se vår [fullständiga lista över tillgängliga klient bibliotek](./sdk-options.md).

### <a name="pricing"></a>Prissättning

Anrops-och skärm delnings tjänster debiteras per minut per deltagare vid $0,004 per deltagare per minut för grupp anrop. Se [den här sidan](./call-flows.md)för att förstå de olika anrops flöden som är möjliga.

Varje deltagare i samtalet debiteras för varje minut som de är anslutna till samtalet. Detta gäller oavsett om användaren är video samtal, röst samtal eller skärm delning.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Prissättnings exempel: gruppera ljud-/video samtal med hjälp av JS-och iOS-klient bibliotek

Alice gjorde ett grupp samtal med kollegor, Bob och Kalle. Alice och Robert använde JS-klient biblioteken, Kalle-klient bibliotek för iOS.

- Anropet varar totalt 60 minuter.
- Alice och Bob deltog i hela anropet. Alice aktiverade videon i fem minuter och delat sin skärm i 23 minuter. Bob hade videon på hela samtalet (60 minuter) och delat sin skärm i 12 minuter.
- Kalle lämnade samtalet efter 43 minuter. Kalle använde ljud och video under tiden som han deltog (43 minuter).

**Kostnads beräkningar**

- 2 deltagare x 60 minuter x $0,004 per deltagare per minut = $0,48 [både video och ljud debiteras enligt samma pris]
- 1 deltagare x 43 minuter x $0,004 per deltagare per minut = $0,172 [både video och ljud debiteras enligt samma pris]

**Total kostnad för grupp anropet**: $0,48 + $0,172 = $0,652

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Prissättnings exempel: en användare av kommunikations tjänstens JS-klient bibliotek ansluter till ett schemalagt Microsoft Teams-möte

Alice är ett läkare som är ett möte med patienten, Bob. Alice kommer att ansluta till besöket från Teams Skriv bords program. Bob får en länk till att gå med på webbplatsen för hälso vårds leverantören, som ansluter till mötet med hjälp av klient biblioteket för Communication Services JS. Bob använder sin mobil telefon för att ange mötet med en webbläsare (iPhone med Safari). Chatten kommer att vara tillgänglig under det virtuella besöket.

- Anropet varar totalt 30 minuter.
- Alice och Robert deltar i hela samtalet. Alice aktiverar hennes video fem minuter efter att anropet startar och delar sin skärm i 13 minuter. Bob innehåller sin video om hela samtalet.
- Alice skickar fem meddelanden, Bob svarar med tre meddelanden.


**Kostnads beräkningar**

- 1 deltagare (Robert) x 30 minuter x $0,004 per deltagare per minut = $0,12 [både video och ljud debiteras enligt samma pris]
- 1 deltagare (Alice) x 30 minuter x $0,000 per deltagare per minut = $0,0 *.
- 1 deltagare (Robert) x 3 Chat-meddelanden x $0,0008 = $0,0024.
- 1 deltagare (Alice) x 5 Chat-meddelanden x $0,000 = $0,0 *.

* Alices medverkan omfattas av hennes team licens. Din Azure-faktura visar de minuter och chattar som team-användare hade för att få hjälp med kommunikations tjänster, men dessa minuter och meddelanden från Team klienten kommer inte att kosta.

**Total kostnad för besöket**:
- Användare ansluter med kommunikations tjänstens JS-klient bibliotek: $0,12 + $0,0024 = $0,1224
- Användare som ansluter till Teams Skriv bords program: $0 (omfattas av team License)


## <a name="chat"></a>Chatt

Med kommunikations tjänster kan du förbättra ditt program med möjligheten att skicka och ta emot chatt meddelanden mellan två eller flera användare. Chat-klient bibliotek är tillgängliga för Java Script, .NET, python och Java. Läs [mer om klient bibliotek på den här sidan](./sdk-options.md)

### <a name="price"></a>Pris

Du debiteras $0,0008 för varje chatt-meddelande som skickas.

### <a name="pricing-example-chat-between-two-users"></a>Prissättnings exempel: chatta mellan två användare

Geeta startar en chatt-tråd med Emily för att dela en uppdatering och skicka 5 meddelanden. Chatten varar 10 minuter. Geeta och Emily skickar ytterligare 15 meddelanden vart och ett.

**Kostnads beräkningar**
- Antal skickade meddelanden (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Prissättnings exempel: gruppera chatt med flera användare

Kalle startar en chatt-tråd med sina vänner Casey & Jasmine för att planera en semester. De chattar för ett tag därin Kalle, Casey & Jasmine skicka 20, 30 respektive 18 meddelanden. De inser att deras vän ros kan vara intresserade av att även delta i resan, så att de lägger till henne i chatten och delar alla meddelande historiken med henne.

Ros ser meddelandena och börjar chatta. I Casey får du ett samtal och han bestämmer sig för att komma igång med konversationen senare. Kalle, Jasmine & ros bestämmer sig för rese datum och skickar ytterligare 30, 25 respektive 35-meddelanden.

**Kostnads beräkningar**

- Antal skickade meddelanden (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefoni och SMS

## <a name="price"></a>Pris

Telefoni tjänster priss ätts per minut, medan SMS priss ätts per meddelande. Priserna bestäms av typ och plats för det nummer som du använder samt målet för dina anrop och SMS-meddelanden.

### <a name="telephone-number-leasing"></a>Leasing nummer

Avgifter för telefonnummer leasing debiteras före och efter en månad till månad:

|Nummertyp   |Månads avgift   |
|--------------|-----------|
|Lokal (USA)     |$1/månad        |
|Avgiftsfritt (USA) |$2/Mo |


### <a name="telephone-calling"></a>Telefon samtal

Traditionell telefon uppringning (samtal som sker via det offentliga telefonnätet) är tillgängligt med priser enligt principen betala per användning för telefonnummer som baseras på USA. Priset är en per minut-avgift baserat på vilken typ av nummer som används och målet för anropet. Pris information för de mest populära anrops målen finns i tabellen nedan. Se den [detaljerade pris listan](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) för en fullständig lista över destinationer.


#### <a name="united-states-calling-prices"></a>USA samtals priser

I följande priser ingår obligatoriska skatter och avgifter för kommunikation fram till den 30 juni 2021:

|Nummertyp   |För att ringa   |Ta emot samtal|
|--------------|-----------|------------|
|Lokal     |Från $0.013/min       |$0.0085/min        |
|Avgiftsfritt |$0.013/min   |$0.0220/min |

#### <a name="other-calling-destinations"></a>Andra anrops destinationer

I följande priser ingår obligatoriska skatter och avgifter för kommunikation fram till den 30 juni 2021:

|Gör anrop till   |Pris per minut|
|-----------|------------|
|Kanada     |Från $0.013/min   |
|Storbritannien     |Från $0.015/min   |
|Tyskland     |Från $0.015/min   |
|Frankrike     |Från $0.016/min   |


### <a name="sms"></a>SMS

SMS erbjuder pris enligt principen betala per användning. Priset är ett per meddelande-avgift baserat på meddelandets mål. Meddelanden kan skickas via avgiftsfria telefonnummer till telefonnummer som finns inom USA. Observera att det inte går att använda lokala telefonnummer (geografiska) för att skicka SMS-meddelanden.

I följande priser ingår obligatoriska skatter och avgifter för kommunikation fram till den 30 juni 2021:

|Land   |Skicka meddelanden|Ta emot meddelanden|
|-----------|------------|------------|
|USA (avgiftsfritt)    |$0.0075/MSG   | $0.0075/MSG |
