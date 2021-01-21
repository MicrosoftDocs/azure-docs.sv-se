---
title: Registrera en ny enhet – Azure IoT Edge | Microsoft Docs
description: Registrera en enskild IoT Edge enhet i IoT Hub för manuell etablering med antingen symmetriska nycklar eller X. 509-certifikat
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 97faf5eff7187bbabe23bbcab60514eef4acc063
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634381"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrera en IoT Edge enhet i IoT Hub

Den här artikeln innehåller anvisningar för att registrera en ny IoT Edge enhet i IoT Hub.

Alla enheter som ansluter till en IoT-hubb har ett enhets-ID som används för att spåra kommunikation från moln till enhet eller från enhet till moln. Du konfigurerar en enhet med dess anslutnings information, som innehåller IoT Hub-värdnamnet, enhets-ID: t och den information som enheten använder för att autentisera till IoT Hub.

Stegen i den här artikeln går igenom en process som kallas manuell etablering, där du ansluter en enskild enhet till IoT-hubben. För manuell etablering har du två alternativ för att autentisera IoT Edge enheter:

* **Symmetrisk nyckel**: när du skapar en ny enhets identitet i IoT Hub skapar tjänsten två nycklar. Du placerar en av nycklarna på enheten, och den presenterar nyckeln för att IoT Hub vid autentisering.

  Den här autentiseringsmetoden är snabbare för att komma igång, men inte lika säkert.

* **X. 509-egensignerat**: du skapar två X. 509-identitets certifikat och placerar dem på enheten. När du skapar en ny enhets identitet i IoT Hub ger du tumavtrycken från båda certifikaten. När enheten autentiserar till IoT Hub, visar den ett certifikat och IoT Hub verifierar att certifikatet matchar dess tumavtryck.

  Den här autentiseringsmetoden är säkrare och rekommenderas för produktions scenarier.

I den här artikeln beskrivs båda autentiseringsmetoderna.

Om du har många enheter att konfigurera och inte vill etablera var och en kan du använda någon av följande artiklar för att lära dig hur IoT Edge fungerar med IoT Hub Device Provisioning Service:

