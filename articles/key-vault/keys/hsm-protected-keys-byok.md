---
title: Så här genererar & överför HSM-skyddade nycklar – BYOK – Azure Key Vault
description: Den här artikeln hjälper dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK (Bring Your Own Key).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: f1b5d6499594e9026e1615be5361c52c9ce2f4ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774817"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importera HSM-skyddade nycklar till Key Vault (BYOK)

För ytterligare säkerhet när du använder Azure Key Vault kan du importera eller generera en nyckel i en maskinvarusäkerhetsmodul (HSM). nyckeln lämnar aldrig HSM-gränsen. Det här scenariot kallas ofta *BYOK (Bring Your Own* Key). Key Vault använder nCipher nShield-familjen HSM:er (FIPS 140-2 Level 2-verifierad) för att skydda dina nycklar.

Använd informationen i den här artikeln när du planerar, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure China 21Vianet. 
> 
> Den här importmetoden är endast tillgänglig för [HSM:er som stöds.](#supported-hsms) 

Mer information och en självstudiekurs som hjälper dig att komma igång med Key Vault (inklusive hur du skapar ett nyckelvalv för HSM-skyddade nycklar) finns i [Vad är Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Översikt

Här är en översikt över processen. Specifika steg att slutföra beskrivs senare i artikeln.

* I Key Vault genererar du en nyckel (kallas nyckelutbytesnyckel(KEK)).  KEK måste vara en RSA-HSM-nyckel som bara har `import` nyckelåtgärden. Endast Key Vault Premium SKU stöder RSA-HSM-nycklar.
* Ladda ned den offentliga KEK-nyckeln som en .pem-fil.
* Överför den offentliga KEK-nyckeln till en offlinedator som är ansluten till en lokal HSM.
* På offlinedatorn använder du BYOK-verktyget som tillhandahålls av din HSM-leverantör för att skapa en BYOK-fil. 
* Målnyckeln krypteras med en KEK, som förblir krypterad tills den överförs till den Key Vault HSM. Endast den krypterade versionen av din nyckel lämnar den lokala HSM:en.
* En KEK som genereras i en Key Vault HSM kan inte exporteras. HSM:er tillämpar regeln att det inte finns någon klar version av en KEK utanför en Key Vault HSM.
* Kek måste finnas i samma nyckelvalv där målnyckeln ska importeras.
* När BYOK-filen laddas upp till Key Vault använder en Key Vault HSM den privata KEK-nyckeln för att dekryptera målnyckelmaterialet och importera det som en HSM-nyckel. Den här åtgärden sker helt i en Key Vault HSM. Målnyckeln finns alltid kvar i HSM-skyddsgränsen.

## <a name="prerequisites"></a>Förutsättningar

I följande tabell visas kraven för att använda BYOK i Azure Key Vault:

| Krav | Mer information |
| --- | --- |
| En Azure-prenumeration |Om du vill skapa ett nyckelvalv i Azure Key Vault behöver du en Azure-prenumeration. [Registrera dig för en kostnadsfri utvärderingsversion.](https://azure.microsoft.com/pricing/free-trial/) |
| En Key Vault Premium-SKU för att importera HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner i Azure Key Vault finns i [Key Vault priser.](https://azure.microsoft.com/pricing/details/key-vault/) |
| En HSM från HSM-listan som stöds och ett BYOK-verktyg och instruktioner från din HSM-leverantör | Du måste ha behörighet för en HSM och grundläggande kunskaper om hur du använder din HSM. Se [HSM:er som stöds.](#supported-hsms) |
| Azure CLI version 2.1.0 eller senare | Se [Installera Azure CLI.](/cli/azure/install-azure-cli)|

## <a name="supported-hsms"></a>HSM:er som stöds

|Leverantörsnamn|Leverantörstyp|HSM-modeller som stöds|Mer information|
|---|---|---|---|
|nCipher|Tillverkare<br/>HSM som en tjänst|<ul><li>nShield-familjen med HSM:er</li><li>nShield som en tjänst</ul>|[nCipher new BYOK tool and documentation (Chiffera nytt BYOK-verktyg och dokumentation)](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Tillverkare|<ul><li>Firmware HSM 7-familjen med version 7.3 eller senare av inbyggd programvara</li></ul>| [Tool and documentation (Verktyg och dokumentation för Tool byok) för Tool byok](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Exportera SDKMS-nycklar till molnleverantörer för BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Tillverkare|Alla LiquidSecurity HSM:er med<ul><li>Version 2.0.4 eller senare av inbyggd programvara</li><li>Version 3.2 eller senare av inbyggd programvara</li></ul>|[Bral BYOK-verktyg och dokumentation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Kryptografisk|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på kryptografiwebbplatsen](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK-verktyg och dokumentation](https://www.cryptomathic.com/azurebyok)|
|Sec secsys SA|Tillverkare<br/>HSM som en tjänst|Primus HSM-familj, Sec secesys clouds HSM|[Primus BYOK-verktyg och dokumentation](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Mer [information finns på StorMagic-webbplatsen](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS och Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Tillverkare|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Tillverkare<br/>HSM som en tjänst|u.trust Anchor, CryptoServer|[ByOK-verktyg och integrationsguide för Utimaco](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Nyckeltyper som stöds

|Nyckelnamn|Nyckeltyp|Nyckelstorlek/kurva|Ursprung|Description|
|---|---|---|---|---|
|Nyckelutbytesnyckel (KEK)|RSA| 2 048-bitars<br />3 072-bitars<br />4 096 bitar|Azure Key Vault HSM|Ett HSM-backat RSA-nyckelpar som genereras i Azure Key Vault|
|Målnyckel|
||RSA|2 048-bitars<br />3 072-bitars<br />4 096 bitar|Leverantörs-HSM|Nyckeln som ska överföras till Azure Key Vault HSM|
||EC|P-256<br />P-384<br />P-521|Leverantörs-HSM|Nyckeln som ska överföras till Azure Key Vault HSM|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generera och överföra din nyckel till Key Vault HSM

Generera och överföra nyckeln till en Key Vault HSM:

* [Steg 1: Generera en kek](#step-1-generate-a-kek)
* [Steg 2: Ladda ned den offentliga kek-nyckeln](#step-2-download-the-kek-public-key)
* [Steg 3: Generera och förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: Överför nyckeln till Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Steg 1: Generera en kek

En KEK är en RSA-nyckel som genereras i en Key Vault HSM. KEK används för att kryptera den nyckel som du vill importera *(målnyckeln).*

Kek måste vara:
- En RSA-HSM-nyckel (2 048 bitar, 3 072 bitar eller 4 096 bitar)
- Genereras i samma nyckelvalv där du tänker importera målnyckeln
- Skapad med tillåtna nyckelåtgärder inställda på `import`

> [!NOTE]
> Kek måste ha "import" som den enda tillåtna nyckelåtgärden. "import" utesluter varandra med alla andra viktiga åtgärder.

Använd kommandot [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) för att skapa en KEK som har nyckelåtgärder inställt på `import` . Registrera nyckelidentifieraren ( `kid` ) som returneras från följande kommando. (Du kommer att använda `kid` värdet i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Steg 2: Ladda ned den offentliga kek-nyckeln

Använd [az keyvault key download för](/cli/azure/keyvault/key#az_keyvault_key_download) att ladda ned den offentliga KEK-nyckeln till en .pem-fil. Målnyckeln som du importerar krypteras med hjälp av den offentliga kek-nyckeln.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Överför KEKforBYOK.publickey.pem-filen till offlinedatorn. Du behöver den här filen i nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: Generera och förbered din nyckel för överföring

Se HSM-leverantörens dokumentation för att ladda ned och installera BYOK-verktyget. Följ anvisningarna från din HSM-leverantör för att generera en målnyckel och skapa sedan ett nyckelöverföringspaket (en BYOK-fil). BYOK-verktyget använder från steg `kid` [1](#step-1-generate-a-kek) och KEKforBYOK.publickey.pem-filen som du laddade ned i steg [2](#step-2-download-the-kek-public-key) för att generera en krypterad målnyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna datorn.

> [!NOTE] 
> Import av RSA 1 024-bitarsnycklar stöds inte. Import av Elliptic Curve-nyckel med kurva P-256K stöds inte.
> 
> **Känt problem:** Import av en RSA 4K-målnyckel från Magazine HSM:er stöds endast med inbyggd programvara 7.4.0 eller nyare.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Steg 4: Överför din nyckel till Azure Key Vault

Slutför nyckelimporten genom att överföra nyckelöverföringspaketet (en BYOK-fil) från den frånkopplade datorn till den Internetanslutna datorn. Använd kommandot [az keyvault key import för](/cli/azure/keyvault/key#az_keyvault_key_import) att ladda upp BYOK-filen till Key Vault HSM.

Använd följande kommando för att importera en RSA-nyckel. Parametern --kty är valfri och standardvärdet är RSA-HSM.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om du vill importera en EC-nyckel måste du ange nyckeltyp och kurvanamn.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om uppladdningen lyckas visar Azure CLI egenskaperna för den importerade nyckeln.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i ditt nyckelvalv. Mer information finns i [pris- och funktionsjämförelse.](https://azure.microsoft.com/pricing/details/key-vault/)
