---
title: Registrera en ny enhet – Azure IoT Edge | Microsoft Docs
description: Registrera en enskild IoT Edge i IoT Hub för manuell etablering med antingen symmetriska nycklar eller X.509-certifikat
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481879"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Registrera en IoT Edge i IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Den här artikeln innehåller anvisningar för att registrera en IoT Edge enhet i IoT Hub.

Varje enhet som ansluter till en IoT Hub har ett enhets-ID som används för att spåra kommunikation från moln till enhet eller från enhet till moln. Du konfigurerar en enhet med dess anslutningsinformation, inklusive IoT-hubbens värdnamn, enhets-ID och den information som enheten använder för att autentisera IoT Hub.

Stegen i den här artikeln går igenom en process som kallas manuell etablering, där du ansluter en enskild enhet till dess IoT Hub. För manuell etablering har du två alternativ för att autentisera IoT Edge enheter:

* **Symmetrisk nyckel:** När du skapar en ny enhetsidentitet IoT Hub skapar tjänsten två nycklar. Du placerar en av nycklarna på enheten och den visar nyckeln som ska IoT Hub vid autentisering.

  Den här autentiseringsmetoden är snabbare att komma igång, men inte lika säker.

* **X.509-själv signerat:** Du skapar två X.509-identitetscertifikat och placerar dem på enheten. När du skapar en ny enhetsidentitet i IoT Hub anger du tumavtryck från båda certifikaten. När enheten autentiseras för IoT Hub visar den ett certifikat och IoT Hub verifierar att certifikatet matchar tumavtrycket.

  Den här autentiseringsmetoden är säkrare och rekommenderas för produktionsscenarier.

Den här artikeln beskriver båda autentiseringsmetoderna.

Om du har många enheter att konfigurera och inte vill etablera var och en manuellt kan du använda någon av följande artiklar för att lära dig hur IoT Edge fungerar med IoT Hub Device Provisioning Service:

