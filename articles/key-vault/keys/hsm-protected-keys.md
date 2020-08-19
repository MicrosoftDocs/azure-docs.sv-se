---
title: Så här skapar du & transfer HSM-skyddade nycklar – Azure Key Vault
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585500"
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
|[Hjälp programmet nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Tillverkare<br/>HSM som en tjänst|<ul><li>nshield maskinvarusäkerhetsmodul-serien HSM: er</li><li>nshield maskinvarusäkerhetsmodul som en tjänst</ul>|**Metod 1:** [Hjälp programmet nCipher BYOK](hsm-protected-keys-ncipher.md) (med stark attestering för nyckel import och HSM-validering)<br/>**Metod 2:** [Använd New BYOK-metod](hsm-protected-keys-byok.md) |
|Thales|Tillverkare|<ul><li>Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare</li></ul>| [Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>SDKMS (Self-försvarande Key Management Service)</li><li>Equinix SmartKey</li></ul>|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Marvell|Tillverkare|Alla LiquidSecurity-HSM: er med<ul><li>Version 2.0.4 eller senare av inbyggd program vara</li><li>Inbyggd program vara version 3,2 eller senare</li></ul>|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och-modeller, inklusive<ul><li>Hjälp programmet nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på Cryptomathic-webbplatsen](https://www.cryptomathic.com/azurebyok)|[Använd ny BYOK-metod](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Nästa steg

* Följ [Key Vault bästa praxis](../general/best-practices.md) för att säkerställa säkerhet, hållbarhet och övervakning för dina nycklar.
* Se [BYOK-specifikationen](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) för en fullständig beskrivning av den nya BYOK-metoden
