---
title: Självstudie – Förstå kryptografi- och X.509-certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – Förstå kryptografi och X.509 PKI för Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378388"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Självstudie: Förstå kryptografi och infrastruktur för offentliga nycklar i X.509

Du kan använda X.509-certifikat för att autentisera enheter till en Azure IoT Hub. Ett certifikat är ett digitalt dokument som innehåller enhetens offentliga nyckel och kan användas för att verifiera att enheten är den som den utger sig för att vara. X.509-certifikat och listor över återkallade certifikat (CRL) dokumenteras av [RFC 5280](https://tools.ietf.org/html/rfc5280). Certifikat är bara en del av en infrastruktur för X.509-offentliga nycklar (PKI). För att förstå X.509 PKI måste du förstå kryptografiska algoritmer, kryptografiska nycklar, certifikat och certifikatutfärdare (CA):

* **Algoritmer definierar** hur ursprungliga klartextdata omvandlas till chiffertext och tillbaka till klartext.
* **Nycklar** är slumpmässiga eller pseudoslumpdatasträngar som används som indata till en algoritm.
* **Certifikat** är digitala dokument som innehåller en entitets offentliga nyckel och gör att du kan avgöra om certifikatets ämne är vem eller vad det utger sig för att vara.
* **Certifikatutfärdare** intygar certifikatämnenas äkthet.

Du kan köpa ett certifikat från en certifikatutfärdare (CA). Du kan också, för testning och utveckling eller om du arbetar i en fristående miljö, skapa en själv signerad rotcertifikatutfärdare. Om du till exempel äger en eller flera enheter och testar IoT Hub-autentisering kan du själv signera rotcertifikatutfärdaren och använda den för att utfärda enhetscertifikat. Du kan också utfärda själv signerade enhetscertifikat. Detta beskrivs i efterföljande artiklar.

Innan vi diskuterar X.509-certifikat i detalj och använder dem för att autentisera enheter till en IoT Hub diskuterar vi kryptografin som certifikat baseras på.

## <a name="cryptography"></a>Kryptografi

Kryptografi används för att skydda information och kommunikation. Detta görs vanligtvis med hjälp av kryptografiska tekniker för att förvränga klartext (vanlig text) i chiffertext (kodad text) och tillbaka igen. Den här krypteringsprocessen kallas kryptering. Den omvända processen kallas för dekryptering. Kryptografi handlar om följande mål:

* **Konfidentialitet:** Informationen kan endast tolkas av den avsedda målgruppen.
* **Integritet:** Informationen kan inte ändras i lagring eller under överföring.
* **Oavvislighet:** Informationsskaparen kan inte senare neka skapandet.
* **Autentisering:** Avsändaren och mottagaren kan bekräfta varandras identitet.

## <a name="encryption"></a>Kryptering

Krypteringsprocessen kräver en algoritm och en nyckel. Algoritmen definierar hur data omvandlas från klartext till chiffertext och tillbaka till klartext. En nyckel är en slumpmässig sträng med data som används som indata till algoritmen. All säkerhet för processen finns i nyckeln. Därför måste nyckeln lagras på ett säkert sätt. Informationen om de mest populära algoritmerna är dock allmänt tillgänglig.

Det finns två typer av kryptering. Symmetrisk kryptering använder samma nyckel för både kryptering och dekryptering. Asymmetrisk kryptering använder olika men matematiskt relaterade nycklar för att utföra kryptering och dekryptering.

### <a name="symmetric-encryption"></a>Symmetrisk kryptering

Symmetrisk kryptering använder samma nyckel för att kryptera klartext i chiffertext och dekryptera chiffertext tillbaka till klartext. Nyckelns nödvändiga längd, uttryckt i antal bitar, bestäms av algoritmen. När nyckeln har använts för att kryptera klartext skickas det krypterade meddelandet till mottagaren som sedan dekrypterar chiffertexten. Den symmetriska nyckeln måste överföras på ett säkert sätt till mottagaren. Att skicka nyckeln är den största säkerhetsrisken när du använder en symmetrisk algoritm.

![Exempel på symmetrisk kryptering](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymmetrisk kryptering

Om endast symmetrisk kryptering används är problemet att alla parter i kommunikationen måste ha den privata nyckeln. Det är dock möjligt att obehöriga tredje parter kan hämta nyckeln under överföringen till behöriga användare. Åtgärda problemet genom att använda kryptografi med asymmetrisk eller offentlig nyckel i stället.

I asymmetrisk kryptografi har varje användare två matematiskt relaterade nycklar som kallas nyckelpar. En nyckel är offentlig och den andra är privat. Nyckelparet säkerställer att endast mottagaren har åtkomst till den privata nyckel som behövs för att dekryptera data. Följande bild sammanfattar processen för asymmetrisk kryptering.

![Exempel på asymmetrisk kryptering](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Mottagaren skapar ett offentligt/privat nyckelpar och skickar den offentliga nyckeln till en certifikatutfärdare. Certifikatutfärdaren paketerar den offentliga nyckeln i ett X.509-certifikat.

1. Den sändande parten hämtar mottagarens offentliga nyckel från certifikatutfärdaren.

1. Avsändaren krypterar klartextdata med hjälp av en krypteringsalgoritm. Mottagarens offentliga nyckel används för att utföra kryptering.

1. Avsändaren skickar chiffertexten till mottagaren. Du behöver inte skicka nyckeln eftersom mottagaren redan har den privata nyckel som behövs för att dekryptera chiffertexten.

1. Mottagaren dekrypterar chiffertexten med hjälp av den angivna asymmetriska algoritmen och den privata nyckeln.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Kombinera symmetrisk och asymmetrisk kryptering

Symmetrisk och asymmetrisk kryptering kan kombineras för att dra nytta av deras relativa styrkor. Symmetrisk kryptering är mycket snabbare än asymmetrisk, men på grund av behovet av att skicka privata nycklar till andra parter är det inte lika säkert. Om du vill kombinera de två typerna kan symmetrisk kryptering användas för att konvertera klartext till chiffertext. Asymmetrisk kryptering används för att utbyta den symmetriska nyckeln. Detta visas i följande diagram.

![Symmetrisk och assymetrisk kryptering](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Avsändaren hämtar mottagarens offentliga nyckel.

1. Avsändaren genererar en symmetrisk nyckel och använder den för att kryptera ursprungliga data.

1. Avsändaren använder mottagarens offentliga nyckel för att kryptera den symmetriska nyckeln.

1. Avsändaren skickar den krypterade symmetriska nyckeln och chiffertexten till den avsedda mottagaren.

1. Mottagaren använder den privata nyckel som matchar mottagarens offentliga nyckel för att dekryptera avsändarens symmetriska nyckel.

1. Mottagaren använder den symmetriska nyckeln för att dekryptera chiffertexten.

### <a name="asymmetric-signing"></a>Asymmetrisk signering

Asymmetriska algoritmer kan användas för att skydda data från ändringar och bevisa dataskaparens identitet. Följande bild visar hur asymmetrisk signering hjälper till att bevisa avsändarens identitet.

![Exempel på asymmetrisk signering](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Avsändaren skickar klartextdata via en asymmetrisk krypteringsalgoritm med hjälp av den privata nyckeln för kryptering. Observera att det här scenariot omvänder användningen av privata och offentliga nycklar som beskrivs i föregående avsnitt som beskriver asymmetrisk kryptering.

1. Resulterande chiffertext skickas till mottagaren.

1. Mottagaren hämtar avsändarens offentliga nyckel från en katalog.

1. Mottagaren dekrypterar chiffertexten med hjälp av avsändarens offentliga nyckel. Den resulterande klartext bevisar avsändarens identitet eftersom endast avsändaren har åtkomst till den privata nyckel som ursprungligen krypterade den ursprungliga texten.

## <a name="signing"></a>Signering

Digital signering kan användas för att avgöra om data har ändrats under överföring eller i vila. Data skickas via en hash-algoritm, en enkelvägsfunktion som genererar ett matematiskt resultat från det angivna meddelandet. Resultatet kallas för ett *hash-värde,* *meddelande sammanfattade,* *sammanfattade,* *signaturer* eller *tumavtryck*. Det går inte att återställa ett hash-värde för att hämta det ursprungliga meddelandet. Eftersom En liten ändring i meddelandet resulterar i en betydande ändring i tumavtrycket *kan* hash-värdet användas för att avgöra om ett meddelande har ändrats. Följande bild visar hur asymmetrisk kryptering och hash-algoritmer kan användas för att verifiera att ett meddelande inte har ändrats.

![Signeringsexempel](media/tutorial-x509-introduction/signing.png)

1. Avsändaren skapar ett meddelande i klartext.

1. Avsändaren hashar meddelandet i klartext för att skapa en sammanfattning av meddelandet.

1. Avsändaren krypterar sammanfattningen med hjälp av en privat nyckel.

1. Avsändaren skickar meddelandet i klartext och den krypterade sammanfattningen till den avsedda mottagaren.

1. Mottagaren dekrypterar sammanfattningen med hjälp av avsändarens offentliga nyckel.

1. Mottagaren kör samma hash-algoritm som avsändaren använde i meddelandet.

1. Mottagaren jämför den resulterande signaturen med den dekrypterade signaturen. Om sammanfattningarna är desamma ändrades inte meddelandet under överföringen.

## <a name="next-steps"></a>Nästa steg

Mer information om de fält som utgör ett certifikat finns i [Understanding X.509 Public Key Certificates (Förstå X.509-certifikat för offentliga nycklar).](tutorial-x509-certificates.md)

Om du redan vet mycket om X.509-certifikat och du vill generera testversioner som du kan använda för att autentisera till IoT Hub kan du läsa följande avsnitt:

* [Använda Microsoft-Supplied för att skapa testcertifikat](tutorial-x509-scripts.md)
* [Använda OpenSSL för att skapa testcertifikat](tutorial-x509-openssl.md)
* [Använda OpenSSL för att skapa Self-Signed testcertifikat](tutorial-x509-self-sign.md)

Om du har ett certifikat från en certifikatutfärdare (CA) eller ett underordnat CA-certifikat och vill ladda upp det till din IoT-hubb och bevisa att du äger den kan du se Bevisa innehavet av ett [CA-certifikat](tutorial-x509-prove-possession.md).
