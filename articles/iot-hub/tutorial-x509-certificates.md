---
title: Självstudie – Förstå X.509-certifikat för offentliga nycklar för Azure IoT Hub| Microsoft Docs
description: Självstudie – Förstå X.509-certifikat för offentliga nycklar för Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378354"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Självstudie: Förstå X.509-certifikat för offentliga nycklar

X.509-certifikat är digitala dokument som representerar en användare, dator, tjänst eller enhet. De utfärdas av en certifikatutfärdare (CA), underordnad certifikatutfärdare eller registreringsutfärdare och innehåller den offentliga nyckeln för certifikatämnet. De innehåller inte ämnets privata nyckel som måste lagras på ett säkert sätt. Certifikat för offentliga nycklar dokumenteras av [RFC 5280](https://tools.ietf.org/html/rfc5280). De är digitalt signerade och innehåller i allmänhet följande information:

* Information om certifikatämnet
* Den offentliga nyckel som motsvarar ämnets privata nyckel
* Information om den utfärdande certifikatutfärdaren
* Krypterings- och/eller digital signeringsalgoritmer som stöds
* Information för att fastställa återkallnings- och giltighetsstatus för certifikatet

## <a name="certificate-fields"></a>Certifikatfält

Med tiden har det funnits tre certifikatversioner. Varje version lägger till fält i den tidigare versionen. Version 3 är aktuell och innehåller fälten version 1 och version 2 utöver fälten för version 3. Version 1 definierade följande fält:

* **Version:** Ett värde (1, 2 eller 3) som identifierar versionsnumret för certifikatet
* **Serienummer:** Ett unikt nummer för varje certifikat som utfärdats av en certifikatutfärdare
* **CA-signaturalgoritm:** Namnet på algoritmen som certifikatutfärdaren använder för att signera certifikatinnehållet
* **Utfärdarnamn:** Unikt namn (DN) för certifikatets utfärdande certifikatutfärdare
* **Giltighetsperiod:** Tidsperioden då certifikatet anses vara giltigt
* **Ämnesnamn:** Namnet på den entitet som representeras av certifikatet
* **Ämnesinformation om offentlig nyckel:** Offentlig nyckel som ägs av certifikatämnet

Version 2 lade till följande fält som innehåller information om certifikatutfärdaren. Dessa fält används dock sällan.

* **Unikt ID för utfärdare:** En unik identifierare för den utfärdande certifikatutfärdaren enligt definitionen av certifikatutfärdaren
* **Unikt ID för certifikatutfärdare:** En unik identifierare för certifikatämnet som definierats av den utfärdande certifikatutfärdaren

Version 3-certifikat har lagt till följande tillägg:

* **Nyckelidentifierare för** auktoritet: Detta kan vara ett av två värden:
  * Ämnet för certifikatutfärdaren och serienumret för det CA-certifikat som utfärdade det här certifikatet
  * Ett hash-värde för den offentliga nyckeln för certifikatutfärdaren som utfärdade det här certifikatet
* **Ämnesnyckelidentifierare:** Hash för det aktuella certifikatets offentliga nyckel
* **Nyckelanvändning** Definierar den tjänst som ett certifikat kan användas för. Detta kan vara ett eller flera av följande värden:
  * **Digital signatur**
  * **Oavvislighet**
  * **Nyckelchiffrering**
  * **Datachiffrering**
  * **Nyckelavtal**
  * **Key Cert Sign**
  * **CRL-tecken**
  * **Endast chiffer**
  * **Endast dechiffrering**
* **Användningsperiod för privat nyckel:** Giltighetsperiod för den privata nyckeldelen av ett nyckelpar
* **Certifikatprinciper:** Principer som används för att verifiera certifikatämnet
* **Principmappningar:** Mappar en princip i en organisation till en princip i en annan
* **Alternativt namn för ämne:** Lista över alternativa namn för ämnet
* **Alternativt namn för utfärdare:** Lista över alternativa namn för den utfärdande certifikatutfärdaren
* **Ämneskatalogattribut:** Attribut från en X.500- eller LDAP-katalog
* **Grundläggande begränsningar:** Tillåter att certifikatet anger om det har utfärdats till en certifikatutfärdare eller till en användare, dator, enhet eller tjänst. Det här tillägget innehåller också en begränsning för sökvägslängd som begränsar antalet underordnade certifikatutfärdare som kan finnas.
* **Namnbegränsningar:** Anger vilka namnrymder som tillåts i ett CERTIFIKATutfärdat certifikat
* **Principbegränsningar:** Kan användas för att förhindra principmappningar mellan CA:er
* **Utökad nyckelanvändning:** Anger hur ett certifikats offentliga nyckel kan användas utöver de syften som identifieras i **nyckelanvändningstillägget**
* **CRL-distributionsplatser:** Innehåller en eller flera webbadresser där listan över återkallade bascertifikat (CRL) har publicerats
* **Ordinska anyPolicy:** Förhindrar användning av OID för alla **utfärdandeprinciper** (2.5.29.32.0) i underordnade CA-certifikat
* **Nyaste CRL:** Innehåller en eller flera url:er där den utfärdande certifikatutfärdarens delta-CRL har publicerats
* **Åtkomst till utfärdarinformation:** Innehåller en eller flera url:er där det utfärdande CA-certifikatet publiceras
* **Åtkomst till ämnesinformation:** Innehåller information om hur du hämtar ytterligare information för ett certifikatämne

## <a name="certificate-formats"></a>Certifikatformat

Certifikat kan sparas i olika format. Azure IoT Hub autentisering använder vanligtvis PEM- och PFX-formaten.

### <a name="binary-certificate"></a>Binärt certifikat

Detta innehåller ett binärt raw-certifikat med DER ASN.1-kodning.

### <a name="ascii-pem-format"></a>ASCII PEM-format

Ett PEM-certifikat (.pem-tillägg) innehåller ett base64-kodat certifikat som börjar med -----BEGIN CERTIFICATE----- och slutar med -----END CERTIFICATE-----. PEM-formatet är mycket vanligt och krävs av IoT Hub när du laddar upp vissa certifikat.

### <a name="ascii-pem-key"></a>ASCII-nyckel (PEM)

Innehåller en base64-kodad DER-nyckel med eventuellt ytterligare metadata om algoritmen som används för lösenordsskydd.

### <a name="pkcs7-certificate"></a>PKCS #7 certifikat

Ett format som utformats för transport av signerade eller krypterade data. Den definieras av [RFC 2315](https://tools.ietf.org/html/rfc2315). Den kan innehålla hela certifikatkedjan.

### <a name="pkcs8-key"></a>PKCS#8-nyckel

Formatet för ett privat nyckelarkiv som definieras av [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>PKCS #12 nyckel och certifikat

Ett komplext format som kan lagra och skydda en nyckel och hela certifikatkedjan. Det används ofta med filnamnstillägget .pfx. PKCS #12 är synonymt med PFX-formatet.

## <a name="for-more-information"></a>Mer information

Mer information finns i följande avsnitt:

* [Handbok för X.509-certifikat jargong](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Konceptuell förståelse för X.509 CA-certifikat i IoT-branschen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Nästa steg

Om du vill generera testcertifikat som du kan använda för att autentisera enheter IoT Hub kan du se följande avsnitt:

* [Använda Microsoft-Supplied för att skapa testcertifikat](tutorial-x509-scripts.md)
* [Använda OpenSSL för att skapa testcertifikat](tutorial-x509-openssl.md)
* [Använda OpenSSL för att skapa Self-Signed testcertifikat](tutorial-x509-self-sign.md)

Om du har ett certifikatutfärdarcertifikat (CA) eller ett underordnat CA-certifikat och du vill ladda upp det till din IoT-hubb och bevisa att du äger det, se Visa innehav av ett [CA-certifikat](tutorial-x509-prove-possession.md).
