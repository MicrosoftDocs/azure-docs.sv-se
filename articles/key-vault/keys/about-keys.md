---
title: Om nycklar – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och information om utvecklare för nycklar.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095988"
---
# <a name="about-keys"></a>Om nycklar

Azure Key Vault tillhandahåller två typer av resurser för att lagra och hantera kryptografiska nycklar. Valv stöder Software-Protected-och HSM-skyddade nycklar (Hardware Security Module). Hanterade HSM: er stöder endast HSM-skyddade nycklar. 

|Resurstyp|Nyckel skydds metoder|Bas-URL för data Plans slut punkt|
|--|--|--|
| **Valv** | Program vara-skyddad<br/><br/>och<br/><br/>HSM-skyddad (med Premium-SKU)</li></ul> | https://{valv-Name}. valv. Azure. net |
| * * Hanterad HSM: er * * | HSM-skyddad | https://{HSM-Name}. managedhsm. Azure. net |
||||

- **Valv** – valven ger dig en låg kostnad, enkel att distribuera, flera klient organisationer, zoner – flexibel (där det är tillgängligt), nyckel hanterings lösning med hög tillgänglighet som passar för de flesta vanliga moln program scenarier.
- **Hanterad HSM: er** -hanterad HSM tillhandahåller en enda klient, zon flexibel (där tillgänglig), hög tillgänglig HSM: er för att lagra och hantera dina kryptografiska nycklar. Lämplig för program-och användnings scenarier som hanterar nycklar med högt värde. Bidrar också till att uppfylla de mest strikta kraven för säkerhet, efterlevnad och regler. 

> [!NOTE]
> Med valv kan du också lagra och hantera flera typer av objekt som hemligheter, certifikat och lagrings konto nycklar, utöver de kryptografiska nycklarna.

Kryptografiska nycklar i Key Vault representeras som JSON-webbnyckel [JWK]-objekt. JOSE-specifikationerna (JSON) och Java Script Object signing and Encryption () är: JavaScript Object Notation

-   [JSON-webbnyckel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON-webbkryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON-webbalgoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON-webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

De grundläggande JWK/JWA-specifikationerna utökas också för att aktivera nyckel typer som är unika för Azure Key Vault och hanterade HSM-implementeringar. 

HSM-skyddade nycklar (kallas även HSM-nycklar) bearbetas i en HSM-modul (Hardware Security Module) och bibehålls alltid HSM-skydds gränser. 

- Valv använder **FIPS 140-2 nivå 2** -verifierade HSM: er för att skydda HSM-nycklar i delad HSM-Server infrastruktur. 
- Hanterad HSM använder **FIPS 140-2 nivå 3** -VERIFIERAde HSM-moduler för att skydda dina nycklar. Varje HSM-pool är en isolerad instans av en enskild klient med en egen [säkerhets domän](../managed-hsm/security-domain.md) som tillhandahåller fullständig kryptografisk isolering från alla andra HSM: er som delar samma maskin varu infrastruktur.

Dessa nycklar skyddas i HSM-pooler med en enda klient. Du kan importera en RSA-, EC-och symmetrisk nyckel, i mjuk form eller genom att exportera från en HSM-enhet som stöds. Du kan också generera nycklar i HSM-pooler. När du importerar HSM-nycklar med hjälp av metoden som beskrivs i [BYOK (ta med din egen nyckel)](../keys/byok-specification.md), aktive ras Secure transport Key material till HANTERAde HSM-pooler. 

Mer information om geografiska gränser finns [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Nyckel typer och skydds metoder

Key Vault stöder RSA-och EG-nycklar. Hanterad HSM stöder RSA, EC och symmetriska nycklar. 

### <a name="hsm-protected-keys"></a>HSM-skyddade nycklar

|Nyckeltyp|Valv (endast Premium-SKU)|Hanterade HSM: er|
|--|--|--|
|**EC-HSM**: Elliptic kurv nyckel | Stöds | Stöds|
|**RSA-HSM**: RSA-nyckel|Stöds|Stöds|
|**okt-HSM**: symmetrisk nyckel|Stöds inte|Stöds|
|||

### <a name="software-protected-keys"></a>Program vara – skyddade nycklar

|Nyckeltyp|Valv|Hanterade HSM: er|
|--|--|--|
**RSA**: "program vara-skyddad" RSA-nyckel|Stöds|Stöds inte
**EG**: "Software-Protected" Elliptic Curve Key|Stöds|Stöds inte
|||

### <a name="compliance"></a>Efterlevnad

|Nyckel typ och mål|Efterlevnad|
|---|---|
|Program varu skyddade nycklar i valv (Premium & standard SKU: er) | FIPS 140-2-nivå 1|
|HSM-skyddade nycklar i valv (Premium-SKU)| FIPS 140-2 nivå 2|
|HSM-skyddade nycklar i hanterad HSM|FIPS 140-2-nivå 3|
|||



Se [nyckel typer, algoritmer och åtgärder](about-keys-details.md) för information om varje nyckel typ, algoritmer, åtgärder, attribut och taggar.

## <a name="next-steps"></a>Nästa steg
- [Om Key Vault](../general/overview.md)
- [Om Managed HSM](../managed-hsm/overview.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Översikt över Key Vault REST API](../general/about-keys-secrets-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
