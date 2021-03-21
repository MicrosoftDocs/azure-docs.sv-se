---
title: Azure Active Directory certifikat utfärdare
description: Lista över betrodda certifikat som används i Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96859139"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Certifikat utfärdare som används av Azure Active Directory

> [!IMPORTANT]
> Informationen på den här sidan är endast relevant för entiteter som uttryckligen anger en lista över godkända certifikat utfärdare (ca). Den här metoden, som kallas certifikat låsning, bör undvikas om det inte finns några andra alternativ.

Alla entiteter som försöker komma åt Azure Active Directory (Azure AD) identitets tjänster via TLS/SSL-protokollen visas med certifikat från certifikat utfärdarna i listan nedan. Om entiteten har förtroende för dessa certifikat utfärdare, kan den använda certifikaten för att verifiera identitets tjänstens identitet och giltighet och upprätta säkra anslutningar.

Certifikat utfärdare kan klassificeras i rot certifikat utfärdare och mellanliggande certifikat utfärdare. Vanligt vis har rot certifikat utfärdare en eller flera associerade mellanliggande certifikat utfärdare. Den här artikeln innehåller rot certifikat utfärdare som används av Azure AD Identity Services och mellanliggande certifikat utfärdare som är kopplade till var och en av dessa rötter För varje certifikat utfärdare inkluderar vi URI: er (Uniform Resource Identifier) för att ladda ned den associerade auktoritets informations åtkomsten (AIA) och distributions plats filen för listan över återkallade certifikat (CDP). Vid behov tillhandahåller vi även en URI till slut punkten för Online Certificate Status Protocol (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Certifikat utfärdare som används i offentliga Azure-och Azure-myndigheter

Olika tjänster kan använda olika rot-eller mellanliggande certifikat utfärdare. Därför kan alla poster som anges nedan vara nödvändiga.

### <a name="digicert-global-root-g2"></a>DigiCert global root G2


| Rotcertifikatutfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - |- |-|-|-|-|
| DigiCert global root G2| 033af1e6a711a 9a0bb2864b11d09fae5| Den 1 augusti 2013 <br>15 januari 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Associerade mellanliggande certifikat utfärdare

| Utfärdande och mellanliggande certifikat utfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - | 
| Microsoft Azure TLS-utfärdande CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 juli 2020<br>27 juni 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS-utfärdande CA 02| 0c6ae97cced59983 8690a00a9ea53214| 29 juli 2020<br>27 juni 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS-utfärdande CA 05| 0d7bede97d8209967a 52631b8bdd18bd| 29 juli 2020<br>27 juni 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS-utfärdande CA 06| 02e79171fb8021e93fe 2d983834c50c0| 29 juli 2020<br>27 juni 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust-rot

| Rotcertifikatutfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - | 
| Baltimore CyberTrust-rot| 020000b9| 12 maj 2000<br>12 maj 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[CERTIFIKAT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Associerade mellanliggande certifikat utfärdare

| Utfärdande och mellanliggande certifikat utfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21 juli 2020<br>8 oktober 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[CERTIFIKAT](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21 juli 2020<br>20 maj, 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[CERTIFIKAT](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert global rot certifikat utfärdare

| Rotcertifikatutfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - | 
| DigiCert global rot certifikat utfärdare| 083be056904246 b1a1756ac95991c74a| 9 november 2006<br>9 november 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[CERTIFIKAT](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Associerade mellanliggande certifikat utfärdare

| Utfärdande och mellanliggande certifikat utfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8 mars 2013 den 8 mars 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[CERTIFIKAT](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |Den 22 september 2020<br>Den 22 september 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[CERTIFIKAT](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Certifikat utfärdare som används i Azure Kina 21Vianet-moln

### <a name="digicert-global-root-ca"></a>DigiCert global rot certifikat utfärdare


| Rotcertifikatutfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - |
| DigiCert global rot certifikat utfärdare| 083be056904246b 1a1756ac95991c74a| Nov. 9, 2006<br>Nov. 9, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[CERTIFIKAT](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Kopplad mellanliggande certifikat utfärdare

| Utfärdande och mellanliggande certifikat utfärdare| Serienummer| Utgivnings datum förfallo datum| SHA1-tumavtryck| URI: er |
| - | - | - | - | - | - |
| DigiCert Basic RSA CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 mars 2020<br>4 mars 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[CERTIFIKAT](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Microsoft 365 krypterings kedjor](/microsoft-365/compliance/encryption-office-365-certificate-chains)