* [Skapa och etablera IoT Edge enheter med X. 509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera IoT Edge enheter med en TPM](how-to-auto-provision-simulated-device-linux.md)
* [Skapa och etablera IoT Edge enheter med symmetriska nycklar](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* En kostnads fri eller standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Din Azure CLI-version måste vara minst 2.0.70 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Alternativ 1: registrera med symmetriska nycklar

Du kan använda flera verktyg för att registrera en ny IoT Edge enhet i IoT Hub och hämta dess anslutnings sträng, beroende på din preferens.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I IoT-hubben i Azure Portal skapas och hanteras IoT Edge enheter separat från IoT-enheter som inte är Edge-aktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. Välj **IoT Edge** på menyn i den vänstra rutan och välj sedan **lägg till en IoT Edge enhet**.

   ![Lägg till en IoT Edge-enhet från Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. På sidan **skapa en enhet** anger du följande information:

   * Skapa ett beskrivande enhets-ID.
   * Välj **symmetrisk nyckel** som autentiseringstyp.
   * Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och Anslut den nya enheten till hubben.

1. Välj **Spara**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT Hub

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja hubben.

1. Öppna vyn **Utforskare** i Visual Studio Code.
1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Avsnittet Expandera Azure IoT Hub-enheter](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **välj IoT Hub**.
1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.
1. Välj din Azure-prenumeration.
1. Välj din IoT-hubb.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrera en ny enhet med Visual Studio Code

1. I Visual Studio Code Explorer expanderar du avsnittet **Azure IoT Hub** .
1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **skapa IoT Edge enhet**.
1. Ge din enhet ett ID i text rutan som öppnas.

På skärmen utdata ser du resultatet av kommandot. Enhets informationen skrivs ut, som innehåller den **deviceId** som du har angett och **ConnectionString** som du kan använda för att ansluta den fysiska enheten till IoT-hubben.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) för att skapa en ny enhets identitet i din IoT-hubb. Till exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt i IoT Hub.
* `--hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : deklarera att enheten är en IoT Edge enhet.

   ![AZ IoT Hub-enhet – identitet skapa utdata](./media/how-to-register-device/create-edge-device-cli.png)

---

Nu när du har en enhet registrerad i IoT Hub hämtar du anslutnings strängen som du använder för att slutföra installationen och etableringen av IoT Edge Runtime. Följ stegen längre fram i den här artikeln för att [Visa registrerade enheter och hämta anslutnings strängar](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Alternativ 2: registrera med X. 509-certifikat

Manuell etablering med X. 509-certifikat kräver IoT Edge version 1.0.10 eller senare.

För X. 509-certifikatautentisering anges varje enhets autentiseringsinformation i form av *tumavtrycken* som tas från dina enhets identitets certifikat. Dessa tumavtrycken ges för att IoT Hub vid tidpunkten för enhets registrering så att tjänsten kan identifiera enheten när den ansluts.

### <a name="create-certificates-and-thumbprints"></a>Skapa certifikat och tumavtrycken

När du etablerar en IoT Edge-enhet med X. 509-certifikat kan du använda vad som kallas för *enhets identitets certifikat*. Det här certifikatet används endast för att tillhandahålla en IoT Edge enhet och autentisera enheten med Azure IoT Hub. Det är ett löv certifikat som inte signerar andra certifikat. Enhetens identitets certifikat skiljer sig från certifikat utfärdarens certifikat (CA) som IoT Edges enheten presenterar moduler eller underordnade enheter för verifiering. Mer information om hur CA-certifikat används i IoT Edge enheter finns i [förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Du behöver följande filer för manuell etablering med X. 509:

* Två av enhets identitets certifikaten med matchande privat nyckel certifikat i. cer-eller. pem-format.

  En uppsättning certifikat/nyckelfiler ges till IoT Edge Runtime. När du skapar enhets identitets certifikat anger du certifikatets egna namn (CN) med det enhets-ID som du vill att enheten ska ha i IoT-hubben.

* Tumavtrycken tas från både enhets identitets certifikat.

  Tumavtryck-värden är 40-hex-tecken för SHA-1-hashar eller 64-hex-tecken för SHA-256-hashar. Båda tumavtrycken tillhandahålls för att IoT Hub vid tidpunkten för enhets registrering.

Om du inte har tillgängliga certifikat kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). Följ anvisningarna i artikeln för att konfigurera skript för att skapa certifikat, skapa ett rot certifikat för certifikat utfärdare och skapa sedan två IoT Edge enhets identitets certifikat.

Ett sätt att hämta tumavtrycket från ett certifikat är med följande openssl-kommando:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrera en ny enhet

Du kan använda flera verktyg för att registrera en ny IoT Edge enhet i IoT Hub och ladda upp certifikatets tumavtrycken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I IoT-hubben i Azure Portal skapas och hanteras IoT Edge enheter separat från IoT-enheter som inte är Edge-aktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. Välj **IoT Edge** på menyn i den vänstra rutan och välj sedan **lägg till en IoT Edge enhet**.

   ![Lägg till en IoT Edge-enhet från Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. På sidan **skapa en enhet** anger du följande information:

   * Skapa ett beskrivande enhets-ID. Anteckna det här enhets-ID: t eftersom du kommer att använda det i nästa avsnitt.
   * Välj **X. 509 själv signerad** som autentiseringstyp.
   * Ange det primära och sekundära identitets certifikatets tumavtrycken. Tumavtryck-värden är 40-hex-tecken för SHA-1-hashar eller 64-hex-tecken för SHA-256-hashar.

1. Välj **Spara**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

För närvarande stöder inte Azure IoT-tillägget för Visual Studio Code enhets registrering med X. 509-certifikat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) för att skapa en ny enhets identitet i din IoT-hubb. Till exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Det här kommandot innehåller flera parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt för din IoT-hubb. Anteckna det här enhets-ID: t eftersom du kommer att använda det i nästa avsnitt.
* `hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : deklarera att enheten är en IoT Edge enhet.
* `--auth-method` eller `--am` : deklarera den typ av auktorisering som enheten ska använda. I det här fallet använder vi X. 509-certifikat tumavtrycken.
* `--primary-thumbprint` eller `--ptp` : Ange ett X. 509-tumavtryck för certifikat som ska användas som primär nyckel.
* `--secondary-thumbprint` eller `--stp` : Ange ett X. 509-tumavtryck för certifikat som ska användas som sekundär nyckel.

---

Nu när du har en enhet registrerad i IoT Hub är du redo att installera och konfigurera IoT Edge runtime på enheten. IoT Edge enheter som autentiseras med X. 509-certifikat använder inte anslutnings strängar, så du kan fortsätta till nästa steg:

* [Installera eller avinstallera Azure IoT Edge för Linux](how-to-install-iot-edge.md)
* [Installera eller avinstallera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Visa registrerade enheter och hämta anslutnings strängar

Enheter som använder symmetrisk nyckel autentisering behöver sina anslutnings strängar för att slutföra installationen och etableringen av IoT Edge Runtime.

Enheter som använder autentisering med X. 509-certifikat behöver inte anslutnings strängar. I stället behöver de enheterna sitt IoT Hub-namn, deras enhets namn och deras certifikatfiler för att slutföra installationen och etableringen av IoT Edge Runtime.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alla Edge-aktiverade enheter som ansluter till din IoT Hub visas på sidan **IoT Edge** .

![Använd Azure Portal för att visa alla IoT Edge enheter i din IoT Hub](./media/how-to-register-device/portal-view-devices.png)

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub.

Enheter som autentiserar med symmetriska nycklar har anslutnings strängar tillgängliga för kopiering i portalen.

1. På sidan **IoT Edge** i portalen klickar du på enhets-ID i listan över IoT Edge enheter.
2. Kopiera värdet för antingen **primär anslutnings sträng** eller **sekundär anslutnings sträng**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visa IoT Edge enheter med Visual Studio Code

Alla enheter som ansluter till din IoT Hub visas i avsnittet **Azure IoT Hub** i Visual Studio Code Explorer. IoT Edge enheter kan särskiljas från icke-Edge-enheter med en annan ikon, och det faktum att modulerna **$edgeAgent** och **$edgeHub** distribueras till varje IoT Edge enhet.

![Använd VS Code för att visa alla IoT Edge enheter i din IoT Hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Hämta anslutnings strängen med Visual Studio Code

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub.

1. Högerklicka på enhetens ID i **Azure IoT Hub** -avsnittet.
1. Välj **Kopiera enhets anslutnings sträng**.

   Anslutnings strängen kopieras till Urklipp.

Du kan också välja **Hämta enhets information** på snabb menyn för att se all enhets information, inklusive anslutnings strängen, i fönstret utdata.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge enheter med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) för att visa alla enheter i din IoT Hub. Till exempel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som har registrerats som en IoT Edge enhet har egenskaps **funktionerna. iotEdge** inställt på **True**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Hämta anslutnings strängen med Azure CLI

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub. Använd kommandot [AZ IoT Hub Device-Identity Connection-sträng show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) för att returnera anslutnings strängen för en enskild enhet:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>`connection-string show`Kommandot introducerades i version 0.9.8 av Azure IoT-tillägget och ersätter det inaktuella `show-connection-string` kommandot. Om du får ett fel när du kör det här kommandot kontrollerar du att tilläggs versionen har uppdaterats till 0.9.8 eller senare. Mer information och de senaste uppdateringarna finns [Microsoft Azure IoT-tillägg för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

Värdet för `device-id` parametern är Skift läges känsligt.

När du kopierar anslutnings strängen som ska användas på en enhet ska du inte inkludera citat tecknen runt anslutnings strängen.

---

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhet registrerad i IoT Hub är du redo att installera och konfigurera IoT Edge runtime på enheten.

* [Installera eller avinstallera Azure IoT Edge för Linux](how-to-install-iot-edge.md)
* [Installera eller avinstallera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md)