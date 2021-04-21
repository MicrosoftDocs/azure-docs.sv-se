---
title: Generera och överföra HSM-skyddade nycklar för Azure Key Vault Managed HSM – Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Managed HSM. Kallas även BYOK (Bring Your Own Key).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: cc9037db3289d7fb3287a8994a8ff6a68fc0583a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790589"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importera HSM-skyddade nycklar till Managed HSM (BYOK)

 Azure Key Vault Managed HSM har stöd för import av nycklar som genereras i din lokala maskinvarusäkerhetsmodul (HSM); nycklarna lämnar aldrig HSM-skyddsgränsen. Det här scenariot kallas ofta *BYOK (Bring Your Own* Key). Hanterad HSM använder Managed LiquidSecurity HSM-kort (FIPS 140-2 Level 3-verifierade) för att skydda dina nycklar.

Använd informationen i den här artikeln när du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Managed HSM.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure China 21Vianet. Den här importmetoden är endast tillgänglig för [HSM:er som stöds.](#supported-hsms) 

Mer information och en självstudiekurs som hjälper dig att komma igång med Managed HSM finns i [Vad är Managed HSM?](overview.md).

## <a name="overview"></a>Översikt

Här är en översikt över processen. Specifika steg som ska slutföras beskrivs senare i artikeln.

* Generera en nyckel (kallas nyckelutbytesnyckel)  i Managed HSM. KEK måste vara en RSA-HSM-nyckel som bara har `import` nyckelåtgärden. 
* Ladda ned den offentliga KEK-nyckeln som en .pem-fil.
* Överför den offentliga KEK-nyckeln till en offlinedator som är ansluten till en lokal HSM.
* På offlinedatorn använder du BYOK-verktyget från HSM-leverantören för att skapa en BYOK-fil. 
* Målnyckeln krypteras med en KEK, som förblir krypterad tills den överförs till den hanterade HSM:en. Endast den krypterade versionen av din nyckel lämnar den lokala HSM:en.
* En KEK som genereras i en hanterad HSM kan inte exporteras. HSM:er framtvingar regeln att det inte finns någon klar version av en KEK utanför en hanterad HSM.
* KEK måste finnas i samma hanterade HSM där målnyckeln ska importeras.
* När BYOK-filen överförs till Managed HSM använder en hanterad HSM den privata KEK-nyckeln för att dekryptera målnyckelmaterialet och importera det som en HSM-nyckel. Den här åtgärden sker helt och hållet inuti HSM. Målnyckeln finns alltid kvar i HSM-skyddsgränsen.


## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-kommandona i den här artikeln måste du ha följande:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI version 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* En hanterad HSM som [stöds HSM-listan](#supported-hsms) i din prenumeration. Se [Snabbstart: Etablera och aktivera en hanterad HSM med Azure CLI för](quick-create-cli.md) att etablera och aktivera en hanterad HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggningsalternativen via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>HSM:er som stöds

|Leverantörsnamn|Leverantörstyp|HSM-modeller som stöds|Mer information|
|---|---|---|---|
|nCipher|Tillverkare<br/>HSM som en tjänst|<ul><li>nShield-familjen med HSM:er</li><li>nShield som en tjänst</ul>|[nCipher new BYOK tool and documentation (Chiffera nytt BYOK-verktyg och dokumentation)](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Tillverkare|<ul><li>Firmware HSM 7-familjen med version 7.3 eller senare av inbyggd programvara</li></ul>| [Verktyget Och dokumentationen för Tool byok](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Tillverkare<br/>HSM som en tjänst|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Exportera SDKMS-nycklar till molnleverantörer för BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Tillverkare|Alla LiquidSecurity HSM:er med<ul><li>Version 2.0.4 eller senare av inbyggd programvara</li><li>Version 3.2 eller senare av inbyggd programvara</li></ul>|[Bral BYOK-verktyg och dokumentation](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Kryptografisk|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Mer [information finns på kryptografiwebbplatsen](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK-verktyg och dokumentation](https://www.cryptomathic.com/azurebyok)|
|Sec secsys SA|Tillverkare, HSM som en tjänst|Primus HSM-familj, Sec secesys clouds HSM|[Primus BYOK-verktyg och dokumentation](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Flera HSM-varumärken och modeller, inklusive<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Mer [information finns på StorMagic-webbplatsen](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS och Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Tillverkare|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Tillverkare<br/>HSM som en tjänst|u.trust Anchor, CryptoServer|[Utimaco BYOK-verktyg och integrationsguide](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Nyckeltyper som stöds

|Nyckelnamn|Nyckeltyp|Nyckelstorlek/kurva|Ursprung|Description|
|---|---|---|---|---|
|Nyckelutbytesnyckel (KEK)|RSA| 2 048 bitar<br />3 072-bitars<br />4 096-bitars|Managed HSM|Ett HSM-backat RSA-nyckelpar som genererats i Managed HSM|
|Målnyckel|
||RSA|2 048 bitar<br />3 072-bitars<br />4 096-bitars|Leverantörs-HSM|Nyckeln som ska överföras till den hanterade HSM:en|
||EC|P-256<br />P-384<br />P-521|Leverantörs-HSM|Nyckeln som ska överföras till den hanterade HSM:en|
||Symmetrisk nyckel (okt-HSM)|128-bitars<br />192-bitars<br />256-bitars|Leverantörs-HSM|Nyckeln som ska överföras till den hanterade HSM:en|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generera och överföra din nyckel till den hanterade HSM:en

Generera och överföra nyckeln till en hanterad HSM:

* [Steg 1: Generera en kek](#step-1-generate-a-kek)
* [Steg 2: Ladda ned den offentliga kek-nyckeln](#step-2-download-the-kek-public-key)
* [Steg 3: Generera och förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: Överför nyckeln till Managed HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Steg 1: Generera en kek

En KEK är en RSA-nyckel som genereras i en hanterad HSM. KEK används för att kryptera den nyckel som du vill importera *(målnyckeln).*

Kek måste vara:
- En RSA-HSM-nyckel (2 048 bitar, 3 072 bitar eller 4 096 bitar)
- Genererad i samma hanterade HSM där du tänker importera målnyckeln
- Skapad med tillåtna nyckelåtgärder inställda på `import`

> [!NOTE]
> KEK måste ha "import" som den enda tillåtna nyckelåtgärden. "import" utesluter varandra med alla andra viktiga åtgärder.

Använd kommandot [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) för att skapa en KEK som har nyckelåtgärder inställda på `import` . Registrera nyckelidentifieraren ( `kid` ) som returneras från följande kommando. (Du kommer att använda `kid` värdet i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Steg 2: Ladda ned den offentliga kek-nyckeln

Använd [az keyvault key download för](/cli/azure/keyvault/key#az_keyvault_key_download) att ladda ned den offentliga KEK-nyckeln till en .pem-fil. Målnyckeln som du importerar krypteras med hjälp av den offentliga kek-nyckeln.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Överför KEKforBYOK.publickey.pem-filen till offlinedatorn. Du behöver den här filen i nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: Generera och förbered din nyckel för överföring

Se HSM-leverantörens dokumentation för att ladda ned och installera BYOK-verktyget. Följ anvisningarna från din HSM-leverantör för att generera en målnyckel och skapa sedan ett nyckelöverföringspaket (en BYOK-fil). BYOK-verktyget använder från steg `kid` [1](#step-1-generate-a-kek) och KEKforBYOK.publickey.pem-filen som du laddade ned i steg [2](#step-2-download-the-kek-public-key) för att generera en krypterad målnyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna datorn.

> [!NOTE] 
> Import av RSA 1 024-bitarsnycklar stöds inte. För närvarande stöds inte import av en EC-nyckel (Elliptic Curve).
>
> **Känt problem:** Import av en RSA 4K-målnyckel från Magazine HSM:er stöds endast med inbyggd programvara 7.4.0 eller nyare.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Steg 4: Överför din nyckel till Managed HSM

Slutför nyckelimporten genom att överföra nyckelöverföringspaketet (en BYOK-fil) från den frånkopplade datorn till den Internetanslutna datorn. Använd kommandot [az keyvault key import för](/cli/azure/keyvault/key#az_keyvault_key_import) att ladda upp BYOK-filen till managed HSM.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om uppladdningen lyckas visar Azure CLI egenskaperna för den importerade nyckeln.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i din hanterade HSM. Mer information finns i [pris- och funktionsjämförelse.](https://azure.microsoft.com/pricing/details/key-vault/)
