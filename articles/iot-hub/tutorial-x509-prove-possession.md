---
title: Självstudie – Bevisa ägarskapet för CA-certifikat i Azure IoT Hub | Microsoft Docs
description: Självstudie – Bevisa att du äger ett CA-certifikat för Azure IoT Hub
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378235"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Självstudie: Bevisa innehavet av ett CA-certifikat

När du har överfört ditt rotcertifikatutfärdarcertifikat (CA) eller ett underordnat CA-certifikat till din IoT-hubb måste du bevisa att du äger certifikatet:

1. I dialogrutan Azure Portal du till din IoTHub och väljer **Inställningar för > Certifikat**.

2. Välj **Lägg till** för att lägga till ett nytt CA-certifikat.

3. Ange ett visningsnamn i fältet **Certifikatnamn** och välj det PEM-certifikat som ska läggas till.

4. Välj **Spara**. Certifikatet visas i certifikatlistan med statusen **Overifierad.** Verifieringsprocessen bevisar att du har tillgång till certifikatet.

5. Välj certifikatet för att visa **dialogrutan Certifikatinformation.**

6. Välj **Generera verifieringskod** i dialogrutan.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="Dialogrutan {Certifikatinformation}":::

7. Kopiera verifieringskoden till Urklipp. Du måste ange verifieringskoden som certifikatämne. Om verifieringskoden till exempel är 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 lägger du till det som certifikatämne som visas i nästa steg.

8. Det finns tre sätt att generera ett verifieringscertifikat:

    * Om du använder Det PowerShell-skript som tillhandahålls av Microsoft kör du för `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` att skapa ett certifikat med namnet `VerifyCert4.cer` . Mer information finns i [Använda skript från Microsoft.](tutorial-x509-scripts.md)

    * Om du använder Bash-skriptet som tillhandahålls av Microsoft kör du för `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` att skapa ett certifikat med namnet `verification-code.cert.pem` . Mer information finns i [Använda skript från Microsoft.](tutorial-x509-scripts.md)

    * Om du använder OpenSSL för att generera certifikat måste du först generera en privat nyckel och sedan en certifikatsigneringsbegäran (CSR):

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

      Skapa sedan ett certifikat med hjälp av rotcertifikatutfärdarkonfigurationsfilen (visas nedan) eller den underordnade CA-konfigurationsfilen och CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Mer information finns i Använda [OpenSSL för att skapa testcertifikat.](tutorial-x509-openssl.md)

10. Välj det nya certifikatet i **vyn Certifikatinformation.**

11. När certifikatet har laddats upp väljer du **Verifiera**. Ca-certifikatstatusen bör ändras till **Verifierad.**
