---
title: Anspråks uppsättningar för Azure-attestering
description: Anspråks uppsättningarna för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704811"
---
# <a name="claim-sets"></a>Anspråksuppsättningar

Anspråk som genereras vid bestyrkande av enclaves med hjälp av Microsoft Azure attestering kan delas in i kategorierna nedan:

- **Inkommande anspråk**: anspråk som genererats av Microsoft Azure attestering efter parsning av bevis för attestering och kan användas av princip författare för att definiera auktoriseringsregler i en anpassad princip

- **Utgående anspråk**: anspråk som genereras av Azure-attestering och innehåller alla anspråk som ingår i attesterings-token

- **Egenskaps anspråk**: de anspråk som skapats som utdata av Azure-attestering. Den innehåller alla anspråk som representerar egenskaperna för attesterings-token, till exempel kodning av rapporten, rapportens giltighets tid och så vidare.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Vanliga inkommande anspråk för alla attesterings typer

Under anspråk genereras av Azure-attestering och kan användas av princip författare för att definiera auktoriseringsregler i en anpassad princip för alla attesterings typer.

- **x-MS-ver**: JWT-schema version (förväntas vara "1,0")
- **x-MS-attestering-typ**: sträng värde som representerar typ av attestering 
- **x-MS-policy-hash**: hash för utvärderings principen för Azure-attestering beräknad som BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (princip text))))
- **x-MS-policy-Signer**: JSON-objekt med en "JwK"-medlem som representerar den nyckel en kund använde för att signera sin princip, när kunden laddar upp en signerad princip

Under anspråk anses föråldrade men stöds fullt ut. Vi rekommenderar att du använder anspråks namnen som inte är föråldrade.

Inaktuellt anspråk | Rekommenderat anspråk 
--- | --- 
ver | x-MS-ver
tee | x-MS-attestering-typ
maa-policyHash | x-MS-policy-hash
policy_hash | x-MS-policy-hash
policy_signer | x-MS-policy-Signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Vanliga utgående anspråk för alla attesterings typer

Under anspråk inkluderas i attesterings-token för alla attesterings typer av tjänsten.

Källa: enligt definitionen i [IETF JWT](https://tools.ietf.org/html/rfc7519)

- **"JTI"-anspråk (JWT ID)**
- **"ISS"-anspråk (Issuer)**
- **"IAT" (utfärdat vid)-anspråk**
- **"EXP"-anspråk (förfallo tid)**
- **"NBF" (inte före)-anspråk**

Källa: enligt definitionen i [IETF äta](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce-anspråk" (nonce)**

Under anspråk ingår som standard i attesterings-token baserat på inkommande anspråk:

- **x-MS-ver**: JWT-schema version (förväntas vara "1,0")
- **x-MS-attestering-typ**: sträng värde som representerar typ av attestering 
- **x-MS-policy-hash**: sträng värde som innehåller SHA256-hash för princip texten beräknad av BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (princip text))))
- **x-MS-policy-Signer**: innehåller en JWK med den offentliga nyckeln eller certifikat kedjan som finns i den signerade princip rubriken. x-MS-policy-Signer läggs bara till om principen är signerad

## <a name="claims-specific-to-sgx-enclaves"></a>Anspråk som är speciella för SGX-enclaves

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Inkommande anspråk som är specifik för SGX-attestering

Under anspråk genereras av Azure-attestering och kan användas av princip författare för att definiera auktoriseringsregler i en anpassad princip för SGX-attestering.

- **x-MS-SGX-är-fel sökning**: ett booleskt värde som anger om enklaven har fel sökning aktiverat eller inte
- **x-MS-SGX-Product-ID**
- **x-MS-SGX-mrsigner**: hex-kodat värde för fältet "mrsigner" för offerten
- **x-MS-SGX-mrenclave**: hex-kodat värde för fältet "mrenclave" för offerten
- **x-MS-SGX-SVN**: säkerhets versions nummer som har kodats i offerten 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Utgående anspråk som är speciella för SGX-attestering

Under anspråk skapas och tas med i attesterings-token av tjänsten för SGX-attestering.

- **x-MS-SGX-är-fel sökning**: ett booleskt värde som anger om enklaven har fel sökning aktiverat eller inte
- **x-MS-SGX-Product-ID**
- **x-MS-SGX-mrsigner**: hex-kodat värde för fältet "mrsigner" för offerten
- **x-MS-SGX-mrenclave**: hex-kodat värde för fältet "mrenclave" för offerten
- **x-MS-SGX-SVN**: säkerhets versions nummer som har kodats i offerten 
- **x-MS-SGX-EHD**: enklaven hålls data formaterade som BASE64URL (enklaven hålls data)
- **x-MS-SGX-släktingar**: JSON-objekt som beskriver de säkerheter som används för att utföra attestering. Värdet för anspråket x-MS-SGX-marknadsföring är ett kapslat JSON-objekt med följande nyckel/värde-par:
    - **qeidcertshash**: SHA256 värde för QE identitet som utfärdar certifikat
    - **qeidcrlhash**: SHA256 värde för lista över återkallade certifikat för QE-identitet
    - **qeidhash**: SHA256 värde för QE Identity-material
    - **quotehash**: SHA256-värdet för den utvärderade offerten
    - **tcbinfocertshash**: SHA256-värdet för den TCB-information som utfärdas av certifikat
    - **tcbinfocrlhash**: SHA256 värde för listan över återkallade certifikat i TCB-informationen
    - **tcbinfohash**: JSON-objekt som beskriver de säkerheter som används för att utföra attestering

