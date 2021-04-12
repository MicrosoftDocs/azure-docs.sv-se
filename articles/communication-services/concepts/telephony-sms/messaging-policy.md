---
title: Meddelande princip
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om SMS Messaging-principer.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646889"
---
# <a name="azure-communication-services-messaging-policy"></a>Meddelande princip för Azure Communication Services

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services förändrar hur våra kunder engagerar sina kunder genom att skapa omfattande, anpassade kommunikations upplevelser som drar nytta av samma tjänster i företags klass som du kan använda för att få tillbaka Microsoft team och Skype. Integrera SMS-meddelanden i kommunikations lösningarna för att komma åt dina kunder när som helst och var som helst behöver support. Du behöver bara tänka på några meddelande krav för att komma igång.

Vi vet att meddelande kraven kan verka avskräckande för att lära sig, men de är lika enkla som att komma ihåg "COMS":

- K – medgivande
- O-Opt-Out
- M – meddelande innehåll
- S-förfalskning

Vi har utvecklat den här meddelande principen för att hjälpa dig att uppfylla reglernas krav och följa rekommenderade metod tips. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Samtycke 

### <a name="what-is-consent"></a>Vad är medgivande?

Medgivande är ett avtal mellan dig och meddelande mottagaren som gör att du kan skicka automatiserade meddelanden till dem. Du måste få ett medgivande innan du skickar det första meddelandet, och du bör ta bort den mottagare som de accepterar för att ta emot meddelanden från dig. Den här proceduren kallas för att ta emot "föregående uttryckliga medgivande" från den person som du ska få meddelandet.

De meddelanden som du skickar måste vara av samma typ av meddelanden som mottagaren har godkänt och ska bara skickas till det nummer som mottagaren har fått. Om du tänker skicka informations meddelanden, till exempel påminnelser eller aviseringar för avtalade tider, kan medgivande vara antingen skrivet eller muntligt. Om du avser att skicka kampanj meddelanden, till exempel försäljnings-eller marknadsförings meddelanden som befordrar en produkt eller tjänst, måste medgivande vara skrivet.

### <a name="how-do-you-obtain-consent"></a>Hur får du medgivande?

Medgivande kan erhållas på flera olika sätt, t. ex.:

- När en användare anger sina telefonnummer till en webbplats 
- När en användare initierar ett SMS för textmeddelande, eller
- När en användare skickar ett inloggnings nyckelord till ditt telefonnummer. 
 
Oavsett hur godkännandet uppnås måste du och dina kunder se till att godkännandet är entydigt. Omfånget för godkännandet bör vara klart för mottagaren.


### <a name="consent-requirements"></a>Tillstånds krav:

- Ange ett "anrop till åtgärd" innan du får medgivande. Du och dina kunder bör tillhandahålla potentiella meddelande mottagare med ett "anrop till åtgärd" som bjuder in dem till att delta i ditt meddelande program. Anropet till åtgärden bör minst omfatta: (1) identiteten för meddelande avsändaren, (2) ta bort opt-instruktioner, (3) anvisningar för att säga upp och (4) eventuella associerade meddelande tjänster.
- Medgivande får inte överföras eller tilldelas. Ett medgivande som en enskild person ger till dig kan inte överföras eller säljas till en tredje part. Om du samlar in en individs medgivande för en tredje part måste du tydligt identifiera tredje part för personen. Du måste också ange att det medgivande du har erhållit endast gäller kommunikation från tredje part.
- Godkännandet är begränsat i syfte. En person som ger sitt nummer för ett visst syfte samtycker till att endast ta emot kommunikation för det specifika syftet och från den specifika meddelande avsändaren. Innan du får ditt medgivande bör du tydligt meddela den avsedda meddelande mottagaren om du ska skicka återkommande meddelanden eller meddelanden från ett dotter bolag.

### <a name="consent-best-practices"></a>Metod tips för godkännande:

Utöver de meddelande krav som beskrivs ovan kanske du vill implementera flera vanliga bästa metoder, inklusive: 

- Detaljerad information om "anrop till åtgärd". För att säkerställa att du får rätt medgivande, ange
  - namnet på eller beskrivningen av ditt meddelande program eller din produkt
  - de nummer som mottagarna tar emot meddelanden från och 
  - alla tillämpliga villkor innan en person som väljer att ta emot meddelanden från dig.
- Korrekta uppgifter om medgivande. Du bör Spara poster om godkännande som en enskild person ger dig minst fyra år. Medgivande poster kan innehålla:
  - tidsstämplar
  - mediet med vilket godkännande erhölls
  - den speciella kampanj för vilken medgivande hämtades
  - skärm dum par
  - sessions-ID eller IP-adress för den skickade personen.
