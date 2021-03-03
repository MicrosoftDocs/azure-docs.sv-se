---
title: Så här skapar du & transfer HSM-skyddade nycklar – BYOK – Azure Key Vault
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som du kan använda med Azure Key Vault. Kallas även för att ta med din egen nyckel (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 1e7ea0dc929fdbb4ca306405e6ed8993ed2e4afe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "100386109"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importera HSM-skyddade nycklar till Key Vault (BYOK)

För extra trygghet när du använder Azure Key Vault kan du importera eller generera en nyckel i en modul för maskin varu säkerhet (HSM). nyckeln lämnar aldrig HSM-gränser. Det här scenariot kallas ofta för att *ta med din egen nyckel* (BYOK). Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2, verifierade) för att skydda dina nycklar.

Använd informationen i den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som du kan använda med Azure Key Vault.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure Kina 21Vianet. 
> 
> Den här import metoden är endast tillgänglig för [HSM: er som stöds](#supported-hsms). 

Mer information och en själv studie kurs om hur du kommer igång med Key Vault (inklusive hur du skapar ett nyckel valv för HSM-skyddade nycklar) finns i [Vad är Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Översikt

Här är en översikt över processen. De åtgärder som ska utföras beskrivs senare i artikeln.

* I Key Vault genererar du en nyckel (kallas *nyckel utbytes nyckel* (KEK)). KEK måste vara en RSA-HSM-nyckel som bara har `import` nyckel åtgärden. Endast Key Vault Premium SKU stöder RSA-HSM-nycklar.
* Hämta den offentliga nyckeln KEK som en. PEM-fil.
* Överför den offentliga KEK-nyckeln till en frånkopplad dator som är ansluten till en lokal HSM.
* I den frånkopplade datorn använder du verktyget BYOK som tillhandahålls av din HSM-leverantör för att skapa en BYOK-fil. 
* Mål nyckeln krypteras med en KEK, som förblir krypterad tills den överförs till Key Vault HSM. Endast den krypterade versionen av nyckeln lämnar den lokala HSM.
* En KEK som genereras inuti en Key Vault HSM kan inte exporteras. HSM: er tillämpar regeln att ingen tydlig version av en KEK finns utanför en Key Vault HSM.
* KEK måste finnas i samma nyckel valv där mål nyckeln kommer att importeras.
* När BYOK-filen överförs till Key Vault, använder en Key Vault HSM den privata KEK-nyckeln för att dekryptera mål nyckel materialet och importera det som en HSM-nyckel. Den här åtgärden sker helt i en Key Vault HSM. Mål nyckeln finns alltid kvar i HSM-skyddets gränser.

## <a name="prerequisites"></a>Förutsättningar

I följande tabell visas förutsättningar för att använda BYOK i Azure Key Vault:

| Krav | Mer information |
| --- | --- |
| En Azure-prenumeration |Om du vill skapa ett nyckel valv i Azure Key Vault behöver du en Azure-prenumeration. [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/). |
| En Key Vault Premium-SKU för import av HSM-skyddade nycklar |Mer information om tjänst nivåer och funktioner i Azure Key Vault finns [Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/). |
| En HSM från listan över HSM: er som stöds och ett BYOK-verktyg och instruktioner från din HSM-leverantör | Du måste ha behörighet för en HSM och grundläggande kunskap om hur du använder HSM. Se [HSM: er som stöds](#supported-hsms). |
| Azure CLI-version 2.1.0 eller senare | Se [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).|

## <a name="supported-hsms"></a>HSM: er som stöds

|Leverantörs namn|Typ av leverantör|HSM-modeller som stöds|Mer information|
|---|---|---|---|
|Hjälp programmet nCipher|Tillverkare<br/>HSM som en tjänst|<ul><li>nshield maskinvarusäkerhetsmodul-serien HSM: er</li><li>nshield maskinvarusäkerhetsmodul som en tjänst</ul>|[Hjälp programmet nCipher nytt BYOK-verktyg och dokumentation](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Tillverkare|<ul><li>Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare</li></ul>| [Luna BYOK-verktyg och dokumentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>Self-Defending nyckel hanterings tjänst (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Exportera SDKMS-nycklar till moln leverantörer för BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Tillverkare|Alla LiquidSecurity-HSM: er med<ul><li>Version 2.0.4 eller senare av inbyggd program vara</li><li>Inbyggd program vara version 3,2 eller senare</li></ul>|[Marvell BYOK-verktyg och dokumentation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och-modeller, inklusive<ul><li>Hjälp programmet nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på Cryptomathic-webbplatsen](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK-verktyg och dokumentation](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Tillverkare<br/>HSM som en tjänst|Primus HSM-familjen, Securosys-moln HSM|[Primus BYOK-verktyg och dokumentation](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och-modeller, inklusive<ul><li>Utimaco</li><li>Thales</li><li>Hjälp programmet nCipher</li></ul>Mer [information finns på StorMagic-webbplatsen](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS och Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Tillverkare|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Tillverkare<br/>HSM som en tjänst|u. förtroende ankare, CryptoServer|[Utimaco BYOK-verktyg och integrerings guide](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Nyckeltyper som stöds

|Nyckelnamn|Nyckeltyp|Nyckel storlek/kurva|Ursprung|Beskrivning|
|---|---|---|---|---|
|Nyckel utbytes nyckel (KEK)|RSA| 2 048-bitars<br />3 072-bitars<br />4 096-bitars|Azure Key Vault HSM|Ett HSM-backat RSA-nyckelpar som genererades i Azure Key Vault|
|Mål nyckel|
||RSA|2 048-bitars<br />3 072-bitars<br />4 096-bitars|Vendor HSM|Nyckeln som ska överföras till Azure Key Vault HSM|
||EC|P-256<br />P-384<br />P-521|Vendor HSM|Nyckeln som ska överföras till Azure Key Vault HSM|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generera och överför din nyckel till Key Vault HSM

Så här genererar och överför du nyckeln till en Key Vault HSM:

* [Steg 1: skapa en KEK](#step-1-generate-a-kek)
* [Steg 2: Ladda ned den offentliga nyckeln KEK](#step-2-download-the-kek-public-key)
* [Steg 3: generera och Förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: överför din nyckel till Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Steg 1: skapa en KEK

En KEK är en RSA-nyckel som genereras i en Key Vault HSM. KEK används för att kryptera den nyckel som du vill importera ( *mål* nyckeln).

KEK måste vara:
- En RSA-HSM-nyckel (2 048-bitar; 3 072-bit; eller 4 096-bitars)
- Genereras i samma nyckel valv där du tänker importera mål nyckeln
- Skapat med tillåtna nyckel åtgärder inställt på `import`

> [!NOTE]
> KEK måste ha ' import ' som den enda tillåtna nyckel åtgärden. import är ömsesidigt uteslutande med alla andra nyckel åtgärder.

Använd kommandot [AZ Key Vault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create) för att skapa en KEK som har nyckel åtgärder inställt på `import` . Registrera nyckel identifieraren ( `kid` ) som returneras från följande kommando. (Du kommer att använda `kid` värdet i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Steg 2: Ladda ned den offentliga nyckeln KEK

Använd [AZ Key Vault Key Download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download) för att hämta den offentliga KEK-nyckeln till en. PEM-fil. Mål nyckeln som du importerar krypteras med hjälp av den offentliga nyckeln KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Överför filen KEKforBYOK. PublicKey. pem till din frånkopplade dator. Du kommer att behöva den här filen i nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: generera och Förbered din nyckel för överföring

Se din HSM-leverantörs dokumentation för att ladda ned och installera BYOK-verktyget. Följ anvisningarna från din HSM-leverantör för att generera en mål nyckel och skapa sedan ett nyckel överförings paket (en BYOK-fil). Verktyget BYOK använder `kid` från [steg 1](#step-1-generate-a-kek) och filen KEKforBYOK. PublicKey. pem som du laddade ned i [steg 2](#step-2-download-the-kek-public-key) för att generera en krypterad mål nyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna datorn.

> [!NOTE] 
> Det finns inte stöd för att importera RSA 1 024-bitars nycklar. Det går inte att importera Elliptic-kurv nyckeln med kurva P-256 kB.
> 
> **Känt problem**: det går bara att importera en RSA 4K-mål nyckel från Luna HSM: er med den inbyggda program varan 7.4.0 eller senare.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Steg 4: överför din nyckel till Azure Key Vault

Slutför nyckel importen genom att överföra nyckel överförings paketet (en BYOK-fil) från den frånkopplade datorn till den Internet-anslutna datorn. Använd kommandot [AZ Key Vault Key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import) för att överföra BYOK-filen till Key Vault HSM.

Om du vill importera en RSA-nyckel använder du följande kommando. Parameter--kty är valfritt och är som standard "RSA-HSM".
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om du vill importera en EG-nyckel måste du ange nyckel typ och kurv namn.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om överföringen lyckas, visar Azure CLI egenskaperna för den importerade nyckeln.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i ditt nyckel valv. Mer information finns i [detta pris-och funktions jämförelse](https://azure.microsoft.com/pricing/details/key-vault/).



