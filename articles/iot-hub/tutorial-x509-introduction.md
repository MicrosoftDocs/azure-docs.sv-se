---
title: Självstudie – förstå kryptografi-och X. 509-certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – förstå kryptografi och X. 509 PKI för Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 88f5803e1d4348b79c7675d627a541ff47700dc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630804"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Självstudie: förstå offentlig nyckel kryptografi och X. 509 infrastruktur för offentliga nycklar

Du kan använda X. 509-certifikat för att autentisera enheter till en Azure-IoT Hub. Ett certifikat är ett digitalt dokument som innehåller enhetens offentliga nyckel och som kan användas för att kontrol lera att enheten är den som den hävdar. X. 509-certifikat och listor över återkallade certifikat (CRL) dokumenteras av [RFC 5280](https://tools.ietf.org/html/rfc5280). Certifikat är bara en del av en X. 509 PKI (Public Key Infrastructure). För att förstå X. 509 PKI måste du förstå kryptografiska algoritmer, kryptografiska nycklar, certifikat och certifikat utfärdare (ca: er):

* **Algoritmer** definierar hur ursprungliga klartext-data omvandlas till chiffertexten och tillbaka till klartext.
* **Nycklar** är slumpmässiga eller pseudorandom data strängar som används som indata till en algoritm.
* **Certifikat** är digitala dokument som innehåller en entitets offentliga nyckel och gör det möjligt att avgöra om certifikatets ämne är det eller vad det hävdar att det är.
* **Certifikat utfärdare** bekräftar äktheten på certifikat ämnen.

Du kan köpa ett certifikat från en certifikat utfärdare (CA). Du kan också skapa en självsignerad rot certifikat utfärdare för testning och utveckling eller om du arbetar i en fristående miljö. Om du till exempel äger en eller flera enheter och testar IoT Hub-autentisering, kan du själv signera rot certifikat utfärdaren och använda den för att utfärda enhets certifikat. Du kan också utfärda självsignerade enhets certifikat. Detta beskrivs i efterföljande artiklar.

Innan vi diskuterar X. 509-certifikat i mer detalj och använder dem för att autentisera enheter till en IoT Hub, diskuterar vi den kryptografiskhet som certifikaten baseras på.

## <a name="cryptography"></a>Kryptografi

Kryptografi används för att skydda information och kommunikation. Detta görs vanligt vis med hjälp av kryptografiska metoder för att avkoda klartext (vanlig text) till chiffertexten (kodad text) och tillbaka igen. Den här processen kallas kryptering. Den omvända processen kallas dekryptering. Kryptografi är bekymrat med följande mål:

* **Konfidentialitet**: informationen kan endast tolkas av den avsedda mål gruppen.
* **Integritet**: informationen kan inte ändras i lagring eller under överföring.
* **Oavvislig het**: skaparen av information kan inte senare neka den skapade.
* **Autentisering**: avsändaren och mottagaren kan bekräfta var and s identitet.

## <a name="encryption"></a>Kryptering

Krypterings processen kräver en algoritm och en nyckel. Algoritmen definierar hur data omvandlas från klartext till chiffertexten och tillbaka till klartext. En nyckel är en slumpmässig sträng med data som används som indata för algoritmen. All säkerhet för processen finns i nyckeln. Därför måste nyckeln lagras på ett säkert sätt. Information om de mest populära algoritmerna är dock allmänt tillgängliga.

Det finns två typer av kryptering. Symmetrisk kryptering använder samma nyckel för både kryptering och dekryptering. Asymmetrisk kryptering använder olika men matematiskt relaterade nycklar för att utföra kryptering och dekryptering.

### <a name="symmetric-encryption"></a>Symmetrisk kryptering

Symmetrisk kryptering använder samma nyckel för att kryptera klartext i chiffertexten och dekryptera chiffertexten i klartext. Nyckelns nödvändiga längd, uttryckt i antal bitar, bestäms av algoritmen. När nyckeln används för att kryptera oformaterad text skickas det krypterade meddelandet till mottagaren som sedan dekrypterar chiffertexten. Den symmetriska nyckeln måste skickas säkert till mottagaren. Att skicka nyckeln är den största säkerhets risken när du använder en symmetrisk algoritm.

![Exempel på symmetrisk kryptering](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymmetrisk kryptering

Om endast symmetrisk kryptering används, är problemet att alla parter i kommunikationen måste ha den privata nyckeln. Det är dock möjligt att obehöriga tredje parter kan samla in nyckeln under överföringen till behöriga användare. Åtgärda problemet genom att använda asymmetrisk eller offentlig nyckel kryptering i stället.

I asymmetrisk kryptering har varje användare två matematiskt relaterade nycklar som kallas nyckel par. En nyckel är offentlig och den andra nyckeln är privat. Nyckel paret ser till att endast mottagaren har åtkomst till den privata nyckel som krävs för att dekryptera data. Följande bild sammanfattar den asymmetriska krypterings processen.

![Exempel på asymmetrisk kryptering](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Mottagaren skapar ett offentligt privat privat nyckel par och skickar den offentliga nyckeln till en certifikat utfärdare. Certifikat utfärdaren paketerar den offentliga nyckeln i ett X. 509-certifikat.

1. Sändnings parten hämtar mottagarens offentliga nyckel från certifikat utfärdaren.

1. Avsändaren krypterar oformaterade data med en krypteringsalgoritm. Mottagarens offentliga nyckel används för att utföra kryptering.

1. Avsändaren skickar chiffertexten till mottagaren. Det är inte nödvändigt att skicka nyckeln eftersom mottagaren redan har den privata nyckel som krävs för att dekryptera chiffertexten.

1. Mottagaren dekrypterar chiffertexten med hjälp av den angivna asymmetriska algoritmen och den privata nyckeln.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Kombinera symmetrisk och asymmetrisk kryptering

Symmetrisk och asymmetrisk kryptering kan kombineras för att dra nytta av deras relativa styrkor. Symmetrisk kryptering är mycket snabbare än asymmetrisk, men eftersom det är nödvändigt att skicka privata nycklar till andra parter är det inte lika säkert. För att kombinera de två typerna tillsammans kan symmetrisk kryptering användas för att konvertera klartext till chiffertexten. Asymmetrisk kryptering används för att utväxla den symmetriska nyckeln. Detta visas i följande diagram.

![Symmetrisk och assymetric kryptering](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Avsändaren hämtar mottagarens offentliga nyckel.

1. Avsändaren genererar en symmetrisk nyckel och använder den för att kryptera ursprungliga data.

1. Avsändaren använder mottagarens offentliga nyckel för att kryptera den symmetriska nyckeln.

1. Avsändaren skickar den krypterade symmetriska nyckeln och chiffertexten till den avsedda mottagaren.

1. Mottagaren använder den privata nyckeln som matchar mottagarens offentliga nyckel för att dekryptera avsändarens symmetriska nyckel.

1. Mottagaren använder den symmetriska nyckeln för att dekryptera chiffertexten.

### <a name="asymmetric-signing"></a>Asymmetrisk signering

Asymmetriska algoritmer kan användas för att skydda data från ändringar och bevisa identiteten för data skaparen. Följande bild visar hur asymmetrisk signering hjälper till att bevisa avsändarens identitet.

![Exempel på asymmetrisk signering](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Avsändaren skickar data i klartext via en algoritm för asymmetrisk kryptering med hjälp av den privata nyckeln för kryptering. Observera att det här scenariot används för att ångra användningen av privata och offentliga nycklar som beskrivs i föregående avsnitt som beskriver asymmetrisk kryptering.

1. Den resulterande chiffertexten skickas till mottagaren.

1. Mottagaren hämtar den offentliga nyckeln från en katalog.

1. Mottagaren dekrypterar chiffertexten med hjälp av den offentliga nyckeln för den ursprungliga nyckeln. Den resulterande klar text filen bevisar identiteten för den som ursprungligen krypterade den ursprungliga texten.

## <a name="signing"></a>Signering

Digital signering kan användas för att avgöra om data har ändrats under överföring eller i vila. Data skickas via en hash-algoritm, en envägs funktion som skapar ett matematiskt resultat från det angivna meddelandet. Resultatet kallas ett hash- *värde*, sammandrag av *meddelande*, *Digest*, *signatur* eller *tumavtryck*. Det går inte att återställa ett hash-värde för att hämta det ursprungliga meddelandet. Eftersom en liten ändring i meddelandet resulterar i en betydande ändring i *tumavtrycket*, kan hash-värdet användas för att avgöra om ett meddelande har ändrats. Följande bild visar hur asymmetrisk kryptering och hash-algoritmer kan användas för att kontrol lera att ett meddelande inte har ändrats.

![Signerings exempel](media/tutorial-x509-introduction/signing.png)

1. Avsändaren skapar ett meddelande i klartext.

1. Avsändaren hashar meddelandet i klartext för att skapa en meddelande sammandrag.

1. Avsändaren krypterar sammanfattningen med en privat nyckel.

1. Avsändaren skickar meddelandet oformaterad text och den krypterade Digesten till den avsedda mottagaren.

1. Mottagaren dekrypterar sammanfattningen med hjälp av avsändarens offentliga nyckel.

1. Mottagaren kör samma hash-algoritm som avsändaren använde i meddelandet.

1. Mottagaren jämför den resulterande signaturen med den dekrypterade signaturen. Om sammanfattningarna är desamma har meddelandet inte ändrats under överföringen.

## <a name="next-steps"></a>Nästa steg

Mer information om fälten som utgör ett certifikat finns i [förstå certifikat från offentliga nycklar i X. 509](tutorial-x509-certificates.md).

Om du redan känner till ett mycket om X. 509-certifikat och du vill generera test versioner som du kan använda för att autentisera till din IoT Hub, se följande avsnitt:

* [Använda Microsoft-Supplied skript för att skapa test certifikat](tutorial-x509-scripts.md)
* [Använda OpenSSL för att skapa test certifikat](tutorial-x509-openssl.md)
* [Använda OpenSSL för att skapa Self-Signed test certifikat](tutorial-x509-self-sign.md)

Om du har ett certifikat för certifikat utfärdare (CA) eller ett underordnat certifikat för certifikat utfärdare och du vill överföra det till din IoT-hubb och se till att du äger det kan du läsa mer i [bevisa innehav av ett CA-certifikat](tutorial-x509-prove-possession.md).
