---
title: Certifikat fel sökning med Azure Stack Edge Pro med GPU | Microsoft Docs
description: Beskriver fel sökning av certifikat fel med Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 67dd2b35229c15ae4df5ec8acb357aa35621d67c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436642"
---
# <a name="troubleshooting-certificate-errors"></a>Felsöka certifikat fel

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Artikeln innehåller fel sökning av vanliga certifikat fel när du installerar certifikat till din Azure Stack Edge Pro-enhet.

## <a name="common-certificate-errors"></a>Vanliga certifikat fel

Följande tabell visar vanliga certifikat fel och detaljerad information om dessa fel och möjliga lösningar:

> [!NOTE]
> Förekomster av &#8220;{0} , {1} ,..., {n} &#8221; anger positions parametrar. Positions parametrarna tar värden beroende på de certifikat som du använder.

| Felkod | Beskrivning |
|---|---|
| CertificateManagement_UntrustedCertificate | Certifikatet med ämnes namnet {0} har en certifikat kedja bruten. Ladda upp certifikatet för signerings kedjan innan du laddar upp det här certifikatet.|
| CertificateManagement_DeviceNotRegistered| Enheten är inte aktive rad. Du kan bara överföra ett support certifikat efter aktiveringen.|
| CertificateManagement_ExpiredCertificate | Certifikatet med typen {0} har upphört att gälla eller går ut snart. Kontrol lera förfallo datum för certifikatet och ta i ett nytt certifikat vid behov.|
| CertificateManagement_FormatMismatch | Certifikat formatet stöds inte. Kontrol lera certifikat formatet och hämta ett nytt certifikat vid behov.  Förväntad {0} , hittades {1} . |
| CertificateManagement_GenericError | Det gick inte att utföra certifikat hanterings åtgärden. Försök igen om några minuter. Kontakta Microsoft Support om problemet kvarstår. |
| CertificateManagement_HttpsBindingFailure | Certifikatet med ämnes namnet {0} kunde inte skapa en säker https-kanal. Kontrol lera certifikatet som du har laddat upp och ta ett nytt certifikat om det behövs. Det här felet uppstår med enhetens Node-certifikat.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Certifikatet med ämnes namnet {0} får inte ha certifikat signering för nyckel användning. Kontrol lera nyckel användningen för certifikatet och ta med ett nytt certifikat vid behov. Det här felet uppstår med signerings kedjans certifikat. |
| CertificateManagement_IncorrectKeyNumber | Certifikatet med ämnes namnet {0} har ett felaktigt nyckel nummer {1} . Kontrol lera nyckel numret för certifikatet och ta med ett nytt certifikat vid behov.|
| CertificateManagement_InvalidP7b | Den uppladdade certifikat filen är inte giltig.  Kontrol lera certifikat formatet och hämta ett nytt certifikat vid behov.|
| CertificateManagement_KeyAlgorithmNotRSA | Det här certifikatet använder inte RSA-nyckelalgoritm. Endast RSA-certifikat stöds. |
| CertificateManagement_KeySizeNotSufficient | Certifikatet med ämnes namnet {0} har otillräcklig nyckel storlek {1} . Den minsta nyckel storleken är 4096.|
| CertificateManagement_MissingClientOid | Certifikatet med ämnes namnet saknar {0} OID för klientautentisering. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Certifikatet med ämnes namnet saknar {0} digital signatur i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Certifikatet med ämnes namnet saknar {0} certifikat signering i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Certifikatet med ämnes namnet {0} har ingen nyckel kryptering i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat. |
| CertificateManagement_MissingServerOid | Certifikatet med ämnes namnet saknar {0} OID för serverautentisering. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_NameMismatch | Felaktig matchning av certifikat typ. Förväntat omfång: {0} , hittades {1} . Ladda upp lämpligt certifikat.|
| CertificateManagement_NoPrivateKeyPresent | {0}Det finns ingen privat nyckel för certifikatet med ämnes namnet. Ladda upp ett. PFX-certifikat med privat nyckel.|
| CertificateManagement_NoRSACryptoPrivateKey | Det går inte att komma åt den privata nyckeln för certifikat med mottagar namn {0} . Kontrol lera att du använder ett certifikat som stöds. Endast Microsoft RSA/SChannel Cryptographic Provider stöds. |
| CertificateManagement_NotSelfSignedCertificate | Certifikatet med ämnes namnet {0} är inte självsignerat. Rot certifikaten ska vara självsignerade |
| CertificateManagement_NotSupportedOnVirtualAppliance | Den här åtgärden stöds inte på den virtuella enheten. Det här felet indikerar att signering endast sker med Data Box Gateway som körs i moln installationen i taktiska. Felet uppstår när enheten hanteras via Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | Certifikatet med ämnes namnet {0} är självsignerat. Ladda upp ett certifikat som är korrekt signerat.|
| CertificateManagement_SignatureAlgorithmSha1 | Certifikatet med ämnes namnet {0} innehåller en signaturalgoritm som inte stöds. SHA1-RSA stöds inte. |
| CertificateManagement_SubjectNamesInvalid | Certifikatet med ämnes namnet {0} har inte rätt ämnes namn eller alternativa namn för {1} certifikat mottagare. Kontrol lera certifikatet som du har laddat upp och ta ett nytt certifikat om det behövs. Du bör också kontrol lera DNS-namnet för att matcha San-namnen.|
| CertificateManagement_UnreadableCertificate | {0}Det gick inte att läsa certifikatet med typen. Felet uppstår när certifikatet är oläsligt eller skadat. Hämta ett nytt certifikat.|
| CertificateSubjectNotFound | {0}Det gick inte att hitta certifikatet med ämnes namnet. Hämta ett nytt certifikat.|
| CertificateRotationGenericFailure | Det gick inte att rotera en eller flera certifikat. Försök igen om några minuter. Kontakta Microsoft Support om problemet kvarstår.|
| CertificateImportFailure | Certifikatet med tumavtrycket {0} importerades inte på noden {1} . Kontakta Microsoft Support om problemet kvarstår. |
| CertificateApplyFailure | Certifikatet med tumavtrycket {0} tillämpades inte på noden {1} . Kontakta Microsoft Support om problemet kvarstår.|
| NodeNotReachable | Det gick inte att verifiera certifikatet på {0} . Kontrol lera systemets maskin-och program varu hälsa.|


## <a name="next-steps"></a>Nästa steg

[Certifikat krav](azure-stack-edge-gpu-certificate-requirements.md)