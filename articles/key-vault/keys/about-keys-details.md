---
title: Nyckel typer, algoritmer och åtgärder – Azure Key Vault
description: Nyckel typer, algoritmer och åtgärder som stöds (information).
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 675c4f04ece322000ae0ebb44d6291c455db9397
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133284"
---
# <a name="key-types-algorithms-and-operations"></a>Viktiga typer, algoritmer och åtgärder

Key Vault stöder två resurs typer: valv och hanterade HSM: er. Båda resurs typerna stöder olika krypterings nycklar. För att se en översikt över vilka nyckel typer som stöds, skydds typer per resurs typ, se [om nycklar](about-keys.md).

Följande tabell innehåller en översikt över nyckel typer och algoritmer som stöds.

|Nyckel typer/storlekar/kurvor| Kryptera/dekryptera<br>(Radbryt/packa upp) | Signera/verifiera | 
| --- | --- | --- |
|EG-P256, EC-P256K, EG-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP – 256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128-bit, 256-bitars <br/>(Endast hanterad HSM)| AES-KW<br>AES-GCM<br>AES-CBC| NA| 
|||

##  <a name="ec-algorithms"></a>EC-algoritmer
 Följande algoritms identifierare stöds med EC-HSM-nycklar

### <a name="curve-types"></a>Kurv typer

