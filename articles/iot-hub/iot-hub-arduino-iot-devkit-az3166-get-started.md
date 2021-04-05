---
title: Ansluta IoT DevKit-AZ3166 till en Azure-IoT Hub
description: I den här självstudien får du lära dig att konfigurera och ansluta IoT DevKit-AZ3166 till Azure IoT Hub så att den kan skicka data till Azures moln plattform.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97562964"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ansluta IoT DevKit-AZ3166 till Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda [MXChip IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/) för att utveckla och prototyp lösningar för Sakernas Internet (IoT) som drar nytta av Microsoft Azure-tjänster. Den innehåller en Arduino-kompatibel tavla med omfattande kring utrustning och sensorer, ett paket med öppen källkod och ett omfattande [exempel Galleri](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en IoT-hubb och registrerar en enhet för MXChip IoT-DevKit.
* Så här ansluter du IoT-DevKit för att Wi-Fi och konfigurera IoT Hub anslutnings strängen.
* Så här skickar du DevKit-sensorns telemetridata till IoT Hub.
* Förbereda utvecklings miljön och utveckla program för IoT-DevKit.

Har du inte någon DevKit än? Prova [DevKit-simulatorn](https://azure-samples.github.io/iot-devkit-web-simulator/) eller [Köp en DevKit](https://aka.ms/iot-devkit-purchase).

Du hittar käll koden för alla DevKit-självstudier från [galleriet kod exempel](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Detta behöver du

- Ett MXChip IoT DevKit-kort med en mikrousb-kabel. [Hämta nu](https://aka.ms/iot-devkit-purchase).
- En dator som kör Windows 10, macOS 10.10 + eller Ubuntu 18.04 +.
- En aktiv Azure-prenumeration. [Aktivera ett kostnads fritt 30-dagars utvärderings Microsoft Azure konto](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Förbered maskin varan

Koppla samman följande maskin vara till datorn:

* DevKit-tavlan
* Mikrousb-kabel

![Nödvändig maskin vara](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Följ dessa steg om du vill ansluta DevKit till datorn:

1. Anslut USB-kopplingen till datorn.

2. Anslut Micro USB-kopplingen till DevKit.

3. Den gröna INDIKATORn för ström bekräftar anslutningen.

   ![Maskin varu anslutningar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Snabb start: skicka telemetri från DevKit till en IoT Hub

Snabb starten använder förkompilerad DevKit-programvara för att skicka telemetri till IoT Hub. Innan du kör det skapar du en IoT-hubb och registrerar en enhet med hubben.

### <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **MyNodeDevice**: namnet på enheten som du registrerar. Använd **MyNodeDevice** såsom det visas. Om du väljer ett annat namn för enheten behöver du använda det namnet i hela artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Om du får ett fel som kör `device-identity` installerar du [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IoT-tillägget lägger till kommandon som är speciella för IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) till Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

### <a name="send-devkit-telemetry"></a>Skicka DevKit-telemetri

DevKit ansluter till en enhetsspecifika slut punkt på din IoT-hubb och skickar in temperatur-och fuktighets telemetri.

1. Ladda ned den senaste versionen av [GetStarted firmware](https://aka.ms/devkit/prod/getstarted/latest) för IoT DevKit.

1. Se till att IoT DevKit ansluter till din dator via USB. Öppna Utforskaren det finns en USB-enhet för Mass lagring som heter **AZ3166**.

    ![Öppna Utforskaren](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Dra och släpp den inbyggda program varan som bara laddats ned till Mass lagrings enheten och den kommer att blinka automatiskt.

    ![Kopiera inbyggd program vara](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. På DevKit, håller du ned knapp **B**, push och släpper knappen **Återställ** och släpper sedan knappen **b**. Din DevKit går in i AP-läge. För att bekräfta, visar skärmen tjänst uppsättnings identifieraren (SSID) för DevKit och IP-adressen för konfigurations portalen.

    ![Återställ knapp, knapp B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Ange AP-läge](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Använd en webbläsare på en annan Wi-Fi-aktiverad enhet (dator eller mobiltelefon) för att ansluta till det IoT DevKit SSID som visades i föregående steg. Lämna det tomt om det frågar efter ett lösen ord.

    ![Anslut SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Öppna **192.168.0.1** i webbläsaren. Välj den Wi-Fi som du vill att IoT-DevKit ska ansluta till, Skriv Wi-Fi lösen ordet och klistra sedan in enhets anslutnings strängen som du antecknade tidigare. Klicka sedan på spara.

    ![Konfigurations gränssnitt](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT-DevKit har bara stöd för 2,4 GHz-nätverk. Mer information finns i [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) .

1. Anslutnings strängen för WiFi-information och-enhet kommer att lagras i IoT-DevKit när du ser resultat sidan.

    ![Konfigurations resultat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > När Wi-Fi har kon figurer ATS sparas dina autentiseringsuppgifter på enheten för anslutningen, även om enheten är frånkopplad.

1. IoT DevKit startas om efter några sekunder. På DevKit-skärmen ser du IP-adressen för DevKit som följer efter telemetridata, inklusive temperatur-och fuktighets värde med antal meddelanden som skickas till Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Skickar data](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. För att verifiera telemetri-data som skickas till Azure kör du följande kommando i Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Följ de här stegen för att förbereda utvecklings miljön för DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installera Visual Studio Code med Azure IoT tools-tilläggs paketet

1. Installera [ARDUINO IDE](https://www.arduino.cc/en/Main/Software). Det ger den nödvändiga verktygskedjan för att kompilera och ladda upp Arduino-kod.
    * **Windows**: Använd Windows Installer version. Installera inte från App Store.
    * **MacOS**: Dra och släpp den extraherade **Arduino.app** i `/Applications` mappen.
    * **Ubuntu**: zippa upp den till en mapp som `$HOME/Downloads/arduino-1.8.8`

2. Installera [Visual Studio Code](https://code.visualstudio.com/), en plattforms kods redigerare mellan plattformar med kraftfulla IntelliSense, kod komplettering och fel söknings support, samt omfattande tillägg kan installeras från Marketplace.

3. Starta VS Code, Sök efter **Arduino** i tillägg Marketplace och installera det. Det här tillägget ger förbättrade upplevelser för att utveckla på Arduino-plattformen.

    ![Installera Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Leta efter [Azure IoT-verktyg](https://aka.ms/azure-iot-tools) på Marketplace för tillägg och installera det.

    ![Skärm bild som visar Azure IoT-verktyg på Marketplace för tillägg.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Eller kopiera och klistra in URL: en i ett webbläsarfönster: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Tillägget Azure IoT-verktyg innehåller [Azure IoT Device Workbench](https://aka.ms/iot-workbench) som används för att utveckla och felsöka på olika IoT devkit-enheter. [Azure IoT Hub-tillägget](https://aka.ms/iot-toolkit), som även ingår med Azure IoT Tools-tilläggspaketet, används för hantering av och interaktion med Azure IoT Hubs.

5. Konfigurera VS Code med Arduino-inställningar.

    I Visual Studio Code klickar du på **fil > inställningar > inställningar** (på MacOS, **kod > inställningar > inställningar**). Klicka sedan på ikonen **Öppna inställningar (JSON)** i det övre högra hörnet på sidan *Inställningar* .

    ![Installera Azure IoT-verktyg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Lägg till följande rader för att konfigurera Arduino utefter din plattform: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Ersätt platshållaren **{username}** nedan med ditt användarnamn.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klicka `F1` för att öppna paletten kommando, skriv och välj **Arduino: Board Manager**. Sök efter **AZ3166** och installera den senaste versionen.

    ![Installera DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installera ST-Link driv rutiner

[St-Link/v2](https://www.st.com/en/development-tools/st-link-v2.html) är det USB-gränssnitt som IoT DevKit använder för att kommunicera med din utvecklings dator. Du måste installera det i Windows för att kunna flasha den kompilerade enhetskoden till DevKit. Följ de OS-specifika stegen för att ge datorn åtkomst till din enhet.

* **Windows**: Hämta och installera USB-drivrutiner från [STMicroelectronics-webbplatsen](https://www.st.com/en/development-tools/stsw-link009.html).
* **MacOS**: ingen driv rutin krävs för MacOS.
* **Ubuntu**: Kör kommandona i terminalen, och logga ut och logga in så att gruppändringen börjar gälla:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu är allt klart med att förbereda och konfigurera utvecklings miljön. Låt oss bygga GetStarted-exemplet som du precis körde.

## <a name="build-your-first-project"></a>Bygg ditt första projekt

### <a name="open-sample-code-from-sample-gallery"></a>Öppna exempel kod från exempel galleriet

IoT-DevKit innehåller ett omfattande galleri med exempel som du kan använda för att lära dig att ansluta DevKit till olika Azure-tjänster.

1. Se till att din IoT-DevKit **inte är ansluten** till din dator. Starta VS Code först och Anslut sedan DevKit till datorn.

1. Klicka `F1` för att öppna paletten kommando, skriv och välj **Azure IoT Device Workbench: öppna exempel.**.. Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench-exempel hittar du **Kom igång** och klickar på **Öppna exempel**. Sedan väljer du standard Sök vägen för att ladda ned exempel koden.

    ![Öppna exempel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Etablera Azure-IoT Hub och-enhet

I stället för att tillhandahålla Azure-IoT Hub och-enhet från Azure Portal kan du göra det i VS-koden utan att lämna utvecklings miljön.

1. I fönstret nytt öppnat projekt klickar `F1` du för att öppna paletten kommando, skriver och väljer **Azure IoT Device Workbench: etablera Azure-tjänster...**. Följ steg för steg-guiden för att slutföra etableringen av Azure-IoT Hub och skapa IoT Hub-enheten.

    ![Etablera kommando](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Om du inte har loggat in på Azure. Följ popup-meddelandet för att logga in.

1. Ange den prenumeration som du vill använda.

    ![Markera under](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Välj eller skapa en ny [resurs grupp](../azure-resource-manager/management/overview.md#terminology).

    ![Välj resursgrupp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. I den resurs grupp som du har angett följer du guiden för att välja eller skapa en ny Azure-IoT Hub.

    ![Välj IoT Hub steg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Välj IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Valda IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. I fönstret Utdata visas Azure IoT Hub etablerad.

    ![IoT Hub etablerad](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Välj eller skapa en ny enhet i Azure IoT Hub som du har etablerad.

    ![Välj IoT-enhets steg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Välj IoT-enhet etablerad](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nu har du etablerat Azure IoT Hub och skapat en enhet i tjänsten. Dessutom sparas enhets anslutnings strängen i VS Code för att konfigurera IoT-DevKit senare.

    ![Etableringen utförs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurera och kompilera enhets kod

1. I det nedre högra statusfältet kontrollerar du att MXCHIP- **AZ3166** visas som vald tavla och seriell port med **STMicroelectronics** används.

    ![Välj tavla och COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klicka `F1` för att öppna paletten kommando, skriv och välj **Azure IoT enhet Workbench: Konfigurera enhets inställningar...** och välj sedan **Konfigurera anslutnings sträng för enhet > väljer IoT Hub enhets anslutnings sträng**.

1. På DevKit trycker du på **knappen a**, push och släpper knappen **Återställ** och släpper sedan **knappen A**. Din DevKit går in i konfigurations läge och sparar anslutnings strängen.

    ![Anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klicka på `F1` igen, skriv och välj **Azure IoT enhet Workbench: Ladda upp enhets kod**. Den börjar kompilera och ladda upp koden till DevKit.

    ![Arduino uppladdning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit startas om och börjar köra koden.

> [!NOTE]
> Om det uppstår fel eller avbrott kan du alltid återställa genom att köra kommandot igen.

## <a name="test-the-project"></a>Testa projektet

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visa telemetri som skickats till Azure IoT Hub

Klicka på Power plugg-ikonen i statusfältet för att öppna den seriella övervakaren:

![Seriell övervakare](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Exempel programmet körs utan problem när följande resultat visas:

* Den seriella övervakaren visar meddelandet som skickas till IoT Hub.
* LYSDIODen på MXChip IoT-DevKit blinkar.

![Utdata för seriell övervakare](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Du kan stöta på ett fel vid testning i vilken INDIKATORn inte blinkar, Azure Portal inte visar inkommande data från enheten, men enheten OLED-skärmen visas som **körs...**. Lös problemet genom att gå till enheten i IoT-hubben i Azure Portal och skicka ett meddelande till enheten. Om du ser följande svar i den seriella övervakaren i VS Code, är det möjligt att direkt kommunikation från enheten blockeras på router-nivå. Kontrol lera brand Väggs-och routergränssnitt som har kon figurer ATS för de anslutande enheterna. Se också till att den utgående porten 1833 är öppen.
> 
> FEL: mqtt_client. c (LN 454): fel: det gick inte att öppna anslutningen till slut punkten  
> INFORMATION:  >>>anslutnings status: frånkopplad  
> FEL: tlsio_mbedtls. c (LN 604): underliggande IO-öppning misslyckades  
> FEL: mqtt_client. c (LN 1042): fel: io_open misslyckades  
> FEL: iothubtransport_mqtt_common. c (LN 2283): det gick inte att ansluta till adressen atcsliothub.azure-devices.net.  
> INFO:  >>>att ansluta igen.  
> INFO: IoThub-version: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visa telemetri som tagits emot av Azure IoT Hub

Du kan använda [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för att övervaka enhets-till-moln-meddelanden (D2C) i IoT Hub.

1. Logga in [Azure Portal](https://portal.azure.com/), leta upp IoT Hub som du har skapat.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. I fönstret **principer för delad åtkomst** klickar du på **iothubowner-principen** och skriver ned anslutnings strängen för din IoT Hub.

    ![Anslutnings sträng för Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. I VS Code, klickar `F1` du på, skriver och väljer **Azure IoT Hub: Ange IoT Hub anslutnings sträng**. Kopiera anslutnings strängen till den.

    ![Ange anslutnings sträng för Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expandera fönstret **Azure IoT Hub-enheter** till vänster, högerklicka på enhets namnet som du skapade och välj **starta övervakning inbyggd händelse slut punkt**.

    ![Övervaka D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. I fönstret **utdata** kan du se inkommande D2C-meddelanden till IoT Hub.

    ![Skärm bild som visar inkommande D2C-meddelanden till IoT Hub.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Granska koden

`GetStarted.ino`Är huvud ritnings filen för Arduino.

![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Om du vill se hur enhetens telemetri skickas till Azure-IoT Hub öppnar du `utility.cpp` filen i samma mapp. Visa [API-referens](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) för att lära dig hur du använder sensorer och kring utrustning på IoT DevKit.

`DevKitMQTTClient`Används är en omslutning av **iothub_client** från [Microsoft Azure IoT SDK: er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) för att interagera med Azure-IoT Hub.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du söka efter en lösning i [IoT DEVKIT FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från [gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Du kan också ge oss feedback genom att lämna en kommentar på den här sidan.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en MXChip IoT-DevKit till din IoT-hubb och har skickat de fångade sensor data till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
