---
title: Begrepp för Azure IoT Hub X. 509-säkerhet | Microsoft Docs
description: Koncept – förstå värdet X. 509 certifikat utfärdare i IoT Device Manufacturing och autentisering.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490728"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Konceptuell förståelse för X. 509 CA-certifikat i IoT-branschen

I den här artikeln beskrivs värdet för att använda certifikat från X. 509 certifikat utfärdare (CA) i IoT Device Manufacturing och autentisering för att IoT Hub. Den innehåller information om installation av leverans kedja och markerings fördelar.

I den här artikeln beskrivs:

* Vad X. 509 CA-certifikat är och hur du hämtar dem

* Registrera ditt X. 509-CA-certifikat för att IoT Hub

* Så här konfigurerar du en tillverknings leverans kedja för X. 509 CA-baserad autentisering

* Hur enheter signerade med X. 509 CA ansluter till IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Översikt

X. 509-autentisering för certifikat utfärdare är en metod för att autentisera enheter till IoT Hub med hjälp av en metod som dramatiskt fören klar genereringen av enhetens identitet och livs cykel hantering i leverans kedjan.

Ett särskiljande attribut för X. 509-CA-autentisering är en en-till-många-relation som ett CA-certifikat har med sina efterföljande enheter. Den här relationen gör det möjligt att registrera ett valfritt antal enheter i IoT Hub genom att registrera ett X. 509 CA-certifikat en gång, annars måste enhetens unika certifikat vara förregistrerade för varje enhet innan en enhet kan ansluta. Den här en-till-många-relationen fören klar också enhets certifikatets livs cykel hanterings åtgärder.

Ett annat viktigt attribut för X. 509-CA-autentiseringen är förenkling av logistik för leverans kedja. Säker autentisering av enheter kräver att varje enhet har en unik hemlighet som en nyckel som grund för förtroende. I certifikatbaserad autentisering är den här hemligheten en privat nyckel. Ett typiskt enhets tillverknings flöde omfattar flera steg och förmyndare komponenter. Att säkert hantera enhetens privata nycklar över flera förmyndare komponenter och upprätthålla förtroende är svårt och dyrt. Att använda certifikat utfärdare löser problemet genom att logga varje övervakare i en kryptografisk förtroende kedja i stället för att lita på dem med privata enhets nycklar. Varje övervakare i tur och ordning signerar enheter i respektive process steg i tillverknings flödet. Det totala resultatet är en optimal leverans kedja med inbyggd ansvars linje genom att använda den kryptografiska förtroende kedjan. Det är värt att notera att den här processen ger störst säkerhet när enheterna skyddar sina unika privata nycklar. I detta fall rekommenderar vi att du använder HSM (Secure modules) som kan generera privata nycklar som aldrig kommer att se dagen.

Den här artikeln ger en heltäckande vy av hur du använder 509 med X., från leverans kedja till enhets anslutning, samtidigt som du använder ett verkligt världs exempel för att stärka förståelse.