-   **P-256** – NIST kurva P-256, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 k** – SECP256K1 för SEC-kurvan, definierat vid [SEK 2: rekommenderade Elliptic för kurv parametrar](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – NIST kurva P-384, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – NIST kurva P-521, definieras i [DSS FIPS pub-186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>SIGNERA/VERIFIERA

-   **ES256** -ECDSA för SHA-256-sammandrag och nycklar som skapats med kurva P-256. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA för SHA-256-sammandrag och nycklar som skapats med kurva P-256 kB. Den här algoritmen väntar på standardisering.
-   **ES384** -ECDSA för SHA-384-sammandrag och nycklar som skapats med kurva P-384. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA för SHA-512-sammandrag och nycklar som skapats med kurva P-521. Den här algoritmen beskrivs på [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>RSA-algoritmer  
 Följande algoritms identifierare stöds med RSA-och RSA-HSM-nycklar  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, KRYPTERA/DEKRYPTERA

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] nyckel kryptering  
-   **RSA-OAEP** – RSAES med optimalt OAEP (asymmetrisk krypterings utfyllnad) [RFC3447], med standard parametrarna som anges i RFC 3447 i avsnitt A. 2.1. Dessa standard parametrar använder en hash-funktion i SHA-1 och en mask-funktion för MGF1 med SHA-1.  
-  **RSA-OAEP-256** – RSAES med optimal asymmetriska krypterings utfyllnad med hash-funktionen SHA-256 och en mask funktion för MGF1 med SHA-256

### <a name="signverify"></a>SIGNERA/VERIFIERA

-   **PS256** -RSASSA-PSS med sha-256 och MGF1 med sha-256, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS med sha-384 och MGF1 med sha-384, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS med sha-512 och MGF1 med sha-512, enligt beskrivningen i [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-V1_5 använder SHA-256. Det program som angavs Digest-värde måste beräknas med SHA-256 och måste vara 32 byte långt.  
-   **RS384** -RSASSA-PKCS-V1_5 använder SHA-384. Det program som angavs Digest-värde måste beräknas med SHA-384 och måste vara 48 byte långt.  
-   **RS512** -RSASSA-PKCS-V1_5 använder SHA-512. Det program som angavs Digest-värde måste beräknas med SHA-512 och måste vara 64 byte långt.  
-   **RSNULL** – se [RFC2437](https://tools.ietf.org/html/rfc2437), ett specialiserat användnings fall för att aktivera vissa TLS-scenarier.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Symmetriska nyckel algoritmer (endast hanterad HSM)
- **AES-kW** – AES Key wrap ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM** -AES-kryptering i Galois Counter mode ([NIST SP 800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC** -AES-kryptering i läget cipher block Chaining ([NIST SP 800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> Den aktuella AES-GCM-implementeringen och motsvarande API: er är experimentella. Implementeringen och API: erna kan ändras väsentligen i framtida iterationer. 

##  <a name="key-operations"></a>Nyckel åtgärder

Hanterad HSM stöder följande åtgärder på nyckel objekt:  

-   **Skapa**: tillåter att en klient skapar en nyckel i Key Vault. Nyckelns värde genereras av Key Vault och lagras, och frigörs inte till klienten. Asymmetriska nycklar kan skapas i Key Vault.  
-   **Importera**: tillåter att en klient importerar en befintlig nyckel till Key Vault. Asymmetriska nycklar kan importeras till Key Vault att använda ett antal olika förpacknings metoder inom en JWK-konstruktion. 
-   **Uppdatera**: tillåter en klient med tillräcklig behörighet att ändra de metadata (nyckelattribut) som är associerade med en nyckel som tidigare lagrats i Key Vault.  
-   **Ta bort**: tillåter en klient med tillräcklig behörighet att ta bort en nyckel från Key Vault.  
-   **Lista**: tillåter att en klient listar alla nycklar i en specifik Key Vault.  
-   **List versioner**: tillåter att en klient listar alla versioner av en specifik nyckel i en specifik Key Vault.  
-   **Get**: tillåter att en klient hämtar offentliga delar av en viss nyckel i en Key Vault.  
-   **Säkerhets kopiering**: exporterar en nyckel i ett skyddat formulär.  
-   **Återställ**: importerar en tidigare säkerhetskopierad nyckel.  

Mer information finns i [nyckel åtgärder i referensen Key Vault REST API](/rest/api/keyvault).  

När en nyckel har skapats i Key Vault kan följande kryptografiska åtgärder utföras med nyckeln:  

-   **Logga och verifiera**: den här åtgärden är "signera hash" eller "verifiera hash", eftersom Key Vault inte stöder hashing av innehåll som en del av att skapa signaturer. Program ska Hasha data för att signeras lokalt och sedan begära att Key Vault signera hashen. Verifiering av signerade hash-värden stöds som en bekvämlighets åtgärd för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa program prestanda bör du kontrol lera att åtgärder utförs lokalt.  
-   **Nyckel kryptering/inbäddning**: en nyckel som lagras i Key Vault kan användas för att skydda en annan nyckel, vanligt vis en symmetrisk innehålls krypterings nyckel (CEK). När nyckeln i Key Vault är asymmetrisk, används nyckel kryptering. Till exempel motsvarar RSA-OAEP och WRAPKEY/UNWRAPKEY-åtgärderna dekryptera/DEKRYPTERA. När nyckeln i Key Vault är symmetrisk används nyckel brytning. Till exempel AES-KW. WRAPKEY-åtgärden stöds som en bekvämlighet för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör WRAPKEY åtgärder utföras lokalt.  
-   **Kryptera och dekryptera**: en nyckel som lagras i Key Vault kan användas för att kryptera eller dekryptera ett enda data block. Storleken på blocket bestäms av nyckel typen och den valda krypteringsalgoritmen. Krypterings åtgärden tillhandahålls för enkelhetens skull, för program som kanske inte har åtkomst till [offentligt] nyckel material. För bästa prestanda bör KRYPTERINGs åtgärder utföras lokalt.  

Även om WRAPKEY/UNWRAPKEY med asymmetriska nycklar kan verka överflödig (eftersom åtgärden motsvarar kryptering/dekryptering) är det viktigt att använda DISTINCT-åtgärder. Distinktionen ger semantisk och avskiljande av dessa åtgärder och konsekvens när andra nyckel typer stöds av tjänsten.  

Key Vault stöder inte EXPORT åtgärder. När en nyckel har skapats i systemet går den inte att extrahera eller så har dess nyckel material ändrats. Användare av Key Vault kan dock kräva sin nyckel för andra användnings fall, till exempel när det har tagits bort. I det här fallet kan de använda säkerhets kopierings-och återställnings åtgärderna för att exportera/importera nyckeln i ett skyddat formulär. Nycklar som skapats av säkerhets kopierings åtgärden kan inte användas utanför Key Vault. Alternativt kan IMPORT åtgärden användas mot flera Key Vault instanser.  

Användare kan begränsa de kryptografiska åtgärder som Key Vault stöder per nyckel med hjälp av egenskapen key_ops för JWK-objektet.  

Mer information om JWK-objekt finns i [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Nyckelattribut

Utöver nyckelmaterialet kan följande attribut anges. I en JSON-begäran krävs attributen nyckelord och klammerparenteser, {}, även om det inte finns några angivna attribut.  

- *aktive rad*: boolesk, valfritt, standardvärdet är **True**. Anger om nyckeln är aktive rad och användbar för kryptografiska åtgärder. Det *aktiverade* attributet används tillsammans med *NBF* och *exp*. När en åtgärd sker mellan *NBF* och *exp*, tillåts den endast om *aktive rad* är inställd på **True**. Åtgärder utanför *NBF*  /  *exp* -fönstret tillåts inte automatiskt, förutom vissa åtgärds typer under [vissa förhållanden](#date-time-controlled-operations).
- *NBF*: IntDate, valfritt, standard är nu. Attributet *NBF* (inte före) anger tiden före vilken nyckeln inte får användas för kryptografiska åtgärder, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *NBF* -attributet kräver att aktuellt datum/tid måste vara efter eller lika med det icke-före-datum/-tid som anges i *NBF* -attributet. Key Vault kan ge vissa små Leeway, vanligt vis inte fler än några minuter, för att kontona ska kunna användas för klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *exp*: IntDate, valfritt, standard är "alltid". Attributet *exp* (förfallo tid) anger förfallo tid för eller efter vilken nyckeln inte får användas för kryptografisk drift, förutom vissa åtgärds typer under [särskilda villkor](#date-time-controlled-operations). Bearbetningen av *exp* -attributet kräver att aktuellt datum/tid måste vara före det datum/tid som anges i attributet *exp* . Key Vault kan vara en del liten Leeway, vanligt vis inte mer än några minuter, för att kunna använda klock skevning. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller viktiga attribut:  

- *skapad*: IntDate, valfritt. Attributet *create* anger när den här versionen av nyckeln skapades. Värdet är null för nycklar som skapats innan det här attributet läggs till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  
- *uppdaterad*: IntDate, valfritt. Det *uppdaterade* attributet indikerar när den här versionen av nyckeln uppdaterades. Värdet är null för nycklar som senast uppdaterades innan det här attributet lades till. Värdet måste vara ett tal som innehåller ett IntDate-värde.  

Mer information om IntDate och andra data typer finns i [om nycklar, hemligheter och certifikat: [data typer](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Kontrollerade åtgärder för datum/tid

Nycklar som inte är giltiga och som har upphört att gälla utanför *NBF*  /  *exp* -fönstret fungerar för att **dekryptera**, **packa** upp och **Verifiera** åtgärder (returnerar inte 403, förbjudet). Rationellt för att använda tillstånd som inte är giltigt är att tillåta att en nyckel testas före produktions användningen. Rationellt för att använda tillståndet upphör att gälla är att tillåta återställnings åtgärder för data som skapades när nyckeln var giltig. Du kan också inaktivera åtkomst till en nyckel med Key Vault-principer eller genom att uppdatera det *aktiverade* nyckelattributet till **falskt**.

Mer information om data typer finns i [data typer](../general/about-keys-secrets-certificates.md#data-types).

Mer information om andra möjliga attribut finns i [JSON-webbnyckeln (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Nyckel etiketter

Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som var och en kan ha ett 256-namn och ett värde på 256.  

> [!NOTE] 
> Taggarna kan läsas av en anropare om de har *listan* eller *får* behörighet till nyckeln.

##  <a name="key-access-control"></a>Nyckelåtkomstkontroll

Åtkomst kontroll för nycklar som hanteras av Key Vault anges på nivån för en Key Vault som fungerar som behållare för nycklar. Åtkomst kontroll principen för nycklar är distinkt från åtkomst kontroll principen för hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att hålla nycklar, och de krävs för att upprätthålla lämplig segmentering och hantering av nycklar. Åtkomst kontroll för nycklar är oberoende av åtkomst kontroll för hemligheter.  

Följande behörigheter kan beviljas, per användare/tjänstens huvud konto, i åtkomst kontroll posten i ett valv. Dessa behörigheter speglar noggrant de åtgärder som tillåts för ett nyckel objekt.  Att bevilja åtkomst till ett huvud namn för tjänsten i Key Vault är en Databasmigrering-åtgärd och det förblir detsamma för alla Azure-prenumerationer. Du kan använda den för att distribuera så många certifikat som du vill. 

- Behörigheter för nyckel hanterings åtgärder
  - *Hämta*: Läs den offentliga delen av en nyckel, plus dess attribut
  - *lista*: visar nycklar eller versioner av en nyckel som lagras i ett nyckel valv
  - *Uppdatera*: uppdatera attributen för en nyckel
  - *skapa*: skapa nya nycklar
  - *Importera*: importera en nyckel till ett nyckel valv
  - *ta bort*: ta bort nyckelobjektet
  - *återställa*: Återställ en borttagen nyckel
  - *säkerhets kopiering*: säkerhetskopiera en nyckel i ett nyckel valv
  - *återställning*: återställa en säkerhets kopie rad nyckel till ett nyckel valv

- Behörigheter för kryptografiska åtgärder
  - *dekryptera*: Använd nyckeln för att ta bort skyddet för en sekvens med byte
  - *kryptera*: Använd nyckeln för att skydda en godtycklig sekvens med byte
  - *unwrapKey*: Använd nyckeln för att ta bort skyddet för figursatta symmetriska nycklar
  - *wrapKey*: Använd nyckeln för att skydda en symmetrisk nyckel
  - *Verifiera*: Använd nyckeln för att verifiera sammandrag  
  - *tecken*: Använd nyckeln för att signera sammandrag
    
- Behörigheter för privilegierade åtgärder
  - *Rensa*: Rensa (ta bort permanent) en borttagen nyckel

Mer information om hur du arbetar med nycklar finns [i nyckel åtgärder i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – skapa eller uppdatera](/rest/api/keyvault/vaults/createorupdate) och [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Nästa steg
- [Om Key Vault](../general/overview.md)
- [Om Managed HSM](../managed-hsm/overview.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Översikt över Key Vault REST API](../general/about-keys-secrets-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