- Sekretess-och säkerhets principer. Utvecklare uppmanas att tillhandahålla enkla sekretess principer som meddelande mottagare kan granska innan de får tag i sitt medgivande. Vi rekommenderar också att du underhåller förebyggande säkerhets kontroller för att skydda enskildas privat information.


## <a name="double-opt-in-consent"></a>Ett dubbelt deltagande godkännande:

Azure Communication Services rekommenderar att du använder Double opt-in-medgivande för alla meddelande kampanjer. Ett dubbelt deltagande är en process i två steg där en person först ger tillåtelse att ta emot vissa typer av meddelanden från dig. Sedan skickar du ett uppföljnings meddelande för att bekräfta sitt medgivande. Du bör bara skicka fler meddelanden när mottagaren bekräftar sitt medgivande.

Det första bekräftelse meddelande som du skickar måste innehålla din identitet, alternativet att inaktivera framtida meddelanden (till exempel användning av ett "STOP"-kommando), ett avgiftsfritt nummer eller "hjälp"-kommando för ytterligare information, meddelande om att personen har registrerats i ett återkommande meddelande program, en kort beskrivning av programmet, den frekvens med vilken du tänker skicka återkommande meddelanden och eventuella associerade avgifter. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Kräver Azure Communication Services ett dubbel deltagande medgivande?
Ja, även om du alltid vill ha ett dubbelt deltagande, kräver Azure Communication Services att du använder dubbelt deltagande för vissa typer av meddelande kampanjer, på grund av deras frekventa användning i phishing-scheman eller deras tendenser att leda till klagomål från konsumenter. Dessa kampanjer omfattar:
- Meddelanden för automatisk garanti
- Kortsiktiga sjukförsäkrings planer
- Gäldenärer eller meddelanden om reducering av ränte satser om de inte görs av ett finansiellt institut
- Meddelanden om generering av leads
- Tävlingen, tävlingar och giveaways
- Arbets starts erbjudanden

De kampanjer för vilka dubbla deltagande medgivande krävs kan ändras i Azure Communication Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Undantag till traditionella medgivande regler:
Det tidigare uttryckliga godkännandet är vanligt vis nödvändigt innan ett meddelande skickas, men det finns två situationer där medgivande till meddelande om att en person är underförstådd.

- Mottagaren initierar en kommunikation. Om en enskild person initierar en kommunikation genom att skicka ett meddelande till dig kan du ange relevant information som svar på en viss förfrågan eller förfrågan i meddelandet. Det underförstådda medgivande att den enskilda som tillhandahållits är begränsad till konversationen som den enskilda initieras, såvida du inte får ett medgivande för ytterligare kommunikation.
- Undantag för vissa tjänster. Det finns flera olika tjänster som du kanske har samtyckt till att initiera ett meddelande. De vanligaste är: 
- paket leverans meddelanden
- finansiella bank meddelanden som berör tids känsliga ämnen (till exempel potentiellt bedrägliga transaktioner eller data intrång)
- meddelanden om hälsovårds leverantören som innehåller tids känslig information och ett behandlings syfte (till exempel avtalad tid eller påminnelser, labb resultat och Skriv meddelanden). 
 
Ingen av dessa meddelanden kan innehålla inbjudningar eller annonser.


## <a name="opt-out"></a>Opt-out

Meddelande mottagare kan återkalla sitt medgivande och välja att ta emot framtida meddelanden på ett rimligt sätt. Du får inte ange ett exklusivt sätt för meddelande mottagare att återkalla medgivande. 

### <a name="opt-out-requirements"></a>Opt-out-krav:

Se till att meddelande mottagarna kan välja att inaktivera framtida meddelanden när som helst. Du måste också ha flera opt-out-alternativ. När en meddelande mottagare väljer att inte skicka fler meddelanden om inte personen ger förnyat medgivande.

En av de vanligaste opt-out-mekanismerna är att inkludera ett "stopp"-nyckelord i det första meddelandet i varje ny konversation. Var beredd på att ta bort kunder som svarar med gemener eller andra vanliga nyckelord, till exempel "Avsluta prenumeration" eller "Avbryt". När en enskild person återkallar ditt medgivande bör du ta bort dem från alla återkommande meddelande kampanjer om de uttryckligen väljer att fortsätta ta emot meddelanden från ett visst program.

### <a name="opt-out-best-practices"></a>Bästa praxis:

Förutom nyckelord kan andra vanliga opt-out-metoder förse kunder med en utsedd e-postadress, telefonnumret till kund support personal eller en länk för att avbryta prenumerationen på din webb sida. 


