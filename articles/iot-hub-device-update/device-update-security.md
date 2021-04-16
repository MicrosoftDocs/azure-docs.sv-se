---
title: Säkerhet för enhetsuppdatering för Azure IoT Hub | Microsoft Docs
description: Förstå hur enhetsuppdatering för IoT Hub ser till att enheterna uppdateras på ett säkert sätt.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567306"
---
# <a name="device-update-security-model"></a>Säkerhetsmodell för enhetsuppdatering

Enhetsuppdatering för IoT Hub en säker metod för att distribuera uppdateringar för enhetens inbyggda programvara, avbildningar och program till dina IoT-enheter. Arbetsflödet tillhandahåller en säker kanal från hela kedjan med en fullständig modell för kontroll som en enhet kan använda för att bevisa att en uppdatering är betrodd, oförändrad och avsiktlig.

Varje steg i arbetsflödet för enhetsuppdatering skyddas via olika säkerhetsfunktioner och processer för att säkerställa att varje steg i pipelinen utför en säker överkörning till nästa. Enhetsuppdateringsklienten identifierar och hanterar alla olegitima uppdateringsbegäranden korrekt. Klienten kontrollerar också varje nedladdning för att säkerställa att innehållet är betrott, oföränderligt och avsiktligt.

## <a name="for-solution-operators"></a>För lösningsoperatörer

När lösningsoperatörer importerar uppdateringar till enhetsuppdateringsinstansen laddar tjänsten upp och kontrollerar de binära uppdateringsfilerna för att säkerställa att de inte har ändrats eller växlats ut av en obehörig användare. När den har verifierats genererar [](./update-manifest.md) enhetsuppdateringstjänsten ett internt uppdateringsmanifest med filhash-värden från importmanifestet och andra metadata. Det här uppdateringsmanifestet signeras sedan av enhetsuppdateringstjänsten.

När de har matats in i tjänsten och lagrats i Azure krypteras de binära uppdateringsfilerna och tillhörande kundmetadata automatiskt i vila av Azure Storage-tjänsten. Enhetsuppdateringstjänsten tillhandahåller inte automatiskt ytterligare kryptering, men gör det möjligt för utvecklare att kryptera innehållet själva innan innehållet når enhetsuppdateringstjänsten.

När lösningsoperatören begär att uppdatera en enhet skickas ett signerat meddelande via den skyddade IoT Hub till enheten. Begärans signatur verifieras av enhetens enhetsuppdateringsagent som giltig. 

Eventuell binär nedladdning skyddas genom validering av uppdateringsmanifestsignaturen. Uppdateringsmanifestet innehåller binärfilens hash-värden, så när manifestet är betrott litar enhetsuppdateringsagenten på hash-koderna och matchar dem mot binärfilerna. När den binära uppdateringsfilen har laddats ned och verifierats lämnas den sedan på ett säkert sätt över till installationsprogrammet på enheten.

## <a name="for-device-builders"></a>För Enhetsbyggare

För att säkerställa att tjänsten Enhetsuppdatering skalas ned till enkla enheter med låg prestanda använder säkerhetsmodellen asymmetriska rådatanycklar och råsignaturer. De använder JSON-baserade format som JSON-webbtoken & JSON-webbnycklar.

### <a name="securing-update-content-via-the-update-manifest"></a>Skydda uppdateringsinnehåll via uppdateringsmanifestet

Uppdateringsmanifestet verifieras med hjälp av två signaturer. Signaturerna skapas med hjälp av en struktur som består av *signeringsnycklar* och *rotnycklar.*

Enhetsuppdateringsagenten har inbäddade offentliga nycklar som används för alla enhetsuppdateringskompatibla enheter. Det här är *rotnycklarna.* Motsvarande privata nycklar styrs av Microsoft.

Microsoft genererar också ett offentligt/privat nyckelpar som inte ingår i enhetsuppdateringsagenten eller lagras på enheten. Det här är *signeringsnyckeln.*

När en uppdatering importeras till Enhetsuppdatering för IoT Hub och uppdateringsmanifestet genereras av tjänsten signerar tjänsten manifestet med signeringsnyckeln och inkluderar själva signeringsnyckeln, som signeras av en rotnyckel. När uppdateringsmanifestet skickas till enheten tar enhetsuppdateringsagenten emot följande signaturdata:

1. Själva signaturvärdet.
2. Algoritmen som används för att generera #1.
3. Information om den offentliga nyckeln för signeringsnyckeln som används för att generera #1.
4. Signaturen för den offentliga signeringsnyckeln i #3.
5. Id:t för den offentliga nyckeln för den rotnyckel som används för #3.
6. Algoritmen som används för att #4.

Enhetsuppdateringsagenten använder informationen som definieras ovan för att verifiera att signaturen för den offentliga signeringsnyckeln signeras av rotnyckeln. Enhetsuppdateringsagenten verifierar sedan att uppdateringsmanifestsignaturen har signerats av signeringsnyckeln. Om alla signaturer är korrekta är uppdateringsmanifestet betrott av enhetsuppdateringsagenten. Eftersom uppdateringsmanifestet innehåller de filhash-värden som motsvarar själva uppdateringsfilerna kan uppdateringsfilerna också vara betrodda om hash-värden matchar.