* [Skapa och etablera IoT Edge enheter med X.509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera IoT Edge enheter med en TPM](how-to-auto-provision-simulated-device-linux.md)
* [Skapa och IoT Edge enheter med symmetriska nycklar](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

En kostnadsfri [IoT-hubb eller standardhubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* En kostnadsfri [IoT-hubb eller standardhubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* En kostnadsfri [IoT-hubb eller standardhubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Din Azure CLI-version måste minst vara 2.0.70 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Alternativ 1: Registrera med symmetriska nycklar

Du kan använda flera verktyg för att registrera en ny IoT Edge i IoT Hub och hämta dess anslutningssträng, beroende på vad du föredrar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I IoT-hubben i Azure Portal skapas IoT Edge och hanteras separat från IoT-enheter som inte är gränsaktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. I den vänstra rutan väljer **du IoT Edge** på menyn och väljer sedan Lägg till en **IoT Edge enhet.**

   ![Lägga till IoT Edge enhet från Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. På sidan **Skapa en** enhet anger du följande information:

   * Skapa ett beskrivande enhets-ID.
   * Välj **Symmetrisk nyckel** som autentiseringstyp.
   * Använd standardinställningarna för att automatiskt generera autentiseringsnycklar och ansluta den nya enheten till hubben.

1. Välj **Spara**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med din IoT-hubb. För att de här åtgärderna ska fungera måste du logga in på ditt Azure-konto och välja din hubb.

1. I Visual Studio Code öppnar du **utforskarvyn.**
1. Längst ned i Utforskaren expanderar du **Azure IoT Hub** avsnitt.

   ![Expandera Azure IoT Hub enheter](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klicka på **... i** Azure IoT Hub avsnittsrubriken.  Om du inte ser ellipsen klickar du på eller hovrar över rubriken.
1. Välj **Välj IoT Hub**.
1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.
1. Välj din Azure-prenumeration.
1. Välj din IoT-hubb.

### <a name="register-a-new-device-with-visual-studio-code"></a>Registrera en ny enhet med Visual Studio Code

1. I Visual Studio Code Explorer expanderar du **Azure IoT Hub** avsnitt.
1. Klicka på **... i** Azure IoT Hub avsnittsrubriken.  Om du inte ser ellipsen klickar du på eller hovrar över rubriken.
1. Välj **Skapa IoT Edge enhet**.
1. Ge enheten ett ID i textrutan som öppnas.

Resultatet av kommandot visas på utdataskärmen. Enhetsinformationen skrivs ut, inklusive **det deviceId** som du angav och **connectionString** som du kan använda för att ansluta den fysiska enheten till din IoT-hubb.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az iot hub device-identity create för](/cli/azure/iot/hub/device-identity) att skapa en ny enhetsidentitet i din IoT-hubb. Exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt i din IoT-hubb.
* `--hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : Deklarera att enheten är en IoT Edge enhet.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Nu när du har registrerat en enhet i IoT Hub hämtar du anslutningssträngen som du använder för att slutföra installationen och etableringen av IoT Edge-körningen. Följ stegen senare i den här artikeln för [att visa registrerade enheter och hämta anslutningssträngar](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Alternativ 2: Registrera med X.509-certifikat

Manuell etablering med X.509-certifikat kräver IoT Edge version 1.0.10 eller senare.

För X.509-certifikatautentisering tillhandahålls varje enhets autentiseringsinformation  i form av tumavtryck som tas från enhetsidentitetscertifikaten. Tumavtrycken ges till IoT Hub vid tidpunkten för enhetsregistreringen så att tjänsten kan identifiera enheten när den ansluter.

### <a name="create-certificates-and-thumbprints"></a>Skapa certifikat och tumavtryck

När du etablerar en IoT Edge-enhet med X.509-certifikat använder du det som kallas *enhetsidentitetscertifikat*. Det här certifikatet används endast för att etablera IoT Edge en enhet och autentisera enheten med Azure IoT Hub. Det är ett lövcertifikat som inte signerar andra certifikat. Enhetsidentitetscertifikatet är separat från certifikatutfärdarcertifikaten (CA) som IoT Edge visar för moduler eller underordnade enheter för verifiering. Mer information om hur CA-certifikaten används i IoT Edge enheter finns i [Förstå hur Azure IoT Edge använder certifikat.](iot-edge-certs.md)

Du behöver följande filer för manuell etablering med X.509:

* Två enhetsidentitetscertifikat med matchande privata nyckelcertifikat i .cer- eller .pem-format.

  En uppsättning certifikat-/nyckelfiler tillhandahålls till IoT Edge-körningen. När du skapar enhetsidentitetscertifikat anger du certifikatets eget namn (CN) med det enhets-ID som du vill att enheten ska ha i din IoT-hubb.

* Tumavtryck som tas från båda enhetsidentitetscertifikaten.

  Tumavtrycksvärden är 40 hexade tecken för SHA-1-hashvärden eller 64 hexade tecken för SHA-256-hashvärden. Båda tumavtrycken anges för IoT Hub vid tidpunkten för enhetsregistreringen.

Om du inte har några tillgängliga certifikat kan du skapa [democertifikat för att testa IoT Edge-enhetsfunktioner.](how-to-create-test-certificates.md) Följ anvisningarna i den artikeln för att konfigurera skript för att skapa certifikat, skapa ett rotcertifikatutfärdarcertifikat och skapa sedan två IoT Edge enhetsidentitetscertifikat.

Ett sätt att hämta tumavtrycket från ett certifikat är med följande openssl-kommando:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Registrera en ny enhet

Du kan använda flera verktyg för att registrera en ny IoT Edge i IoT Hub och ladda upp certifikattumavtrycken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

I IoT-hubben i Azure Portal skapas IoT Edge och hanteras separat från IoT-enheter som inte är gränsaktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. I den vänstra rutan väljer **du IoT Edge** på menyn och väljer sedan Lägg till en **IoT Edge enhet**.

   ![Lägga till IoT Edge en enhet från Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Ange **följande information på** sidan Skapa en enhet:

   * Skapa ett beskrivande enhets-ID. Anteckna detta enhets-ID eftersom du kommer att använda det i nästa avsnitt.
   * Välj **X.509 Self-Signed** som autentiseringstyp.
   * Ange tumavtryck för primärt och sekundärt identitetscertifikat. Tumavtrycksvärden är 40 hexade tecken för SHA-1-hashvärden eller 64 hexade tecken för SHA-256-hashvärden.

1. Välj **Spara**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Azure IoT-tillägget för Visual Studio Code stöder för närvarande inte enhetsregistrering med X.509-certifikat.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az iot hub device-identity create för](/cli/azure/iot/hub/device-identity) att skapa en ny enhetsidentitet i din IoT-hubb. Exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Det här kommandot innehåller flera parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt för din IoT-hubb. Anteckna detta enhets-ID eftersom du kommer att använda det i nästa avsnitt.
* `hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : Deklarera att enheten är en IoT Edge enhet.
* `--auth-method` eller `--am` : Deklarera auktoriseringstypen som enheten ska använda. I det här fallet använder vi tumavtryck för X.509-certifikat.
* `--primary-thumbprint` eller `--ptp` : Ange ett X.509-certifikattumavtryck som ska användas som primärnyckel.
* `--secondary-thumbprint` eller `--stp` : Ange ett tumavtryck för X.509-certifikatet som ska användas som sekundär nyckel.

---

Nu när du har registrerat en enhet i IoT Hub är du redo att installera och etablera IoT Edge på enheten. IoT Edge enheter som autentiseras med X.509-certifikat använder inte anslutningssträngar, så du kan fortsätta till nästa steg:

* [Installera eller avinstallera Azure IoT Edge för Linux](how-to-install-iot-edge.md)
* [Installera eller avinstallera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Visa registrerade enheter och hämta anslutningssträngar

Enheter som använder autentisering med symmetrisk nyckel behöver sina anslutningssträngar för att slutföra installationen och etableringen av IoT Edge-körningen.

Enheter som använder X.509-certifikatautentisering behöver inte anslutningssträngar. I stället behöver enheterna sitt IoT Hub-namn, enhetsnamn och certifikatfiler för att slutföra installationen och etableringen av IoT Edge-körningen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alla gränsaktiverade enheter som ansluter till din IoT-hubb visas på **IoT Edge** sidan.

![Använd Azure Portal för att visa alla IoT Edge enheter i din IoT-hubb](./media/how-to-register-device/portal-view-devices.png)

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar den fysiska enheten till dess identitet i IoT Hub.

Enheter som autentiseras med symmetriska nycklar har sina anslutningssträngar tillgängliga för kopiering i portalen.

1. På **IoT Edge** i portalen klickar du på enhets-ID:t i listan med IoT Edge enheter.
2. Kopiera värdet för antingen primär **anslutningssträng eller** **sekundär anslutningssträng.**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visa IoT Edge enheter med Visual Studio Code

Alla enheter som ansluter till din IoT-hubb visas **i Azure IoT Hub** i Visual Studio Code Explorer. IoT Edge enheter kan skiljas från icke-edge-enheter med en annan ikon och det faktum att **modulerna $edgeAgent** **och $edgeHub** distribueras till varje IoT Edge enhet.

![Använda VS Code för att visa IoT Edge enheter i din IoT-hubb](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Hämta anslutningssträngen med Visual Studio Code

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar den fysiska enheten till dess identitet i IoT Hub.

1. Högerklicka på ID:t för din enhet i **Azure IoT Hub** avsnitt.
1. Välj **Kopiera enhetens anslutningssträng.**

   Anslutningssträngen kopieras till Urklipp.

Du kan också välja **Hämta enhetsinformation** på snabbmenyn för att se all enhetsinformation, inklusive anslutningssträngen, i utdatafönstret.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge enheter med Azure CLI

Använd kommandot [az iot hub device-identity list för](/cli/azure/iot/hub/device-identity) att visa alla enheter i din IoT-hubb. Exempel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som är registrerade som IoT Edge enheter har egenskapen **capabilities.iotEdge** inställt på **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Hämta anslutningssträngen med Azure CLI

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar den fysiska enheten till dess identitet i IoT Hub. Använd kommandot [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) för att returnera anslutningssträngen för en enskild enhet:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>Kommandot `connection-string show` introducerades i version 0.9.8 av Azure IoT-tillägget och ersätter det inaktuella `show-connection-string` kommandot. Om du får ett fel när du kör det här kommandot kontrollerar du att tilläggsversionen har uppdaterats till 0.9.8 eller senare. Mer information och de senaste uppdateringarna finns i Microsoft Azure [IoT-tillägg för Azure CLI.](https://github.com/Azure/azure-iot-cli-extension)

Värdet för parametern `device-id` är fallkänsligt.

När du kopierar anslutningssträngen som ska användas på en enhet ska du inte inkludera citattecknen runt anslutningssträngen.

---

## <a name="next-steps"></a>Nästa steg

Nu när du har registrerat en enhet i IoT Hub är du redo att installera och etablera IoT Edge på enheten.

* [Installera eller avinstallera Azure IoT Edge för Linux](how-to-install-iot-edge.md)
* [Installera eller avinstallera Azure IoT Edge för Windows](how-to-install-iot-edge-windows-on-windows.md)