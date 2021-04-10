---
title: Ta med din egen nyckel specifikation – Azure Key Vault | Microsoft Docs
description: I det här dokumentet beskrivs ta med din egen nyckel specifikation.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581031"
---
# <a name="bring-your-own-key-specification"></a>Specifikation för Bring your own key

Det här dokumentet beskriver specifikationer för att importera HSM-skyddade nycklar från kunders lokala HSM: er till Key Vault.

## <a name="scenario"></a>Scenario

En Key Vault kund vill överföra en nyckel på ett säkert sätt från sin lokala HSM utanför Azure till HSM-Azure Key Vault. Processen för att importera en nyckel som genereras utanför Key Vault kallas vanligt vis Bring Your Own Key (BYOK).

Följande är kraven:
* Nyckeln som ska överföras finns inte utanför en HSM i oformaterad text form.
* Utanför en HSM skyddas den nyckel som ska överföras alltid av en nyckel som lagras i Azure Key Vault HSM

## <a name="terminology"></a>Terminologi

|Nyckel namn|Nyckel typ|Ursprung|Beskrivning|
|---|---|---|---|
|Nyckel utbytes nyckel (KEK)|RSA|Azure Key Vault HSM|Ett HSM-baserat RSA-nyckelpar som genererats i Azure Key Vault
Rad brytnings nyckel|AES|Vendor HSM|En [beständig] AES-nyckel genererad av HSM on-lokal
Mål nyckel|RSA, EG, AES (endast hanterad HSM)|Vendor HSM|Nyckeln som ska överföras till Azure Key Vault HSM

**Nyckel utbytes nyckel**: en HSM-backad nyckel som kunden genererar i nyckel valvet där BYOK-nyckeln kommer att importeras. Den här KEK måste ha följande egenskaper:

* Det är en RSA-HSM-nyckel (4096-bitars eller 3072-bitars eller 2048-bitars)
* Den har fasta key_ops (endast import) som gör att den bara kan användas under BYOK
* Måste finnas i samma valv där mål nyckeln kommer att importeras

## <a name="user-steps"></a>Användar steg

För att utföra en nyckel överföring utför en användare följande steg:

1. Generera KEK.
2. Hämta den offentliga nyckeln för KEK.
3. Använda HSM-leverantör tillhandahållet BYOK-verktyg – importera KEK till mål-HSM och exportera mål nyckeln som skyddas av KEK.
4. Importera den skyddade mål nyckeln till Azure Key Vault.

Kunder använder BYOK-verktyget och dokumentationen från HSM-leverantören för att slutföra steg 3. Den skapar en nyckel överförings-BLOB (en ". BYOK"-fil).


## <a name="hsm-constraints"></a>HSM-begränsningar

Befintliga HSM kan tillämpa begränsningar för nyckel som de hanterar, inklusive:
* HSM kan behöva konfigureras för att tillåta nyckel brytnings-baserad export
* Mål nyckeln kan behöva markeras CKA_EXTRACTABLE för HSM för att tillåta kontrollerad export
* I vissa fall kan KEK och Wrapping-nyckeln behöva markeras som CKA_TRUSTED. Detta gör att det kan användas för att figursätta nycklar i HSM.

Konfigurationen av source HSM är vanligt vis utanför omfånget för den här specifikationen. Microsoft förväntar sig att HSM-leverantören ska skapa dokumentation som medföljer deras BYOK-verktyg för att inkludera sådana konfigurations steg.

> [!NOTE]
> Steg 1, 2 och 4 som beskrivs nedan kan utföras med hjälp av andra gränssnitt som Azure PowerShell och Azure Portal. De kan också utföras program mässigt med motsvarande funktioner i Key Vault SDK.

### <a name="step-1-generate-kek"></a>Steg 1: generera KEK

Använd kommandot **AZ Key Vault Key Create** för att skapa KEK med de nyckel åtgärder som ska importeras. Anteckna nyckel identifieraren ' Kill ' som returneras från kommandot nedan.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Steg 2: hämta den offentliga nyckeln för KEK

Hämta den offentliga nyckel delen av KEK och lagra den i en PEM-fil.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Steg 3: generera nyckel överförings-BLOB med HSM-leverantör tillhandahållet BYOK-verktyg