Du kan också använda registrerings grupper med Azure-IoT Hub Device Provisioning Service (DPS) för att hantera etablering av enheter till hubbar. Mer information om hur du använder DPS för att etablera X. 509-certifikat enheter finns i [Självstudier: etablera flera X. 509-enheter med hjälp av registrerings grupper](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="introduction"></a>Introduktion

CA-certifikatet för X. 509 är ett digitalt certifikat vars innehavare kan signera andra certifikat. Det här digitala certifikatet är X. 509 eftersom det överensstämmer med en certifikat formats standard som föreskrivs i IETF: s RFC 5280 standard och är en certifikat utfärdare (CA) eftersom dennes innehavare kan signera andra certifikat.

Användningen av X. 509-CA: n tolkas bäst i relation till ett konkret exempel. Överväg företags-X, en tillverkare av smarta X-widgetar utformade för professionell installation. Företags-X-utkälla, både för tillverkning och installation. Leverantören tillverkar Factory-Y för att tillverka tjänsterna Smart-X-widget och service provider – Z för att installera. Företags-X önskar att Smart-X-widgeten direkt levereras från fabriken-Y till tekniker-Z för installation och att den ansluts direkt till företags instansen av IoT Hub efter installationen utan ytterligare åtgärder från företaget-X. För att detta ska ske måste företags-X slutföra en del konfigurations åtgärder för en viss tidpunkt till primär Smart-X-widget för automatisk anslutning. Med scenariot från slut punkt till slut punkt i åtanke struktureras resten av den här artikeln enligt följande:

* Hämta CA-certifikatet för X. 509

* Registrera X. 509 CA-certifikat till IoT Hub

* Signera enheter till en certifikat kedja med förtroende

* Enhets anslutning

## <a name="acquire-the-x509-ca-certificate"></a>Hämta CA-certifikatet för X. 509

Företags-X har möjlighet att köpa ett X. 509 CA-certifikat från en offentlig rot certifikat utfärdare eller skapa ett via en självsignerad process. Ett alternativ skulle vara optimalt jämfört med det andra, beroende på program scenariot. Oavsett vilket alternativ som används i processen finns två grundläggande steg som genererar ett offentligt/privat nyckel par och signerar den offentliga nyckeln till ett certifikat.

![Flöde för att generera ett X509CA-certifikat](./media/iot-hub-x509ca-concept/csr-flow.png)

Information om hur du utför de här stegen skiljer sig mellan olika tjänst leverantörer.

### <a name="purchasing-an-x509-ca-certificate"></a>Köpa ett X. 509 CA-certifikat

Att köpa ett CA-certifikat har fördelen att en välkänd rot certifikat utfärdare fungerar som en betrodd tredje part för att kunna identifiera giltighet av IoT-enheter när enheterna ansluter. Företag-X väljer det här alternativet om de avser en smart-X-widget för att interagera med tredjepartsprodukter eller tjänster från tredje part efter den första anslutningen till IoT Hub.

För att köpa ett X. 509 CA-certifikat, väljer företaget-X en rot certifikat tjänst leverantör. En Internets ökning för frasen "rot certifikat utfärdare" ger en bättre lead. Rot certifikat utfärdaren leder till företags-X om hur du skapar ett offentligt/privat nyckel par och hur du skapar en begäran om certifikat signering för deras tjänster. En CSR är den formella processen att tillämpa för ett certifikat från en certifikat utfärdare. Resultatet av det här köpet är ett certifikat som används som auktoritets certifikat. Med tanke på allmän förekomst för X. 509-certifikat, har certifikatet förmodligen formaterats korrekt i IETF: s RFC 5280-standard.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Skapa ett Self-Signed X. 509-CA-certifikat

Processen för att skapa ett Self-Signed X. 509-CA-certifikat liknar köpet med undantag för en tredjeparts-signerare som rot certifikat utfärdaren. I vårt exempel kommer företag-X att signera sitt auktoritets certifikat i stället för en rot certifikat utfärdare. Företag-X kan välja det här alternativet för testning tills de är redo att köpa ett auktoritets certifikat. Företag-X kan också använda ett självsignerat X. 509 CA-certifikat i produktion, om Smart-X-widget inte är avsett att ansluta till några tjänster från tredje part utanför IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrera X. 509-certifikatet för att IoT Hub

Företags-X måste registrera X. 509-CA: n för att IoT Hub där den ska kunna autentisera smarta X-widgetar när de ansluter. Detta är en engångs process som gör det möjligt att autentisera och hantera valfritt antal enheter med smarta X-widgetar. Detta är en engångs process på grund av en en-till-många-relation mellan CA-certifikat och enhets certifikat som har signerats av CA-certifikatet eller ett mellanliggande certifikat. Den här relationen är en av de största fördelarna med att använda metoden X. 509 CA-autentisering. Alternativet är att ladda upp enskilda certifikat tumavtrycken för varje enhet med Smart-X-widget och därmed lägga till drifts kostnader.

Att registrera X. 509 CA-certifikatet är en två stegs process, certifikat uppladdning och certifikats bevis.

![Registrera ett X509CA-certifikat](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X. 509 CA-certifikat uppladdning

Uppladdnings processen för X. 509 certifikat utfärdare är bara att ladda upp CA-certifikatet till IoT Hub. IoT Hub förväntar sig certifikatet i en fil. Företag-X laddar bara upp certifikat filen. Certifikat filen får inte under några omständigheter innehålla några privata nycklar. Bästa praxis från standarder som styr PKI-myndigheter (Public Key Infrastructure) som har kunskap om företagets privata i det här fallet finns bara i företaget-X.

### <a name="proof-of-possession-of-the-certificate"></a>Certifikatets besittnings bevis

X. 509-CA-certifikatet, precis som alla digitala certifikat, är offentlig information som är mottaglig för avlyssning. På så sätt kan en avlyssning fånga ett certifikat och försöka överföra det som ett eget. I vårt exempel vill IoT Hub se till att CA-certifikatets företags-X-överföring verkligen tillhör företags-X. Det gör det genom att utmana företags-X för att bevisa att de faktiskt besitter certifikatet genom ett [pop-flöde (proof-of-Drive)](https://tools.ietf.org/html/rfc5280#section-3.1). Det huvudsakliga flödet är IoT Hub att generera ett slumptal som ska signeras av företags-X med hjälp av den privata nyckeln. Om bästa praxis för företags-X följde PKI och skyddar sin privata nyckel, skulle det bara finnas plats för att svara på korrekt utmaning. IoT Hub fortsätter att registrera X. 509 CA-certifikatet på ett framgångs rik svars bevis.

Ett lyckat svar på en utmanings utmaning från IoT Hub Slutför 509 CA-registrering.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Signera enheter till en certifikat kedja med förtroende

IoT kräver varje enhet för att ha en unik identitet. Dessa identiteter har formatet certifikat för certifikatbaserade autentiseringsscheman. I vårt exempel innebär detta att varje smart-X-widget måste ha ett unikt enhets certifikat. Hur fungerar företags-X-installationen i dess leverans kedja?

Ett sätt att gå vidare med detta är att i förväg generera certifikat för smarta X-widgetar och att lita på kunskaper om motsvarande unika privata enhets privata nycklar med partners för leverans kedja. För företag-X innebär detta att lita på fabriks-Y och tekniker-Z. Även om detta är en giltig metod, medföljer de utmaningar som måste lösas för att säkerställa förtroende enligt följande:

1. För att kunna dela enhetens privata nycklar med partner partner, förutom att ignorera PKI: ns bästa praxis att aldrig dela privata nycklar, gör det kostsamt att skapa förtroende i leverans kedjan. Det innebär att kapital system som till exempel säkra rum för att ta del av privata enhets privata nycklar och processer som regelbunden säkerhets granskning måste installeras. Lägg till kostnad i leverans kedjan.

2. På ett säkert sätt kan redovisning av enheter i leverans kedjan och senare hantera dem i distributionen bli en en-till-en-aktivitet för varje nyckel-till-enhet-par från enhetens unika certifikat (därmed privat nyckel) för att dra tillbaka enheten. Detta utesluter grupp hantering av enheter om inte begreppet grupper uttryckligen är inbyggda i processen. Säker hantering av redovisning och enhets cykel, är därför en hög belastning. I vårt exempel skulle företags-X ha den här belastningen.

X. 509 CA-certifikatautentisering ger eleganta lösningar för att afore listade utmaningar genom att använda certifikat kedjor. En certifikat kedja resulterar från en CA-signering av en mellanliggande certifikat utfärdare som i sin tur signerar en annan mellanliggande certifikat utfärdare och så vidare till en sluten mellanliggande CA I vårt exempel är företags-X tecken Factory-Y, som i sin tur registrerar tekniker-Z som slutligen signerar Smart-X-widget.

![Certifikat kedja-hierarki](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Över överlappande av certifikat i kedjan visar den logiska överlämnande av utfärdaren. Många leverans kedjor följer denna logiska hand om varje mellanliggande certifikat utfärdare är inloggad i kedjan och tar emot alla överordnade CA-certifikat, och den senaste mellanliggande CA: n signerar varje enhet och matar in alla certifikat från kedjan i enheten. Detta är vanligt när kontraktet Manufacturing Company med en hierarki med fabriker provisiorar en viss fabrik för tillverkning. Även om hierarkin kan vara flera nivåer (till exempel per geografi/produkt typ/tillverknings linje), kommer bara fabriken i slutet att samverka med enheten, men kedjan bevaras från toppen av hierarkin.

Alternativa kedjor kan ha olika mellanliggande certifikat utfärdare som interagerar med enheten, vilket innebär att CA: n som kommunicerar med enheten infogar innehåll för certifikat kedjan. Hybrid modeller är också möjliga om bara en del av CA: n har fysisk interaktion med enheten.

I vårt exempel interagerar både Factory-Y-och tekniker-Z med Smart-X-widgeten. Även om företags-X äger Smart-X-widgeten, interagerar den faktiskt inte fysiskt med den i hela leverans kedjan. Certifikat kedjan för smart-X-widget utgör därför en företags-X-signerings fabrik-Y som i sin tur registrerar tekniker-Z som sedan ger den slutliga signaturen till Smart-X-widget. Tillverkning och installation av Smart-X-widget utgör fabriks-Y och tekniker-Z med deras respektive mellanliggande CA-certifikat för att signera var och en av de smarta X-widgetarna. Slut resultatet av hela processen är smart-X-widgetar med unika enhets certifikat och certifikat kedjan som går upp till företags-X CA-certifikat.

![Förtroende kedja från certifikaten för ett företag till certifikaten för ett annat företag](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Det här är en lämplig punkt för att granska värdet för CA-metoden X. 509. I stället för att skapa och lämna ut certifikat för varje smart-X-widget till leverans kedjan var företags X bara tvungen att signera Factory-Y en gång. I stället för att behöva spåra alla enheter i enhetens livs cykel, kan företags-X nu spåra och hantera enheter genom grupper som naturligt kommer från leverans kedjan, till exempel enheter som installeras av tekniker-ö efter en gång i juli år.

Senast men minst, certifikat utfärdarens metod för autentisering skyddar säkert ansvar i enhetens tillverknings kedja. På grund av processen för certifikat kedjan registreras alla medlemmar i kedjan kryptografiskt och verifierbara.

Den här processen är beroende av vissa antaganden som måste vara tilldelad för att vara klar. Det kräver oberoende skapande av enhetens unika offentliga/privata nyckel par och att den privata nyckeln skyddas i enheten. Lyckligt vis är säker Silicon-chips i form av HSM (Secure modules) som kan generera nycklar och skydda privata nycklar. Företag-X behöver bara lägga till en sådan chips i komponent strukturen för smart-X-widget.

## <a name="device-connection"></a>Enhets anslutning

Föregående avsnitt ovan har byggts upp till enhets anslutning. Genom att helt enkelt registrera ett X. 509 CA-certifikat för att IoT Hub en gång, hur kan miljon tals enheter ansluta och bli autentiserade från första gången?  Gång genom samma certifikat överföring och det bevis som du har påträffat, gick det tidigare att registrera CA-certifikatet för X. 509.

Enheter som tillverkats för X. 509-CA-autentisering är utrustade med enhetens unika certifikat och en certifikat kedja från respektive tillverknings leverantör. Enhets anslutning, även för första gången, sker i en två stegs process: certifikat kedjans uppladdning och bevis-för-besittning.

När certifikat kedjan har laddats upp laddar enheten upp enhetens unika certifikat tillsammans med certifikat kedjan som installerades i IoT Hub. Med hjälp av det förregistrerade X. 509-CA-certifikatet kan IoT Hub verifiera ett par saker som den uppladdade certifikat kedjan är internt konsekvent och att kedjan har sitt ursprung av den giltiga ägaren av CA-certifikatet för X. 509. Med en registrerings process för X. 509-certifikat utfärdare skulle IoT Hub starta en process med en utmanings process för att kontrol lera att kedjan och därmed enhets certifikatet verkligen tillhör den enhet som laddar upp den. Det gör det genom att generera en slumpmässig utmaning som signeras av enheten med hjälp av den privata nyckeln för verifiering med IoT Hub. Ett lyckat svar utlöser IoT Hub att acceptera enheten som autentisk och bevilja den anslutning.

I vårt exempel överför varje smart-X-widget sitt unika enhets certifikat tillsammans med Factory-Y-och tekniker-Z X. 509 CA-certifikat och svarar sedan på den utmanings utmaning från IoT Hub.

![Flöda från ett certifikat till ett annat, pop-Challenge från hubben](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Observera att stiftelsen i förtroendet är att skydda privata nycklar, inklusive enhetens privata nycklar. Vi kan därför inte stressa tillräckligt vikten av säker Silicon-chips i form av HSM (Secure modules) för att skydda enhetens privata nycklar och den övergripande bästa praxis att aldrig dela privata nycklar, t. ex. en fabrik som litar på en annan med den privata nyckeln.
