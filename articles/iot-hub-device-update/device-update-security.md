---
title: Säkerhet för enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Förstå hur enhets uppdatering för IoT Hub säkerställer att enheterna uppdateras på ett säkert sätt.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489437"
---
# <a name="device-update-security-model"></a>Säkerhets modell för enhets uppdatering

Enhets uppdatering för IoT Hub erbjuder en säker metod för att distribuera uppdateringar för enhetens inbyggda program vara, avbildningar och program till dina IoT-enheter. Arbets flödet ger en säker och säker kanal från slut punkt till slut punkt med en fullständig typ av invårdnads modell som en enhet kan använda för att bevisa att en uppdatering är betrodd, oförändrad och avsiktlig.

Varje steg i arbets flödet för enhets uppdateringar skyddas genom olika säkerhetsfunktioner och processer för att säkerställa att varje steg i pipelinen utför en säker leverans till nästa. Enhets uppdaterings klienten identifierar och hanterar korrekt eventuella Illegitimate uppdaterings begär Anden. Klienten kontrollerar också varje nedladdning för att säkerställa att innehållet är betrott, oförändrat och avsiktligt.

## <a name="for-solution-operators"></a>För lösnings operatörer

När lösnings operatörer importerar uppdateringar till sin enhets uppdaterings instans, laddar tjänsten upp och kontrollerar uppdateringen av binärfilerna för att säkerställa att de inte har ändrats eller bytts ut av en obehörig användare. När den har verifierats genererar enhets uppdaterings tjänsten ett internt [uppdaterings manifest](./update-manifest.md) med hashar för filer från import manifestet och andra metadata. Uppdaterings manifestet signeras sedan av enhets uppdaterings tjänsten.

När en lösnings operatör begär att uppdatera en enhet skickas ett signerat meddelande över den skyddade IoT Hub-kanalen till enheten. Begärans signatur verifieras av enhetens enhets uppdaterings agent som autentisk. 

Alla resulterande binära hämtningar skyddas genom validering av signaturen för uppdaterings manifestet. Uppdaterings manifestet innehåller binärfilerna för binärfiler, så när manifestet är betrott är enhets uppdaterings agenten betrodd för hashar och matchar dem mot binärfilerna. När den binära uppdateringen har laddats ned och verifierats, överlämnas den till installations programmet på enheten.

## <a name="for-device-builders"></a>För enhets byggare

För att säkerställa att enhets uppdaterings tjänsten skalar ned till enkla enheter med låg prestanda, använder säkerhets modellen obehandlade asymmetriska nycklar och RAW-signaturer. De använder JSON-baserade format som JSON Web tokens & JSON-webbnycklar.

### <a name="securing-update-content-via-the-update-manifest"></a>Skydda uppdaterings innehåll via uppdaterings manifestet

Uppdaterings manifestet verifieras med hjälp av två signaturer. Signaturerna skapas med en struktur som består av *signerings* nycklar och *rot* nycklar.

Enhets uppdaterings agenten har inbäddade offentliga nycklar som används för alla enheter som är kompatibla med enhets uppdateringar. Detta är *rot* nycklarna. Motsvarande privata nycklar styrs av Microsoft.

Microsoft genererar också ett offentligt/privat nyckel par som inte ingår i enhets uppdaterings agenten eller som lagras på enheten. Detta är *signerings* nyckeln.

När en uppdatering importeras till enhets uppdatering för IoT Hub och uppdaterings manifestet genereras av tjänsten, signerar tjänsten manifestet med hjälp av signerings nyckeln och inkluderar själva signerings nyckeln, som är signerad av en rot nyckel. När uppdaterings manifestet skickas till enheten tar enhets uppdaterings agenten emot följande signaturinformation:

1. Själva värdet för signaturen.
2. Den algoritm som används för att skapa #1.
3. Information om den offentliga nyckeln för den signerings nyckel som används för att generera #1.
4. Signaturen för den offentliga signerings nyckeln i #3.
5. ID för den offentliga nyckeln för den rot nyckel som används för att skapa #3.
6. Den algoritm som används för att skapa #4.

Enhets uppdaterings agenten använder den information som definierats ovan för att verifiera att signaturen för den offentliga signerings nyckeln signeras av rot nyckeln. Enhets uppdaterings agenten verifierar sedan att signaturen för uppdaterings manifestet signeras av signerings nyckeln. Om alla signaturer är korrekta är uppdaterings manifestet betrott av enhets uppdaterings agenten. Eftersom uppdaterings manifestet innehåller de filhashar som motsvarar själva uppdateringsfilerna kan även uppdateringsfiler vara betrodda om hash-filerna matchar.

Med rot-och signerings nycklar kan Microsoft regelbundet återställa signerings nyckeln, en säkerhets rutin.