Kunden använder HSM-leverantörens tillhandahållna BYOK-verktyg för att skapa en nyckel överförings-BLOB (lagrad som en ". BYOK"-fil). KEK offentlig nyckel (som en. PEM-fil) är en av indata till det här verktyget.

#### <a name="key-transfer-blob"></a>Nyckel överförings-BLOB
Lång sikt vill Microsoft använda PKCS # 11 CKM_RSA_AES_KEY_WRAP-mekanismen för att överföra mål nyckeln till Azure Key Vault eftersom den här mekanismen skapar en enda blob och, mer viktig, hanteras den mellanliggande AES-nyckeln av de två HSM: er och garanterar att den är tillfällig. Den här mekanismen är inte tillgänglig i vissa HSM: er, men kombinationen av skydd av mål nyckeln med CKM_AES_KEY_WRAP_PAD att använda en AES-nyckel och skydda AES-nyckeln med CKM_RSA_PKCS_OAEP ger en motsvarande blob.

Mål nyckeln i klartext beror på nyckel typen: 
* För en RSA-nyckel är den privata nyckeln ASN. 1 DER encoding [RFC3447] omsluten i PKCS # 8 [RFC5208] 
* För en EG-nyckel är den privata nyckeln ASN. 1 DER encoding [RFC5915] omsluten i PKCS # 8 [RFC5208]
* För en oktett Key är det rå byte för nyckeln

Byte för nyckeln oformaterad text omvandlas sedan med CKM_RSA_AES_KEY_WRAP mekanismen:
* En tillfällig AES-nyckel skapas och krypteras med hjälp av RSA-nyckeln för att använda RSA-OAEP med SHA1.
* Den kodade klartext-nyckeln krypteras med AES-nyckeln med hjälp av AES-nyckel omslutning med utfyllnad.
* Den krypterade AES-nyckeln och den krypterade klar text nyckeln sammanfogas för att skapa den slutliga chiffertexten-blobben.

Formatet på överförings-bloben använder JSON-webbkryptering (RFC7516) i första hand som ett fordon för att leverera nödvändiga metadata till tjänsten för korrekt dekryptering.

Om CKM_RSA_AES_KEY_WRAP_PAD används skulle JSON-serialiseringen av överförings-bloben vara:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* barn = nyckel identifierare för KEK. För Key Vault nycklar ser det ut så här: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = algoritm. 
* dir = Direct-läge, d.v.s. det hänvisade barnet används för att direkt skydda chiffertexten som är en korrekt representation av CKM_RSA_AES_KEY_WRAP
* Generator = ett informations fält som anger namn och version för BYOK-verktyget och källans HSM-tillverkare och modell. Den här informationen är avsedd att användas vid fel sökning och support.

JSON-blobben lagras i en fil med ett ". BYOK"-tillägg så att Azure PowerShell-/CLI-klienterna behandlar det korrekt när "Add-AzKeyVaultKey" (PSH) eller "AZ Key Vault Key import" (CLI)-kommandon används.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Steg 4: överför nyckel överförings-blob till importera HSM-Key

Kunden överför nyckel överförings-Bloben (". BYOK"-filen) till en online-arbetsstation och kör sedan ett **AZ import kommando för nyckel valv** för att importera denna blob som en ny HSM-backad nyckel till Key Vault. 

Använd följande kommando för att importera en RSA-nyckel:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Om du vill importera en EG-nyckel måste du ange nyckel typ och kurv namn.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


När ovanstående kommando körs, resulterar det i att en REST API-begäran skickas enligt följande:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Begär ande text när du importerar en RSA-nyckel:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Begär ande text vid import av en EG-nyckel:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

värdet "key_hsm" är hela innehållet i KeyTransferPackage-ContosoFirstHSMkey. BYOK kodat i base64-format.

## <a name="references"></a>Referenser
- [Utvecklarguide för Key Vault](../general/developers-guide.md)

## <a name="next-steps"></a>Nästa steg
* Steg-för-steg-BYOK-instruktioner: [Importera HSM-skyddade nycklar till Key Vault (BYOK)](hsm-protected-keys-byok.md)

