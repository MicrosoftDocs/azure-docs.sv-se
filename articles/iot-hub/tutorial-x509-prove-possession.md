---
title: Självstudie – bevisa att du är ägare till CA-certifikat i Azure IoT Hub | Microsoft Docs
description: Självstudie – bevisa att du äger ett CA-certifikat för Azure IoT Hub
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
ms.openlocfilehash: 5e2827a4f87398f0a37ef04f797d2c7276d1a66d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384178"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Självstudie: bevisa innehav av ett CA-certifikat

När du har överfört certifikat utfärdare för rot certifikat utfärdare eller underordnat certifikat till din IoT-hubb måste du bevisa att du äger certifikatet:

1. Gå till din IoTHub i Azure Portal och välj **inställningar > certifikat**.

2. Välj **Lägg** till för att lägga till ett nytt CA-certifikat.

3. Ange ett visnings namn i fältet **certifikat namn** och välj det PEM-certifikat som du vill lägga till.

4. Välj **Spara**. Ditt certifikat visas i certifikat listan med statusen **overifierad**. Den här verifierings processen visar att du har till gång till certifikatet.

5. Välj certifikat för att Visa dialog rutan **certifikat information** .

6. Välj **generera verifierings kod** i dialog rutan.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Dialog rutan certifikat information}":::

7. Kopiera verifieringskoden till Urklipp. Du måste ange verifierings koden som certifikat ämne. Om verifierings koden till exempel är 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 kan du lägga till den som ämne för ditt certifikat, som du ser i nästa steg.

8. Det finns tre sätt att generera ett verifierings certifikat:

    * Om du använder PowerShell-skriptet från Microsoft kör `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` du för att skapa ett certifikat med namnet `VerifyCert4.cer` . Mer information finns i [använda skript som tillhandahålls av Microsoft](tutorial-x509-scripts.md).

    * Om du använder bash-skriptet som tillhandahålls av Microsoft kör `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` du för att skapa ett certifikat med namnet `verification-code.cert.pem` . Mer information finns i [använda skript som tillhandahålls av Microsoft](tutorial-x509-scripts.md).

    * Om du använder OpenSSL för att generera certifikat måste du först skapa en privat nyckel och sedan en certifikat signerings förfrågan (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Skapa sedan ett certifikat med hjälp av rot certifikat utfärdarens konfigurations fil (visas nedan) eller den underordnade CA-konfigurationsfilen och CSR-filen.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Mer information finns i [använda OpenSSL för att skapa test certifikat](tutorial-x509-openssl.md).

10. Välj det nya certifikatet i vyn **certifikat information** .

11. När du har laddat upp certifikatet väljer du **Verifiera**. Certifikat utfärdarens certifikat status bör ändras till **verifierad**.
