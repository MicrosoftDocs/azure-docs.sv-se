---
title: Självstudie – förstå 509 offentliga nyckel certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – förstå 509 offentliga nyckel certifikat för Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: cdc5b261abe91c31d31827aeab03c9e8838b2a91
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630817"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Självstudie: förstå X. 509 offentliga nyckel certifikat

X. 509-certifikat är digitala dokument som representerar en användare, dator, tjänst eller enhet. De utfärdas av en certifikat utfärdare (CA), en underordnad certifikat utfärdare eller en registrerings utfärdare och innehåller den offentliga nyckeln för certifikatets ämne. De innehåller inte ämnets privata nyckel som måste lagras på ett säkert sätt. Certifikat för offentliga nycklar dokumenteras av [RFC 5280](https://tools.ietf.org/html/rfc5280). De är digitalt signerade och innehåller i allmänhet följande information:

* Information om certifikatets ämne
* Den offentliga nyckeln som motsvarar ämnets privata nyckel
* Information om den utfärdande certifikat utfärdaren
* Algoritmerna för kryptering och/eller digital signering som stöds
* Information för att fastställa certifikatets återkallnings-och giltighets status

## <a name="certificate-fields"></a>Certifikat fält

Det finns tre certifikat versioner över tid. Varje version lägger till fält till en före. Version 3 är aktuell och innehåller version 1 och version 2 fält förutom version 3 fält. I version 1 definieras följande fält:

* **Version**: ett värde (1, 2 eller 3) som identifierar certifikatets versions nummer
* **Serie nummer**: ett unikt nummer för varje certifikat som utfärdats av en certifikat utfärdare
* **Algoritm för ca-signatur**: namnet på den algoritm som certifikat utfärdaren använder för att signera certifikat innehållet
* **Utfärdarens namn**: det unika namnet (DN) för certifikatets utfärdande certifikat utfärdare
* **Giltighets period**: den tids period då certifikatet anses vara giltigt
* **Ämnes namn**: namnet på entiteten som representeras av certifikatet
* **Information om offentlig nyckel för ämne**: offentlig nyckel som ägs av certifikat ämnet

Version 2 lade till följande fält som innehåller information om certifikat utfärdaren. Dessa fält är dock sällan använda.

* **Unikt ID för utfärdare**: en unik identifierare för den utfärdande certifikat utfärdaren som definieras av ca: n
* **Unikt ID** för certifikat mottagare: en unik identifierare för certifikatets ämne som definieras av den utfärdande certifikat utfärdaren

Version 3-certifikat har lagt till följande tillägg:

* **Nyckel identifierare för utfärdare**: det kan vara ett av två värden:
  * Ämnet för CA: n och serie numret för det CA-certifikat som utfärdade det här certifikatet
  * En hash av den offentliga nyckeln för den certifikat utfärdare som utfärdade certifikatet
* **Nyckel identifierare** för certifikat mottagare: hash för det aktuella certifikatets offentliga nyckel
* **Nyckel användning** Definierar den tjänst som ett certifikat kan användas för. Detta kan vara ett eller flera av följande värden:
  * **Digital signatur**
  * **Oavvislig het**
  * **Nyckelchiffrering**
  * **Data kryptering**
  * **Nyckel avtal**
  * **Nyckel certifikats tecken**
  * **CRL-tecken**
  * **Endast chiffrering**
  * **Endast dechiffrerad**
* **Användnings period för privat nyckel**: giltighets period för den privata nyckel delen av ett nyckel par
* **Certifikat principer**: principer som används för att validera certifikatets ämne
* **Princip mappningar**: mappar en princip i en organisation till en princip i en annan
* **Alternativt namn för certifikat mottagare**: lista över alternativa namn för ämnet
* **Alternativt namn på utfärdare**: lista över alternativa namn för den utfärdande certifikat utfärdaren
* **Ämnes-dir-attribut**: attribut från en X. 500-eller LDAP-katalog
* **Grundläggande begränsningar**: tillåter att certifikatet anger om det har utfärdats till en certifikat utfärdare eller till en användare, dator, enhet eller tjänst. Tillägget innehåller också en begränsning för Sök vägs längd som begränsar antalet underordnade certifikat utfärdare som kan finnas.
* **Namn begränsningar**: anger vilka namn områden som tillåts i ett ca-utfärdat certifikat
* **Princip begränsningar**: kan användas för att förhindra princip mappningar mellan certifikat utfärdare
* **Utökad nyckel användning**: anger hur ett certifikats offentliga nyckel kan användas utöver de områden som identifieras i tillägget för **nyckel användning**
* **CRL-distributions platser**: innehåller en eller flera URL: er där den grundläggande listan över återkallade certifikat (CRL) publiceras
* **Hindra anyPolicy**: förhindrar att **alla utgivnings principer** OID (2.5.29.32.0) används i underordnade CA-certifikat
* Den **färskaste listan över återkallade certifikat**: innehåller en eller flera URL: er där den utfärdande certifikat utfärdaren för utfärdande certifikat
* **Åtkomst till auktoritets information**: innehåller en eller flera URL: er där certifikat utfärdarens certifikat publiceras
* **Åtkomst till ämnes information**: innehåller information om hur du hämtar ytterligare information om ett certifikat ämne

## <a name="certificate-formats"></a>Certifikat format

Certifikat kan sparas i olika format. Azure IoT Hub-autentisering använder vanligt vis formatet PEM och PFX.

### <a name="binary-certificate"></a>Binärt certifikat

Innehåller ett binärt oformaterat certifikat med hjälp av DER ASN. 1-kodning.

### <a name="ascii-pem-format"></a>ASCII PEM-format

Ett PEM-certifikat (. pem-tillägg) innehåller ett base64-kodat certifikat som börjar med-----BEGIN CERTIFICATe-----och slutar med-----END CERTIFICATe-----. PEM-formatet är mycket vanligt och krävs av IoT Hub vid överföring av vissa certifikat.

### <a name="ascii-pem-key"></a>ASCII-nyckel (PEM)

Innehåller en Base64-kodad DER-nyckel med eventuellt ytterligare metadata om den algoritm som används för lösen ords skydd.

### <a name="pkcs7-certificate"></a>PKCS # 7-certifikat

Ett format som har utformats för transport av signerade eller krypterade data. Den definieras av [RFC 2315](https://tools.ietf.org/html/rfc2315). Den kan innehålla hela certifikat kedjan.

### <a name="pkcs8-key"></a>PKCS # 8-nyckel

Formatet för ett privat nyckel arkiv som definieras av [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>PKCS # 12-nyckel och certifikat

Ett komplext format som kan lagra och skydda en nyckel och hela certifikat kedjan. Den används ofta med fil namns tillägget. pfx. PKCS # 12 är synonymt med PFX-formatet.

## <a name="next-steps"></a>Nästa steg

Om du vill generera test certifikat som du kan använda för att autentisera enheter till din IoT Hub, se följande avsnitt:

* [Använda Microsoft-Supplied skript för att skapa test certifikat](tutorial-x509-scripts.md)
* [Använda OpenSSL för att skapa test certifikat](tutorial-x509-openssl.md)
* [Använda OpenSSL för att skapa Self-Signed test certifikat](tutorial-x509-self-sign.md)

Om du har ett certifikat för certifikat utfärdare (CA) eller ett underordnat certifikat för certifikat utfärdare och du vill överföra det till din IoT-hubb och se till att du äger det kan du läsa mer i [bevisa innehav av ett CA-certifikat](tutorial-x509-prove-possession.md).