Med rot- och signeringsnycklar kan Microsoft regelbundet rulla signeringsnyckeln, vilket är en bra säkerhetspraxis.

### <a name="json-web-signature-jws"></a>JSON-webbsignatur (JWS)

`updateManifestSignature`används för att säkerställa att informationen i inte har `updateManifest` manipulerats. `updateManifestSignature`skapas med hjälp av en JSON-webbsignatur med JSON-webbnycklar, vilket möjliggör källverifiering. Signaturen är en Base64Url-kodad sträng med tre avsnitt avgränsade med ".".  Se [hjälpmetoderna jws_util.h för](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) parsning och verifiering av JSON-nycklar och token.

JSON-webbsignatur är en ofta använd [IETF-standard för](https://tools.ietf.org/html/rfc7515) signering av innehåll med JSON-baserade datastrukturer. Det är ett sätt att säkerställa integriteten för data genom att verifiera signaturen för data. Mer information finns i JSON Web Signature (JWS) [RFC 7515](https://www.rfc-editor.org/info/rfc7515).

### <a name="json-web-token"></a>JSON Web Token

JSON-webbtoken är en öppen, [branschstandardmetod](https://tools.ietf.org/html/rfc7519) för att representera anspråk på ett säkert sätt mellan två parter.

### <a name="root-keys"></a>Rotnycklar

Varje enhet för enhetsuppdatering innehåller en uppsättning rotnycklar. De här nycklarna är förtroenderoten för alla signaturer för enhetsuppdateringen. Alla signaturer måste vara kedjade genom någon av dessa rotnycklar för att anses vara legitima.

Uppsättningen med rotnycklar ändras med tiden eftersom det är lämpligt att regelbundet rotera signeringsnycklar av säkerhetsskäl. Därför måste enhetsuppdateringsagentens programvara uppdatera sig själv med den senaste uppsättningen rotnycklar. 

### <a name="signatures"></a>Signaturer

Alla signaturer kommer att kunna användas av en signeringsnyckel (offentlig) som signerats av en av rotnycklarna. Signaturen identifierar vilken rotnyckel som användes för att signera signeringsnyckeln. 

En enhetsuppdateringsagent måste verifiera signaturer genom att först verifiera att signeringsnyckelns signatur (offentlig) är korrekt, giltig och signerad av en av de godkända rotnycklarna. När signeringsnyckeln har verifierats kan själva signaturen verifieras med hjälp av den nu betrodda offentliga signeringsnyckeln.

Signeringsnycklar roteras mycket snabbare än rotnycklar, så förväntar dig meddelanden som signeras av olika olika signeringsnycklar. 

Återkallning av en signeringsnyckel hanteras av enhetsuppdateringstjänsten, så användarna bör inte försöka cachelagra signeringsnycklar. Använd alltid signeringsnyckeln som medföljer en signatur.

### <a name="receiving-updates"></a>Ta emot uppdateringar

Uppdateringsbegäranden som tas emot av en enhetsuppdateringsagent innehåller ett signerat dokument för uppdateringsmanifestet (UM). Agenten måste verifiera att signaturen för UM är korrekt och intakt. Detta görs genom att verifiera att UM-signaturens signeringsnyckel har signerats av en korrekt rotnyckel. När det är klart verifierar agenten UM-signaturen mot signeringsnyckeln.

När UM-signaturen har verifierats kan enhetsuppdateringsagenten lita på den som en "sanningskälla". Allt ytterligare säkerhetsförtroende kommer från den här källan. 

UM innehåller URL:er och filhash-värden för innehåll som ska laddas ned och installeras. När agenten har laddat ned en binär uppdatering måste den verifiera uppdateringen mot den filhash som finns i UM. Detta ger en transitiv förtroendemodell för nedladdningsverifiering. Det säkerställer inte bara att innehållet är intakt (inte ändrat), utan bekräftar också att det som laddades ned verkligen var det som var avsett att laddas ned. 

### <a name="securing-the-device"></a>Skydda enheten

Det är viktigt att se till att enhetsuppdateringsrelaterade säkerhetstillgångar är korrekt skyddade på enheten. Tillgångar som rotnycklar måste skyddas mot ändringar. Det finns olika sätt att göra detta, till exempel att använda säkerhetsenheter (TPM, SGX, HSM, andra säkerhetsenheter) eller till och med hårdkoda dem i enhetsuppdateringsagenten. Det senare kräver att enhetsuppdateringsagentens kod är digitalt signerad och att systemets stöd för kodintegritet är aktiverat för att skydda mot skadliga ändringar av agentkoden.

Ytterligare säkerhetsåtgärder kan vara befogade, till exempel att överta från komponent till komponent utförs på ett säkert sätt. Du kan till exempel registrera ett specifikt isolerat konto för att köra de olika komponenterna. Och begränsa nätverksbaserad kommunikation (t.ex. REST API)till endast localhost.

**[Nästa steg: Läs mer om hur enhetsuppdatering använder Azure RBAC](.\device-update-control-access.md)**