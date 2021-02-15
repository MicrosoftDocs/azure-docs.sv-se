---
title: Ändringar i Azure TLS-certifikat
description: Ändringar i Azure TLS-certifikat
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 9e4edbcfd9e4180e727cd885902d9f0150a967ca
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389407"
---
# <a name="azure-tls-certificate-changes"></a>Ändringar i Azure TLS-certifikat  

Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Den här ändringen görs eftersom de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarens forum bas linje och kommer att återkallas den 15 februari 2021.

## <a name="when-will-this-change-happen"></a>När sker den här ändringen?

Befintliga Azure-slutpunkter har övergått till ett stegvist sätt sedan den 13 augusti 2020. Alla nyligen skapade Azure TLS/SSL-slutpunkter innehåller uppdaterade certifikat som går samman till de nya rot certifikat utfärdarna.

Alla Azure-tjänster påverkas av den här ändringen. Här följer ytterligare information om vissa tjänster:

- [Azure Active Directory](../../active-directory/index.yml) (Azure AD)-tjänster började den här över gången den 7 juli 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) och [DPS](../../iot-dps/index.yml) finns kvar på Baltimore CyberTrust-rot certifikat utfärdare men deras mellanliggande certifikat utfärdare kommer att ändras. [Klicka här om du vill ha mer information](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Azure Storage](../../storage/index.yml) kommer att finnas kvar på Baltimore CyberTrust rot certifikat utfärdare, men deras mellanliggande certifikat utfärdare ändras. [Klicka här om du vill ha mer information](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Azure cache för Redis](../../azure-cache-for-redis/index.yml) är kvar på Baltimore CyberTrust rot certifikat utfärdare, men deras mellanliggande certifikat utfärdare kommer att ändras. [Klicka här om du vill ha mer information](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service finns kvar på Baltimore CyberTrust rot certifikat utfärdare men deras mellanliggande certifikat utfärdare kommer att ändras. [Klicka här om du vill ha mer information](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Kunder kan behöva uppdatera sina program efter den här ändringen för att förhindra anslutnings problem vid försök att ansluta till Azure-tjänster.

## <a name="what-is-changing"></a>Vad ändras?

Idag går de flesta TLS-certifikaten som används av Azure-tjänsterna till följande rot certifikat utfärdare:

| Eget namn på CA: n | Tumavtryck (SHA1) |
|--|--|
| [Baltimore CyberTrust-rot](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

TLS-certifikat som används av Azure-tjänster kommer att kedjas upp till någon av följande rot certifikat utfärdare:

| Eget namn på CA: n | Tumavtryck (SHA1) |
|--|--|
| [DigiCert global root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert global rot certifikat utfärdare](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust-rot](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D – förtroende rot klass 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC rot certifikat utfärdare 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>När kan jag dra tillbaka det gamla mellanliggande tumavtrycket?

De aktuella CA-certifikaten kommer *inte* att återkallas förrän 15 februari 2021. Efter det datumet kan du ta bort den gamla tumavtrycken från din kod.

Om det här datumet ändras får du ett meddelande om det nya återkallnings datumet.

## <a name="will-this-change-affect-me"></a>Kommer den här ändringen att påverka mig? 

Vi räknar med att **de flesta Azure-kunder inte** påverkas.  Ditt program kan dock påverkas om det uttryckligen anger en lista med acceptabla certifikat utfärdare. Den här metoden kallas certifikat fäst.

Här följer några sätt att identifiera om ditt program påverkas:

- Sök i käll koden efter tumavtryck, eget namn och andra certifikat egenskaper för någon av Microsofts IT TLS-certifikatutfärdare som du hittar [här](https://www.microsoft.com/pki/mscorp/cps/default.htm). Om det finns en matchning kommer programmet att påverkas. Lös problemet genom att uppdatera käll koden innehåller de nya certifikat utfärdarna. Vi rekommenderar att du kontrollerar att ca: er kan läggas till eller redige ras på kort varsel. Bransch regler kräver att CA-certifikat ersätts inom sju dagar, och därför måste kunderna fästa på ett snabbt sätt.

- Om du har ett program som integreras med Azure-API: er eller andra Azure-tjänster och du är osäker på om det använder certifikat fäst, kontrollerar du med program leverantören.

- Olika operativ system och språk körningar som kommunicerar med Azure-tjänster kan kräva ytterligare åtgärder för att korrekt bygga certifikat kedjan med följande nya rötter:
    - **Linux**: många distributioner kräver att du lägger till ca: er i/etc/ssl/certs. Mer information finns i distributionens dokumentation.
    - **Java**: kontrol lera att Java-nyckelfilen innehåller de certifikat utfärdare som anges ovan.
    - **Fönster som körs i frånkopplade miljöer**: system som körs i frånkopplade miljöer måste ha de nya rötter som lagts till i arkivet Betrodda rot certifikat utfärdare och de mellanliggande certifikat utfärdare som lagts till i arkivet mellanliggande certifikat utfärdare.
    - **Android**: Mer information finns i dokumentationen för enheten och Android-versionen.
    - **Andra maskin varu enheter, särskilt IoT**: kontakta enhets tillverkaren.

- Om du har en miljö där brand Väggs regler har ställts in för att tillåta utgående anrop till enbart en speciell lista över återkallade certifikat (CRL) och/eller Online Certificate Status Protocol (OCSP) verifierings platser. Du måste tillåta följande CRL-och OCSP-URL: er:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d-förtroende&#46;net
    - http://root-c3-ca2-2009&#46; OCSP&#46;d-förtroende&#46;net
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Nästa steg

Om du har fler frågor kan du kontakta oss via [supporten](https://azure.microsoft.com/support/options/).