### <a name="how-we-handle-opt-out-requests"></a>Så här hanterar du opt-out-begäranden:

Om en enskild begäran om att inaktivera framtida meddelanden på ett kostnads fritt Azure Communication Services-nummer, kommer all ytterligare trafik från det antalet att stoppas automatiskt. Du måste dock fortfarande se till att du inte skickar ytterligare meddelanden för den meddelande kampanjen från nya eller andra nummer. Om du har fått ett separat uttryckligt godkännande för en annan meddelande kampanj kan du fortsätta att skicka meddelanden från ett annat nummer för kampanjen. Kolla in våra vanliga frågor och svar om du vill veta mer om att [välja ut hantering](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Meddelande innehåll

### <a name="adult-content"></a>Vuxen innehåll:

Meddelande innehåll som innehåller element av kön, hat, alkohol, skjutvapen, tobak, spel eller tävlingen och tävlingar kan utlösa ytterligare krav. Det här innehållet är uttryckligen förbjudet i vissa jurisdiktioner. Om du skickar ett meddelande som innehåller det här innehållet är det din skyldighet att följa alla tillämpliga lagar för de jurisdiktioner där kommunikationen tas emot. På begäran av myndighets tvångs-eller Azure Communication Services måste du vara beredd att tillhandahålla bevis på medgivande med lokal lagstiftning som reglerar vuxna innehåll.

Även om sådant innehåll inte är olagligt bör du inkludera en ålders kontroll vid valbarhet till Age-grind av avsedd meddelande mottagare från innehåll som är olämpligt för barn. I USA gäller ytterligare juridiska krav för marknadsförings kommunikation riktade mot barn under tretton den 13. 

### <a name="prohibited-content"></a>Förbjudet innehåll:

Azure Communication Services förhindrar viss meddelande innehåll oavsett medgivande. Förbjudet innehåll innehåller:
- Innehåll som främjar olagliga aktiviteter (t. ex. skatte skatteflykt eller animaliska Cruelty i USA)
- Hat tal, kränkande tal, trakasserier eller annat tal som bedöms vara patent stötande
- Pornografiskt-innehåll
- Obscent-eller vulgari-innehåll
- Intimidation och hot
- Innehåll som avser att defraud, vilseleda, orsaka skada eller få till gång till något värde 
- Innehåll som inciterar skada, diskriminering eller våld
- Innehåll som sprider skadlig kod
- Innehåll som avser att undvika ålder – hantera-krav

Vi förbehåller oss rätten att ändra listan över förbjudna meddelande innehåll när som helst.

## <a name="spoofing"></a>Förfalskning

Förfalskning är en del av att orsaka ett missvisande eller felaktigt ursprungs nummer som visas på en meddelande mottagares enhet. Vi rekommenderar starkt att du och någon annan tjänst leverantör än skickar falska meddelanden. Förfalskning skyddar meddelande avsändarens identitet och förhindrar att meddelande mottagare enkelt väljer bort oönskad kommunikation. Vi kräver också att du följer alla tillämpliga förfalsknings lagar.

## <a name="final-thoughts"></a>Slutliga tankar

### <a name="legal-responsibility"></a>Juridiskt ansvar:

Den här meddelande principen utgör inte juridisk information och vi förbehåller oss rätten att när som helst ändra principen. Azure Communication Services ansvarar inte för att se till att innehållet, tiden eller mottagarna av våra kunders meddelanden uppfyller alla tillämpliga juridiska krav. 

Våra kunder ansvarar för alla meddelande krav. Om du är en plattforms-eller program varu leverantör som använder Azure Communication Services i meddelande syfte, bör du även kräva att kunderna följer alla krav som beskrivs i den här meddelande principen. För ytterligare vägledning ger CTIA användbara [meddelande principer och bästa praxis](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf).

### <a name="penalties"></a>Påföljder

Vi uppmuntrar våra kunder att utveckla och implementera principer och procedurer som har utformats för att säkerställa efterlevnaden av alla meddelande krav. Överträdelser av meddelande kraven kan leda till stora böter som snabbt kan prat bubbla. Det är i bästa intresse att lära sig och följa alla tillämpliga meddelande krav och utveckla effektiva skydds åtgärder för att innehålla och eliminera överträdelser innan de sprids. Om du gör intrång i vår meddelande princip eller andra juridiska krav kommer vi att samar beta med dig för att säkerställa framtida efterlevnad. Men vi förbehåller oss rätten att ta bort alla kunder från Azure Communication Services-plattformen som visar ett mönster för inkompatibilitet med vår meddelande princip eller juridiska krav.
