---
title: Så här genererar & överför HSM-skyddade nycklar – Azure Key Vault
description: Lär dig hur du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: 171e0c03dc6f246d0f56d11f793ca711b0082f49
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588300"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importera HSM-skyddade nycklar till Key Vault

När du använder en Azure Key Vault kan du importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault nCipher nShield-familjen HSM:er (FIPS 140-2 Level 2-verifierad) för att skydda dina nycklar.

Den här funktionen är inte tillgänglig för Azure China 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../general/overview.md)  
> En självstudiekurs som hjälper dig att komma igång med att skapa ett nyckelvalv för HSM-skyddade nycklar finns i [Vad är Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSM:er som stöds

Överföring av HSM-skyddade nycklar till Key Vault stöds via två olika metoder beroende på vilka HSM:er du använder. Använd tabellen nedan för att avgöra vilken metod som ska användas för dina HSM:er för att generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

|Leverantörsnamn|Leverantörstyp|HSM-modeller som stöds|Överföringsmetod för HSM-nyckel som stöds|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Tillverkare<br/>HSM som en tjänst|<ul><li>nShield-familjen med HSM:er</li><li>nShield som en tjänst</ul>|**Metod 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (inaktuell). Den här metoden stöds inte efter <strong>30 juni 2021</strong><br/>**Metod 2: Använd** [ny BYOK-metod](hsm-protected-keys-byok.md) (rekommenderas)|
|Thales|Tillverkare|<ul><li>Firmware HSM 7-familjen med version 7.3 eller senare av inbyggd programvara</li></ul>| [Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|Marvell|Tillverkare|Alla LiquidSecurity HSM:er med<ul><li>Version 2.0.4 eller senare av inbyggd programvara</li><li>Version 3.2 eller senare av inbyggd programvara</li></ul>|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|Kryptografisk|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på kryptografiwebbplatsen](https://www.cryptomathic.com/azurebyok)|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|Sec secsys SA|Tillverkare<br/>HSM som en tjänst|Primus HSM-familj, Sec secesys clouds HSM|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Mer [information finns på StorMagic-webbplatsen](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|IBM|Tillverkare|IBM 476x, CryptoExpress|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|Utimaco|Tillverkare<br/>HSM som en tjänst|u.trust Anchor, CryptoServer|[Använda ny BYOK-metod](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Nästa steg

* Granska Key Vault [säkerhetsöversikten för](../general/security-overview.md) att säkerställa säkerhet, hållbarhet och övervakning för dina nycklar.
* En fullständig [beskrivning av den nya BYOK-metoden](./byok-specification.md) finns i BYOK-specifikationen