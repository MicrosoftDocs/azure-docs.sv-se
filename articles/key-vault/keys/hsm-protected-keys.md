---
title: Så här skapar du & transfer HSM-skyddade nycklar – Azure Key Vault
description: Lär dig hur du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: a7e709ba9a4de5ff77524a2d2b1b64a5933131a2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489420"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importera HSM-skyddade nycklar till Key Vault

När du använder Azure Key Vault kan du med extra garantier importera eller generera nycklar i HSM: er (Hardware Security modules) som aldrig lämnar HSM-gränser. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2 verifieras) för att skydda dina nycklar.

Den här funktionen är inte tillgänglig för Azure Kina 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../general/overview.md)  
> En vägledning för att komma igång, vilket innefattar att skapa ett nyckel valv för HSM-skyddade nycklar, finns i [Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSM: er som stöds

Överföring av HSM-skyddade nycklar till Key Vault stöds via två olika metoder beroende på vilken HSM: er du använder. Använd tabellen nedan för att avgöra vilken metod som ska användas för din HSM: er för att generera och sedan överföra egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

|Leverantörs namn|Typ av leverantör|HSM-modeller som stöds|Överförings metod som stöds av HSM-nyckel|
|---|---|---|---|
|[Hjälp programmet nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Tillverkare<br/>HSM som en tjänst|<ul><li>nshield maskinvarusäkerhetsmodul-serien HSM: er</li><li>nshield maskinvarusäkerhetsmodul som en tjänst</ul>|**Metod 1:** [Hjälp programmet nCipher BYOK](hsm-protected-keys-ncipher.md) (inaktuell)<br/>**Metod 2:** [Använd New BYOK-metod](hsm-protected-keys-byok.md) (rekommenderas)|
|Thales|Tillverkare|<ul><li>Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare</li></ul>| [Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>Self-Defending nyckel hanterings tjänst (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Marvell|Tillverkare|Alla LiquidSecurity-HSM: er med<ul><li>Version 2.0.4 eller senare av inbyggd program vara</li><li>Inbyggd program vara version 3,2 eller senare</li></ul>|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och-modeller, inklusive<ul><li>Hjälp programmet nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på Cryptomathic-webbplatsen](https://www.cryptomathic.com/azurebyok)|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Securosys SA|Tillverkare<br/>HSM som en tjänst|Primus HSM-familjen, Securosys-moln HSM|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och-modeller, inklusive<ul><li>Utimaco</li><li>Thales</li><li>Hjälp programmet nCipher</li></ul>Mer [information finns på StorMagic-webbplatsen](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|IBM|Tillverkare|IBM 476x, CryptoExpress|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Utimaco|Tillverkare<br/>HSM som en tjänst|u. förtroende ankare, CryptoServer|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Nästa steg

* Granska [Key Vault säkerhets översikt](../general/security-overview.md) för att säkerställa säkerhet, hållbarhet och övervakning för dina nycklar.
* Se [BYOK-specifikationen](./byok-specification.md) för en fullständig beskrivning av den nya BYOK-metoden