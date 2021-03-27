---
title: Självstudie – Använd Microsoft-skript för att skapa x. 509 test certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – Använd anpassade skript för att skapa CA-och enhets certifikat för Azure IoT Hub
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
ms.openlocfilehash: f11aec770818cd4ceeeda1ae7decf30acb9ca92b
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630773"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Självstudie: använda Microsoft-skript för att skapa test certifikat

Microsoft tillhandahåller PowerShell-och bash-skript som hjälper dig att förstå hur du skapar dina egna X. 509-certifikat och autentiserar dem till en IoT Hub. Skripten finns i [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates). De tillhandahålls endast i demonstrations syfte. Certifikat som skapats av dem får inte användas för produktion. Certifikaten innehåller hårdkodade lösen ord ("1234") och upphör att gälla efter 30 dagar. För en produktions miljö måste du använda dina egna metod tips för att skapa och hantera certifikat.

## <a name="powershell-scripts"></a>PowerShell-skript

### <a name="step-1---setup"></a>Steg 1 – installation

Hämta OpenSSL för Windows. Se <https://www.openssl.org/docs/faq.html#MISC4> för platser för att ladda ned den eller <https://www.openssl.org/source/> bygga från källan. Kör sedan de preliminära skripten:

1. Kopiera skripten från [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) till den lokala katalog där du vill arbeta. Alla filer kommer att skapas som underordnade till den här katalogen.

1. Starta PowerShell som administratör.

1. Ändra till den katalog där du laddade skripten.

1. På kommando raden anger du miljövariabeln `$ENV:OPENSSL_CONF` till den katalog där konfigurations filen för openssl (OpenSSL. cnf) finns.

1. Kör `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` så att PowerShell kan köra skripten.

1. Kör `. .\ca-certs.ps1`. Detta hämtar funktionerna i skriptet till den globala PowerShell-namnrymden.

1. Kör `Test-CACertsPrerequisites`. PowerShell använder Windows certifikat Arkiv för att hantera certifikat. Det här kommandot kontrollerar att det inte finns namn konflikter senare med befintliga certifikat och att OpenSSL har kon figurer ATS korrekt.

### <a name="step-2---create-certificates"></a>Steg 2 – Skapa certifikat

Kör `New-CACertsCertChain [ecc|rsa]`. ECC rekommenderas för CA-certifikat men krävs inte. Det här skriptet uppdaterar din katalog och Windows certifikat Arkiv med följande certifikat utfärdare och mellanliggande certifikat:

* intermediate1. pem
* intermediate2. pem
* intermediate3. pem
* RootCA. cer
* RootCA. pem

När du har kört skriptet lägger du till det nya CA-certifikatet (RootCA. pem) till din IoT Hub:

1. Gå till din IoT Hub och gå till certifikat.

1. Välj **Lägg till**.

1. Ange ett visnings namn för CA-certifikatet.

1. Ladda upp CA-certifikatet.

1. Välj **Spara**.

### <a name="step-3---prove-possession"></a>Steg 3 – bevisa innehav

Nu när du har laddat upp CA-certifikatet till din IoT Hub måste du bevisa att du faktiskt äger det:

1. Välj det nya CA-certifikatet.

1. Välj **generera verifierings kod** i dialog rutan **certifikat information** . Mer information finns i [bevisa innehav av ett CA-certifikat](tutorial-x509-prove-possession.md).

1. Skapa ett certifikat som innehåller verifierings koden. Om verifierings koden till exempel är `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` kör du följande för att skapa ett nytt certifikat i din arbets katalog som innehåller ämnet `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Skriptet skapar ett certifikat med namnet `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Ladda upp `VerifyCert4.cer` IoT Hub i dialog rutan **certifikat information** .

1. Välj **Verifiera**.

### <a name="step-4---create-a-new-device"></a>Steg 4 – skapa en ny enhet

Skapa en enhet för IoT Hub:

1. I IoT Hub navigerar du till avsnittet **IoT-enheter** .

1. Lägg till en ny enhet med ID `mydevice` .

1. För autentisering väljer du **X. 509 ca signerad**.

1. Kör `New-CACertsDevice mydevice` för att skapa ett nytt enhets certifikat. Följande filer skapas i arbets katalogen:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Steg 5 – testa ditt enhets certifikat

Gå till [testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om ditt enhets certifikat kan autentiseras för din IoT Hub. Du behöver PFX-versionen av ditt certifikat, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Steg 6 – rensa

Från Start-menyn öppnar du **Hantera dator certifikat** och navigerar till  **certifikat – lokal dator > personliga**. Ta bort certifikat som utfärdats av "Azure IoT CA TestOnly *". Ta på samma sätt bort lämpliga certifikat från **>betrodd rot certifikat utfärdare > certifikat och >mellanliggande certifikat utfärdare > certifikat**.

## <a name="bash-scripts"></a>Bash-skript

### <a name="step-1---setup"></a>Steg 1 – installation

1. Starta bash.

1. Ändra till den katalog som du vill arbeta med. Alla filer kommer att skapas i den här katalogen.

1. Kopiera `*.cnf` och `*.sh` till din arbets katalog.

### <a name="step-2---create-certificates"></a>Steg 2 – Skapa certifikat

1. Kör `./certGen.sh create_root_and_intermediate`. Följande filer skapas i katalogen med **certifikat** :

    * Azure-IoT-test-Only. Chain. ca. cert. pem
    * Azure-IoT-test-Only. mellanliggande. cert. pem
    * Azure-IoT-test-Only. root. ca. cert. pem

1. Gå till din IoT Hub och gå till **certifikat**.

1. Välj **Lägg till**.

1. Ange ett visnings namn för CA-certifikatet.

1. Ladda bara upp CA-certifikatet till IoT Hub. Namnet på certifikatet är `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Välj **Spara**.

### <a name="step-3---prove-possession"></a>Steg 3 – bevisa innehav

1. Välj det nya CA-certifikatet som skapades i föregående steg.

1. Välj **generera verifierings kod** i dialog rutan **certifikat information** . Mer information finns i [bevisa innehav av ett CA-certifikat](tutorial-x509-prove-possession.md).

1. Skapa ett certifikat som innehåller verifierings koden. Om verifierings koden till exempel är `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` kör du följande för att skapa ett nytt certifikat i din arbets katalog med namnet `verification-code.cert.pem` som innehåller ämnet `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Ladda upp certifikatet till din IoT-hubb i dialog rutan **certifikat information** .

1. Välj **Verifiera**.

### <a name="step-4---create-a-new-device"></a>Steg 4 – skapa en ny enhet

Skapa en enhet för din IoT-hubb:

1. I IoT Hub navigerar du till avsnittet IoT-enheter.

1. Lägg till en ny enhet med ID `mydevice` .

1. För autentisering väljer du **X. 509 ca signerad**.

1. Kör `./certGen.sh create_device_certificate mydevice` för att skapa ett nytt enhets certifikat. Detta skapar två filer med namnet `new-device.cert.pem` och `new-device.cert.pfx` filer i din arbets katalog.

### <a name="step-5---test-your-device-certificate"></a>Steg 5 – testa ditt enhets certifikat

Gå till [testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om ditt enhets certifikat kan autentiseras för din IoT Hub. Du behöver PFX-versionen av ditt certifikat, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Steg 6 – rensa

Eftersom bash-skriptet bara skapar certifikat i din arbets katalog, tar du bara bort dem när du är klar med testet.

## <a name="next-steps"></a>Nästa steg

Om du vill testa certifikatet går du till [testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om ditt certifikat kan autentisera din enhet till din IoT Hub.
