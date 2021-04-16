---
title: Självstudie – Använda Microsoft-skript för att skapa x.509-testcertifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – Använda anpassade skript för att skapa CERTIFIKATutfärdare och enhetscertifikat för Azure IoT Hub
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
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378218"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Självstudie: Använda skript från Microsoft för att skapa testcertifikat

Microsoft tillhandahåller PowerShell- och Bash-skript som hjälper dig att förstå hur du skapar dina egna X.509-certifikat och autentiserar dem till en IoT Hub. Skripten finns på en [GitHub-lagringsplats.](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) De tillhandahålls endast i demonstrationssyfte. Certifikat som skapas av dem får inte användas för produktion. Certifikaten innehåller hårdkodade lösenord ("1234") och upphör att gälla efter 30 dagar. För en produktionsmiljö måste du använda dina egna metodtips för att skapa certifikat och hantera livslängden.

## <a name="powershell-scripts"></a>PowerShell-skript

### <a name="step-1---setup"></a>Steg 1 – Installation

Hämta OpenSSL för Windows. Se <https://www.openssl.org/docs/faq.html#MISC4> för platser där du kan ladda ned den eller skapa från <https://www.openssl.org/source/> källan. Kör sedan de preliminära skripten:

1. Kopiera skripten från den här [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) till den lokala katalog där du vill arbeta. Alla filer skapas som underordnade till den här katalogen.

1. Starta PowerShell som administratör.

1. Ändra till den katalog där du har läst in skripten.

1. På kommandoraden anger du miljövariabeln till den katalog där `$ENV:OPENSSL_CONF` openssl-konfigurationsfilen (openssl.cnf) finns.

1. Kör `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` så att PowerShell kan köra skripten.

1. Kör `. .\ca-certs.ps1`. Detta för in funktionerna i skriptet i det globala PowerShell-namnområdet.

1. Kör `Test-CACertsPrerequisites`. PowerShell använder Windows Certificate Store för att hantera certifikat. Det här kommandot verifierar att det inte kommer att finnas namnkollisioner senare med befintliga certifikat och att OpenSSL har ställts in korrekt.

### <a name="step-2---create-certificates"></a>Steg 2 – Skapa certifikat

Kör `New-CACertsCertChain [ecc|rsa]`. ECC rekommenderas för CA-certifikat men krävs inte. Det här skriptet uppdaterar katalogen och Windows-certifikatarkivet med följande CERTIFIKATUTFÄRDARE och mellanliggande certifikat:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

När skriptet har körts lägger du till det nya CA-certifikatet (RootCA.pem) i IoT Hub:

1. Gå till IoT Hub och gå till Certifikat.

1. Välj **Lägg till**.

1. Ange ett visningsnamn för CA-certifikatet.

1. Ladda upp CA-certifikatet.

1. Välj **Spara**.

### <a name="step-3---prove-possession"></a>Steg 3 – Bevisa innehav

Nu när du har laddat upp ca-certifikatet till IoT Hub måste du bevisa att du faktiskt äger det:

1. Välj det nya CA-certifikatet.

1. Välj **Generera verifieringskod** i **dialogrutan Certifikatinformation.** Mer information finns i Bevisa [innehavet av ett CA-certifikat.](tutorial-x509-prove-possession.md)

1. Skapa ett certifikat som innehåller verifieringskoden. Om verifieringskoden till exempel är `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` kör du följande för att skapa ett nytt certifikat i arbetskatalogen som innehåller ämnet `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Skriptet skapar ett certifikat med namnet `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Ladda `VerifyCert4.cer` upp IoT Hub i dialogrutan **Certifikatinformation.**

1. Välj **Verifiera**.

### <a name="step-4---create-a-new-device"></a>Steg 4 – Skapa en ny enhet

Skapa en enhet för din IoT Hub:

1. I ditt IoT Hub går du till avsnittet **IoT-enheter.**

1. Lägg till en ny enhet med `mydevice` ID: .

1. För autentisering väljer du **X.509 CA-signerad**.

1. Kör `New-CACertsDevice mydevice` för att skapa ett nytt enhetscertifikat. Detta skapar följande filer i arbetskatalogen:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Steg 5 – Testa enhetscertifikatet

Gå till [Testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om enhetscertifikatet kan autentisera till IoT Hub. Du behöver PFX-versionen av certifikatet, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Steg 6 – Rensning

På Start-menyn öppnar du **Hantera datorcertifikat** och går till **Certifikat – Lokal dator > personligt.** Ta bort certifikat som utfärdats av "Azure IoT CA TestOnly*". Ta på samma sätt bort lämpliga certifikat från>betrodda rotcertifikatutfärdare > certifikat och >mellanliggande **certifikatutfärdare > certifikat**.

## <a name="bash-scripts"></a>Bash-skript

### <a name="step-1---setup"></a>Steg 1 – Installation

1. Starta Bash.

1. Ändra till den katalog där du vill arbeta. Alla filer skapas i den här katalogen.

1. Kopiera `*.cnf` och `*.sh` till arbetskatalogen.

### <a name="step-2---create-certificates"></a>Steg 2 – Skapa certifikat

1. Kör `./certGen.sh create_root_and_intermediate`. Detta skapar följande filer i **katalogen certs:**

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Gå till IoT Hub och gå till **Certifikat**.

1. Välj **Lägg till**.

1. Ange ett visningsnamn för CA-certifikatet.

1. Överför endast CA-certifikatet till IoT Hub. Namnet på certifikatet är `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Välj **Spara**.

### <a name="step-3---prove-possession"></a>Steg 3 – Bevisa innehav

1. Välj det nya CA-certifikat som skapades i föregående steg.

1. Välj **Generera verifieringskod** i **dialogrutan Certifikatinformation.** Mer information finns i Bevisa [innehavet av ett CA-certifikat.](tutorial-x509-prove-possession.md)

1. Skapa ett certifikat som innehåller verifieringskoden. Om verifieringskoden till exempel är kör du följande för att skapa ett nytt certifikat i arbetskatalogen med `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` namnet som innehåller ämnet `verification-code.cert.pem` `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Ladda upp certifikatet till din IoT-hubb i **dialogrutan Certifikatinformation.**

1. Välj **Verifiera**.

### <a name="step-4---create-a-new-device"></a>Steg 4 – Skapa en ny enhet

Skapa en enhet för din IoT-hubb:

1. I ditt IoT Hub går du till avsnittet IoT-enheter.

1. Lägg till en ny enhet med `mydevice` ID: .

1. För autentisering väljer du **X.509 CA-signerad**.

1. Kör `./certGen.sh create_device_certificate mydevice` för att skapa ett nytt enhetscertifikat. Detta skapar två filer med `new-device.cert.pem` namnet och filer i `new-device.cert.pfx` arbetskatalogen.

### <a name="step-5---test-your-device-certificate"></a>Steg 5 – Testa enhetscertifikatet

Gå till [Testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om enhetscertifikatet kan autentisera till IoT Hub. Du behöver PFX-versionen av certifikatet, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Steg 6 – Rensning

Eftersom bash-skriptet bara skapar certifikat i arbetskatalogen tar du bara bort dem när du är klar med testningen.

## <a name="next-steps"></a>Nästa steg

Om du vill testa certifikatet går du [till Testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om certifikatet kan autentisera enheten till din IoT Hub.
