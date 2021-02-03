---
title: Protokoll för virtualiseringsbaserad säkerhet (VBS) för Azure-attestering
description: VBS-attesteringsprotokoll
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507921"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Trusted Platform Module (TPM) och enklaven för virtualiseringsbaserad säkerhet (VBS) 

Microsoft Azure attestering för att tillhandahålla en stark säkerhets garanti är beroende av att verifiera att en förtroende kedja upprätthålls från en förtroende rot (TPM) till lanseringen av hypervisor-program och säker kernel. För att uppnå denna Azure-attestering måste du intyga datorns start tillstånd innan vi kan upprätta förtroende i den säkra enklaven. Binärfilerna för operativ systemet, hypervisor och säker kernel måste signeras av rätt officiella Microsoft-myndigheter och konfigureras på ett säkert sätt. När vi har bundit förtroende mellan Trusted Platform Module (TPM) och hälsan för hypervisorn, vi kan lita på enklaven-IDKs för virtualiseringsbaserad säkerhet (VBS) som tillhandahålls i den uppmätta start loggen, där vi kan kontrol lera att ett nyckel par genererats av enklaven och mintgrön en attesterings rapport som binder förtroendet i nyckeln och innehåller andra anspråk, till exempel egenskaperna säkerhets nivå och start attestering. 

VBS enclaves kräver att en TPM tillhandahåller mått för att verifiera säkerhets stiftelsen. VBS-enclaves bestyrkas av TPM-slutpunkten med ett tillägg till objektet Request i protokollet. 

## <a name="protocol-messages"></a>Protokoll meddelanden

### <a name="init-message"></a>Init-meddelande

#### <a name="direction"></a>Riktning

Klient-> Azure-attestering

#### <a name="payload"></a>Innehållet

```
{ 
  "type": "aikcert" 
} 
```

"typ" (ASCII-sträng): representerar den typ av attestering som begärs. För närvarande stöds endast "aikcert".

### <a name="challenge-message"></a>Utmanings meddelande

#### <a name="direction"></a>Riktning

Azure-attestering – >-klient

#### <a name="payload"></a>Innehållet

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (oktetter)): slumpmässigt värde som utfärdats av tjänsten.

**service_context** (BASE64URL (oktetter)): ogenomskinlig kontext som skapats av tjänsten.


### <a name="request-message"></a>Begär ande meddelande

#### <a name="direction"></a>Riktning

Klient-> Azure-attestering 

#### <a name="payload"></a>Innehållet

```
{
  "request": "<JWS>"
}
```

**Request** (JWS): begäran består av en JWS-struktur (JSON Web Signature). JWS skyddade huvud-och JWS-nyttolasten visas nedan. Som i någon JWS-struktur består det slutliga värdet av:

BASE64URL (UTF8 (JWS skyddat huvud)) | | '.' ||

BASE64URL (JWS-nyttolast) | | '.' ||

BASE64URL (JWS Signature)

##### <a name="jws-protected-header"></a>JWS skyddat huvud

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS-nyttolast

JWS-nyttolasten kan vara av typen Basic eller VBS. Basic används när bestyrkande bevis inte innehåller VBS-data. 

Exempel på TPM-exempel: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enklaven-exempel: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): identifierare för förlitande part. Används av tjänsten vid beräkning av dator-ID-anspråk

**rp_data** (BASE64URL (oktetter)): täckande data som skickas av den förlitande parten. Detta används vanligt vis av den förlitande parten som ett nonce för att garantera rapportens aktualitet

**Challenge** (BASE64URL (oktetter)): slumpmässigt värde utfärdat av tjänsten

**tpm_att_data**: TPM-relaterade attesterings data

- **srtm_boot_log (BASE64URL (oktetter))**: SRTM start-loggar som hämtats av funktionen Tbsi_Get_TCG_Log_Ex med log type = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (oktetter))**: SRTM återupptar loggen som den har hämtats av funktionen Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (oktetter))**: drtm start-loggar som hämtats av funktionen Tbsi_Get_TCG_Log_Ex med log type = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (oktetter))**: drtm återupptar loggen som den har hämtats av funktionen Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (oktetter))**: X. 509-certifikatet för AIK som returneras av Function NCryptGetProperty med property = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: den offentliga delen av AIK visas som ett JWK-objekt (JSON Web Key) (RFC 7517)

- **current_claim (BASE64URL (oktetter))**: attesterings anspråket för det aktuella PCR-läget som returneras av Function NCryptCreateClaim med dwClaimType = NCRYPT_CLAIM_PLATFORM och parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ange att inkludera alla PCRs. Anropet som skickas av tjänsten bör också användas för att beräkna detta anspråk

- **boot_claim (BASE64URL (oktetter))**: attesterings anspråket för PCR-tillstånd vid start som returneras av Function NCryptCreateClaim med dwClaimType = NCRYPT_CLAIM_PLATFORM och parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ange att inkludera alla PCRs

**vsm_report**   (BASE64URL (OKTETTer)): rapporten VBS enklaven attestering som returneras av Function EnclaveGetAttestationReport. EnclaveData-parametern måste vara SHA-512-hashvärdet för värdet för report_signed (inklusive inledande och avslutande klammerparenteser). Inmatade hash-funktioner är UTF8 (report_signed)

**attest_key**: den offentliga delen av enklaven-nyckeln som representeras som ett JWK-objekt (JSON Web Key) (RFC 7517)

**custom_claims**: matris med anpassade enklaven-anspråk som skickas till tjänsten som kan utvärderas av principen. Anspråket

- **namn (sträng)**: namnet på anspråket. Det här namnet läggs till i en URL som fastställs av attesterings tjänsten (för att undvika konflikter) och den sammanfogade strängen blir den typ av anspråk som kan användas i principen

- **värde (sträng)**: värde för anspråket

- **value_type (sträng)**: data typen för anspråkets värde

**service_context** (BASE64URL (oktetter)): ogenomskinlig kontext som skapats av tjänsten.

### <a name="report-message"></a>Rapport meddelande

#### <a name="direction"></a>Riktning

Azure-attestering – >-klient

#### <a name="payload"></a>Innehållet

```
{
  "report": "<JWT>"
}
```

**rapport** (JWT): rapport om attestering i JSON Web token (JWT) (RFC 7519).

## <a name="next-steps"></a>Nästa steg

- [Arbets flöde för Azure-attestering](workflow.md)