### <a name="json-web-signature-jws"></a>JSON-webbsignatur (JWS)

`updateManifestSignature`Används för att säkerställa att den information som ingår i `updateManifest` inte har ändrats. Skapas `updateManifestSignature` med hjälp av en JSON-webbsignatur med JSON-webbnycklar, vilket gör det möjligt att verifiera källan. Signaturen är en Base64Url kodad sträng med tre avsnitt som anges av ".".  Se [Hjälp metoder för jws_util. h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) för att parsa och verifiera JSON-nycklar och tokens.

JSON Web Signature är en vanlig [föreslagen IETF-standard](https://tools.ietf.org/html/rfc7515) för signering av innehåll med JSON-baserade data strukturer. Det är ett sätt att säkerställa integriteten för data genom att verifiera signaturen för data. Mer information finns i [RFC 7515](https://www.rfc-editor.org/info/rfc7515)för JSON-webbsignaturen (JWS).

### <a name="json-web-token"></a>JSON Web Token

JSON Web tokens är en öppen, bransch [standard](https://tools.ietf.org/html/rfc7519) Metod för att representera anspråk på ett säkert sätt mellan två parter.

### <a name="root-keys"></a>Rot nycklar

Varje enhets uppdaterings enhet innehåller en uppsättning rot nycklar. Dessa nycklar är förtroende roten för alla signaturer för enhets uppdateringar. En signatur måste vara kedjad genom en av dessa rot nycklar för att anses vara legitim.

Uppsättningen rot nycklar ändras med tiden eftersom det är rätt att regelbundet rotera signerings nycklar av säkerhets synpunkt. Det innebär att enhets uppdaterings agentens program vara måste uppdateras med den senaste uppsättningen rot nycklar. 

### <a name="signatures"></a>Signera

Alla signaturer kommer att tillgodoses av en signerings nyckel (offentlig) signerad av en av rot nycklarna. Signaturen kommer att identifiera vilken rot nyckel som användes för att signera signerings nyckeln. 

En enhets uppdaterings agent måste verifiera signaturer genom att först verifiera att signaturen (offentlig) nyckelns signatur är korrekt, giltig och signerad av en av de godkända rot nycklarna. När signerings nyckeln har verifierats kan själva signaturen verifieras med hjälp av den offentliga nyckeln för den betrodda signeringen.

Signerings nycklar roteras på ett mycket snabbare takt än rot nycklar, så förväntar sig meddelanden som signerats av olika signerings nycklar. 

Återkallning av en signerings nyckel hanteras av enhets uppdaterings tjänsten, så användarna bör inte försöka cache-signera nycklar. Använd alltid signerings nyckeln som tillhör en signatur.

### <a name="receiving-updates"></a>Ta emot uppdateringar

Uppdaterings begär Anden som tas emot av en enhets uppdaterings Agent innehåller ett UM-dokument (signerat uppdaterings manifest). Agenten måste verifiera att signaturen för UM är korrekt och intakt. Detta görs genom att verifiera att den UM signaturens signerings nyckel har signerats av en korrekt rot nyckel. När den är klar verifierar agenten UM-signaturen mot signerings nyckeln.

När UM-signaturen har verifierats kan enhets uppdaterings agenten lita på den som en "källa för sanningen". Alla ytterligare säkerhets förtroenden härrör från den här källan. 

UM innehåller URL: er och filhashar för innehåll att ladda ned och installera. När agenten har laddat ned en uppdaterings-binärfil måste den Verifiera uppdateringen mot den fil-hash som finns i UM. Detta ger en transitiv förtroende modell för nedladdning av verifiering. Det garanterar inte bara att innehållet är intakt (inte ändrat), utan bekräftar också att det som hämtades var verkligen vad som var tänkt att laddas ned. 

### <a name="securing-the-device"></a>Skydda enheten

Det är viktigt att se till att säkerhets resurserna för enhets uppdatering är korrekt skyddade och skyddade på enheten. Till gångar som rot nycklar måste skyddas mot modifiering. Det finns olika sätt att göra detta, till exempel att använda säkerhetsenheter (TPM, SGX, HSM, andra säkerhetsenheter) eller till och med hårdkoda dem i enhets uppdaterings agenten. Den senare kräver att enhets uppdaterings agentens kod är digitalt signerad och att systemets kod integritets stöd är aktiverat för att skydda mot skadlig modifiering av agent koden.

Ytterligare säkerhets åtgärder kan vara motiverade, som att se till att leverans från komponent till komponent utförs på ett säkert sätt. Du kan till exempel registrera ett enskilt isolerat konto för att köra de olika komponenterna. Och begränsa nätverksbaserade kommunikationer (t. ex. REST API-anrop) enbart till localhost.

**[Nästa steg: Lär dig mer om hur enhets uppdatering använder Azure RBAC](.\device-update-control-access.md)**