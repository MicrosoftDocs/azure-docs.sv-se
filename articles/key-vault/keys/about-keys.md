---
title: Om nycklar – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnitt och utvecklarinformation för nycklar.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: b9565095a40052a940d7a7b31f0fd3a27e0e75c2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815008"
---
# <a name="about-keys"></a>Om nycklar

Azure Key Vault två typer av resurser för att lagra och hantera kryptografiska nycklar. Valv stöder programvaruskyddade nycklar och HSM-skyddade nycklar (Hardware Security Module). Hanterade HSM:er stöder endast HSM-skyddade nycklar. 

|Resurstyp|Nyckelskyddsmetoder|Bas-URL för dataplansslutpunkt|
|--|--|--|
| **Valv** | Programvaruskyddad<br/><br/>och<br/><br/>HSM-skyddad (med Premium SKU)</li></ul> | https://{vault-name}.vault.azure.net |
| **Hanterade HSM:er** | HSM-skyddad | https://{hsm-name}.managedhsm.azure.net |
||||

- **Valv** – valv ger en enkel, distribuerad nyckelhanteringslösning med hög tillgång till flera klientorganisationszoner (där det finns tillgängligt), lämplig för de vanligaste molnprogramscenarierna.
- **Hanterade HSM:er** – Hanterad HSM tillhandahåller zonfjärr elastiska HSM:er med en enda klientorganisation (där det finns tillgängligt), HSM:er med hög tillgänglig lagring och hantering av dina kryptografiska nycklar. Passar bäst för program och användningsscenarier som hanterar nycklar med högt värde. Hjälper också till att uppfylla de strängaste säkerhets-, efterlevnads- och regelkraven. 

> [!NOTE]
> Med valv kan du också lagra och hantera flera typer av objekt som hemligheter, certifikat och lagringskontonycklar, förutom kryptografiska nycklar.

Kryptografiska nycklar i Key Vault representeras som JSON Web Key [JWK]-objekt. Specifikationerna JavaScript Object Notation JSON och JavaScript Object Signing and Encryption (UM) är:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON-webbkryptering (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON-webbalgoritmer (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON-webbsignatur (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

De grundläggande JWK/JWA-specifikationerna utökas också för att aktivera nyckeltyper som är unika för Azure Key Vault och hanterade HSM-implementeringar. 

HSM-skyddade nycklar (kallas även HSM-nycklar) bearbetas i en HSM (Hardware Security Module) och förblir alltid HSM-skyddsgräns. 

- Valv använder **FIPS 140-2 Level 2-verifierade** HSM:er för att skydda HSM-nycklar i en delad HSM-infrastruktur. 
- Managed HSM använder **FIPS 140-2 Level 3-verifierade** HSM-moduler för att skydda dina nycklar. Varje HSM-pool är en isolerad instans [](../managed-hsm/security-domain.md) för en enskild klientorganisation med en egen säkerhetsdomän som ger fullständig kryptografisk isolering från alla andra HSM:er som delar samma maskinvaruinfrastruktur.

Dessa nycklar skyddas i HSM-pooler med en enda klientorganisation. Du kan importera en RSA-, EC- och symmetrisk nyckel i mjuk form eller genom att exportera från en HSM-enhet som stöds. Du kan också generera nycklar i HSM-pooler. När du importerar HSM-nycklar med hjälp av metoden som beskrivs i [specifikationen för BYOK (Bring Your Own Key)](../keys/byok-specification.md)möjliggör det säkert transportnyckelmaterial till hanterade HSM-pooler. 

Mer information om geografiska gränser finns i [Microsoft Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Nyckeltyper och skyddsmetoder

Key Vault har stöd för RSA- och EC-nycklar. Managed HSM stöder RSA-, EC- och symmetriska nycklar. 

### <a name="hsm-protected-keys"></a>HSM-skyddade nycklar

|Nyckeltyp|Valv (endast Premium-SKU)|Hanterade HSM:er|
|--|--|--|
|**EC-HSM:** Elliptic Curve-nyckel | Stöds | Stöds|
|**RSA-HSM:** RSA-nyckel|Stöds|Stöds|
|**oct-HSM:** Symmetrisk nyckel|Stöds inte|Stöds|
|||

### <a name="software-protected-keys"></a>Programvaruskyddade nycklar

|Nyckeltyp|Valv|Hanterade HSM:er|
|--|--|--|
**RSA: RSA-nyckel**"Programvaruskyddad"|Stöds|Stöds inte
**EC:**"Programvaruskyddad" Elliptic Curve-nyckel|Stöds|Stöds inte
|||

### <a name="compliance"></a>Efterlevnad

|Nyckeltyp och mål|Efterlevnad|
|---|---|
|Programvaruskyddade nycklar i valv (Premium & Standard-SKU:er) | FIPS 140-2 Level 1|
|HSM-skyddade nycklar i valv (Premium SKU)| FIPS 140-2 nivå 2|
|HSM-skyddade nycklar i Managed HSM|FIPS 140-2 Level 3|
|||



Se [Nyckeltyper, algoritmer och åtgärder för](about-keys-details.md) information om varje nyckeltyp, algoritmer, åtgärder, attribut och taggar.

## <a name="next-steps"></a>Nästa steg
- [Om Key Vault](../general/overview.md)
- [Om Managed HSM](../managed-hsm/overview.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Key Vault REST API översikt](../general/about-keys-secrets-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