Under anspråk anses föråldrade men stöds fullt ut och kommer även fortsättnings vis att ingå i framtiden. Vi rekommenderar att du använder anspråks namnen som inte är föråldrade.

Inaktuellt anspråk | Rekommenderat anspråk
--- | --- 
$is – fel sökning | x-MS-SGX-är-fel sökning
$sgx-mrsigner | x-MS-SGX-mrsigner
$sgx-mrenclave | x-MS-SGX-mrenclave
$product-ID | x-MS-SGX-Product-ID
$svn | x-MS-SGX-SVN
$tee | x-MS-attestering-typ
maa-ehd | x-MS-SGX-EHD
AAS-EHD | x-MS-SGX-EHD
maa-attestationcollateral | x-MS-SGX-material

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Anspråk som är speciella för Trusted Platform Module (TPM)/VBS-attestering

### <a name="incoming-claims-for-tpm-attestation"></a>Inkommande anspråk för TPM-attestering

Anspråk som utfärdats av Azure-attestering för TPM-attestering. Anspråkets tillgänglighet är beroende av beviset som tillhandahålls för attestering.

- **aikValidated**: booleskt värde som innehåller information om certifikat för attestering av identitets nyckel (AIK) har verifierats eller inte
- **aikPubHash**: sträng som innehåller base64 (offentlig nyckel för SHA256 (AIK i der-format))
- **tpmVersion**: heltals värde som innehåller den Trusted Platform Module (TPM) huvud version
- **secureBootEnabled**: booleskt värde som anger om säker start har Aktiver ATS
- **iommuEnabled**: booleskt värde som anger om indata-utgående minnes hanterings enhet (IOMMU) är aktive rad
- **bootDebuggingDisabled**: booleskt värde som anger om start fel sökning har inaktiverats
- **notSafeMode**: booleskt värde som anger om Windows inte körs i fel säkert läge
- **notWinPE**: booleskt värde som anger om Windows inte körs i WinPE-läge
- **vbsEnabled**: booleskt värde som anger om vbs är aktiverat
- **vbsReportPresent**: booleskt värde som anger om vbs enklaven Report är tillgängligt

### <a name="incoming-claims-for-vbs-attestation"></a>Inkommande anspråk för VBS-attestering

Anspråk som utfärdats av Azure-attestering för VBS-attestering är utöver de anspråk som görs tillgängliga för TPM-attestering. Anspråkets tillgänglighet är beroende av beviset som tillhandahålls för attestering.

- **enclaveAuthorId**: sträng värde som innehåller det Base64Url-kodade värdet för enklaven författar-ID: författar-ID för den primära modulen för enklaven
- **enclaveImageId**: sträng värde som innehåller det Base64Url-kodade värdet för enklaven-avbildnings-ID: t för avbildnings-ID: t för den primära modulen för enklaven
- **enclaveOwnerId**: sträng värde som innehåller det Base64Url-kodade värdet för enklaven ägar-ID: ID för ägaren för enklaven
- **enclaveFamilyId**: sträng värde som innehåller det Base64Url-kodade värdet för enklaven Family ID. Familje identifieraren för den primära modulen för enklaven
- **enclaveSvn**: heltals värde som innehåller säkerhets versions numret för den primära modulen för enklaven
- **enclavePlatformSvn**: heltals värde som innehåller säkerhets versions numret för den plattform som är värd för enklaven
- **enclaveFlags**: enclaveFlags-anspråket är ett heltals värde som innehåller flaggor som beskriver körnings principen för enklaven

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Utgående anspråk som är speciella för TPM och VBS-attestering

- **CNF (bekräftelse)**: "CNF"-anspråket används för att identifiera den viktiga nyckeln. Bekräftelse anspråk enligt definitionen i RFC 7800 innehåller den offentliga delen av den bevisade enklaven-nyckeln som representeras som ett JWK-objekt (JSON Web Key) (RFC 7517)
- **rp_data (förlitande part data)**: förlitande part data, om sådana finns, som anges i begäran och som används av den förlitande parten som ett nonce för att säkerställa att rapporten uppdateras. rp_data läggs bara till om det finns rp_data

### <a name="property-claims"></a>Egenskaps anspråk

- **report_validity_in_minutes**: ett heltals anspråk som indikerar hur länge token är giltig.
  - **Standardvärde (tid)**: en dag på några minuter.
  - **Högsta värde (tid)**: ett år på några minuter.
- **omit_x5c**: ett booleskt anspråk som anger om Azure-attesteringen ska utelämna det certifikat som används för att tillhandahålla bevis på tjänstens äkthet. Om värdet är true kommer x5t att läggas till i attesterings-token. Om värdet är false (standard) kommer X5C att läggas till i attesterings-token.

## <a name="next-steps"></a>Nästa steg
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
