---
title: Ansluta en MXCHIP-AZ3166 till Azure IoT Central snabb start
description: Använd Azure återställnings tider Embedded-programvara för att ansluta en MXCHIP AZ3166-enhet till Azure IoT och skicka telemetri.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: dbe5ebaef7821299dc871adfd1000a999ff6b995
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639508"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Snabb start: ansluta en MXCHIP AZ3166-devkit till IoT Central

**Gäller för**: [inbäddad enhets utveckling](about-iot-develop.md#embedded-device-development)<br>
**Total slut för ande tid**: 30 minuter

[![Sökkod](media/common/browse-code-github.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

I den här självstudien använder du Azure-återställnings tider för att ansluta en MXCHIP AZ3166 IoT DevKit (hädanefter MXCHIP DevKit) till Azure IoT. Artikeln är en del av serien [Kom igång med utveckling av Azure IoT Embedded-enheter](quickstart-device-development.md). Serien introducerar enhets utvecklare i Azure återställnings tider och visar hur du ansluter flera enhets utvärderings paket till Azure IoT.

Du kommer att utföra följande uppgifter:

* Installera en uppsättning inbäddade utvecklingsverktyg för programmering av en MXCHIP-DevKit i C
* Bygg en bild och blinka den på MXCHIP-DevKit
* Använd Azure IoT Central för att skapa moln komponenter, Visa egenskaper, Visa telemetri för enheter och anropa direkta kommandon

## <a name="prerequisites"></a>Förutsättningar

* En dator som kör Microsoft Windows 10
* [Git](https://git-scm.com/downloads) för kloning av lagrings platsen
* Maskinvara

    > * [MXCHIP AZ3166 IoT DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2,4 GHz
    > * USB 2,0 A hane till Micro USB hane-kabel

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Om du vill konfigurera utvecklings miljön måste du först klona en GitHub-lagrings platsen som innehåller alla de till gångar du behöver för självstudien. Sedan installerar du en uppsättning programmerings verktyg.

### <a name="clone-the-repo-for-the-tutorial"></a>Klona lagrings platsen för självstudien

Klona följande lagrings platsen för att ladda ned all exempel enhets kod, installations skript och offline-versioner av dokumentationen. Om du tidigare har klonat den här lagrings platsen i en annan själv studie kurs behöver du inte göra det igen.

Kör följande kommando för att klona lagrings platsen:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installera verktygen

Den klonade lagrings platsen innehåller ett installations skript som installerar och konfigurerar de verktyg som krävs. Om du har installerat dessa verktyg i en annan självstudie i komma igång-guiden behöver du inte göra det igen.

> [!NOTE]
> Installations skriptet installerar följande verktyg:
> * [Cmake](https://cmake.org): build
> * [Arm-gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): kompilera
> * [Termite](https://www.compuphase.com/software_termite.htm): övervaka seriell ports utdata för anslutna enheter

Installera verktygen:

1. Från Utforskaren navigerar du till följande sökväg i lagrings platsen och kör installations skriptet med namnet *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Efter installationen öppnar du ett nytt konsol fönster för att identifiera konfigurations ändringarna som gjorts av installations skriptet. Använd den här konsolen för att slutföra de återstående programmerings aktiviteterna i självstudien. Du kan använda Windows CMD, PowerShell eller git bash för Windows.
1. Kör följande kod för att bekräfta att CMake version 3,14 eller senare är installerad.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Skapa moln komponenter

### <a name="create-the-iot-central-application"></a>Skapa IoT Central programmet

Det finns flera sätt att ansluta enheter till Azure IoT. I det här avsnittet får du lära dig hur du ansluter en enhet med hjälp av Azure IoT Central. IoT Central är en IoT-programplattform som minskar kostnaden och komplexiteten med att skapa och hantera IoT-lösningar.

Så här skapar du ett nytt program:
1. Från [Azure IoT Central-portalen](https://apps.azureiotcentral.com/)väljer du **Mina appar** på sidans navigerings meny.
1. Välj **+ nytt program**.
1. Välj **Anpassade program**.
1. Lägg till program namn och en URL.
1. Välj **kostnads fri** pris plan för att aktivera en 7-dagars utvärderings version.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Skapa en anpassad app i Azure IoT Central":::

1. Välj **Skapa**.

    När IoT Central etablerar programmet omdirigeras du automatiskt till den nya program instrument panelen.

    > [!NOTE]
    > Om du har ett befintligt IoT Central-program kan du använda det för att slutföra stegen i den här artikeln i stället för att skapa ett nytt program.

### <a name="create-a-new-device"></a>Skapa en ny enhet

I det här avsnittet ska du använda instrument panelen för IoT Central-program för att skapa en ny enhet. Du kommer att använda anslutnings informationen för den nyligen skapade enheten för att på ett säkert sätt ansluta den fysiska enheten i ett senare avsnitt.

Så här skapar du en enhet:
1. Från instrument panelen för program väljer du **enheter** på sidans navigerings meny.
1. Välj **+ nytt** för att öppna fönstret **skapa en ny enhet** .
1. Lämna enhets mal len som **otilldelad**.
1. Fyll i det önskade enhets namnet och enhets-ID: t.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Skapa en enhet i Azure IoT Central":::

1. Välj knappen **Skapa**.
1. Den nyligen skapade enheten visas i listan **alla enheter** .  Välj på enhets namnet för att visa information.
1. Välj **Anslut** i det övre högra meny fältet för att Visa anslutnings informationen som används för att konfigurera enheten i nästa avsnitt.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Visa information om enhets anslutning":::

1. Observera anslutnings värdena för följande anslutnings Strängs parametrar som visas i dialog rutan **Anslut** . Du lägger till dessa värden i en konfigurations fil i nästa steg:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Förbered enheten

Om du vill ansluta MXCHIP-DevKit till Azure ändrar du en konfigurations fil för Wi-Fi och Azure IoT-inställningar, återskapar avbildningen och Flash-avbildningen till enheten.

### <a name="add-configuration"></a>Lägga till konfiguration

1. Öppna följande fil i en text redigerare:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Ange Wi-Fi konstanter till följande värden från den lokala miljön.

    |Konstant namn|Värde|
    |-------------|-----|
    |`WIFI_SSID` |{*Din Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Ditt Wi-Fi lösen ord*}|
    |`WIFI_MODE` |{*Ett av värdena för uppräknade Wi-Fi lägen i filen*}|

1. Ange konstanter för Azure IoT Device information till de värden som du sparade efter att du har skapat Azure-resurser.

    |Konstant namn|Värde|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Värdet för ID-omfång*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Värdet för enhets-ID*}|
    |`IOT_DEVICE_SAS_KEY` |{*Värdet för primär nyckel*}|

1. Spara och stäng filen.

### <a name="build-the-image"></a>Skapa avbildningen

I konsolen eller i Utforskaren kör du skriptet *rebuild.bat* på följande sökväg för att skapa avbildningen:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

När bygget är klart kontrollerar du att den binära filen skapades på följande sökväg:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>Blinkar bilden

1. På MXCHIP-DevKit letar du reda på **återställnings** knappen och Micro USB-porten. Du använder dessa komponenter i följande steg. Båda är markerade i följande bild:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Hitta viktiga komponenter på MXChip devkit-tavlan":::

1. Anslut mikrousb-kabeln till Micro USB-porten på MXCHIP-DevKit och Anslut den sedan till din dator.
1. Leta upp den binära fil som du skapade i föregående avsnitt i Utforskaren.
1. Kopiera den binära filen *mxchip_azure_iot. bin*.
1. Leta upp den MXCHIP DevKit-enhet som är ansluten till datorn i Utforskaren. Enheten visas som en enhet i systemet med enhets etiketten **AZ3166**.
1. Klistra in den binära filen i rotmappen för MXCHIP-devkit. Blinkande startar automatiskt och slutförs om några sekunder.

    > [!NOTE]
    > Under den blinkande processen växlar en grön indikator på MXCHIP DevKit.

### <a name="confirm-device-connection-details"></a>Bekräfta information om enhets anslutning

Du kan använda **Termite** -appen för att övervaka kommunikationen och kontrol lera att enheten är korrekt konfigurerad.

1. Starta **Termite**.
    > [!TIP]
    > Om du inte kan ansluta Termite till din devkit installerar du den [Saint Link-drivrutinen](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) och försöker igen. Se  [fel sökning](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) för ytterligare steg.
1. Välj **inställningar**.
1. I dialog rutan **Inställningar för seriell port** kontrollerar du följande inställningar och uppdaterar vid behov:
    * **Baud-hastighet**: 115 200
    * **Port**: porten som din MXCHIP-DevKit är ansluten till. Om det finns flera port alternativ i list rutan kan du hitta rätt port att använda. Öppna Windows **Enhetshanteraren** och visa **portar** för att identifiera vilken port som ska användas.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Bekräfta inställningarna i Termite-appen":::

1. Välj OK.
1. Tryck på knappen **Återställ** på enheten. Knappen är märkt på enheten och finns nära USB-anslutningen.
1. I **Termite** -appen kontrollerar du följande kontroll punkts värden för att bekräfta att enheten har initierats och anslutits till Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Håll Termite öppen för att övervaka enhets utdata i följande steg.

## <a name="verify-the-device-status"></a>Verifiera enhetens status

Så här visar du enhetens status i IoT Central-portalen:
1. Från instrument panelen för program väljer du **enheter** på sidans navigerings meny.
1. Bekräfta att **enhetens status** har uppdaterats till **etablerad**.
1. Bekräfta att **enhets mal len** har uppdaterats till **komma igång guide**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Visa enhets status i IoT Central":::

## <a name="view-telemetry"></a>Visa telemetrin

Med IoT Central kan du Visa flödet av telemetri från enheten till molnet.

Så här visar du telemetri i IoT Central-portalen:

1. Från instrument panelen för program väljer du **enheter** på sidans navigerings meny.
1. Välj enheten i enhets listan.
1. Visa Telemetrin när enheten skickar meddelanden till molnet på fliken **Översikt** .

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Visa telemetri för enheter i IoT Central":::

    > [!NOTE]
    > Du kan också övervaka telemetri från enheten med Termite-appen.

## <a name="call-a-direct-method-on-the-device"></a>Anropa en direkt metod på enheten

Du kan också använda IoT Central för att anropa en direkt metod som du har implementerat på enheten. Direkta metoder har ett namn och kan också ha en JSON-nyttolast, konfigurerbar anslutning och tids gräns för metoden. I det här avsnittet anropar du en metod som gör att du kan aktivera eller inaktivera en lysdiod.

Anropa en metod i IoT Central Portal:

1. Välj fliken **kommando** på sidan enhet.
1. Välj **tillstånd** och välj **Kör**.  LYSDIODen bör vara tänd.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Anropa en direkt metod på en enhet":::

1. Avmarkera **tillstånd** och välj **Kör**. LAMPAn lampa bör stängas av.

## <a name="view-device-information"></a>Visa enhets information

Du kan visa enhets informationen från IoT Central.

Välj fliken **om** på sidan enhet.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Visa information om enheten i IoT Central":::

## <a name="debugging"></a>Felsökning

Information om hur du felsöker programmet finns i [fel sökning med Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver de Azure-resurser som skapas i den här självstudien kan du ta bort dem från IoT Central-portalen. Om du fortsätter till en annan självstudie i den här Komma igångs guiden kan du behålla de resurser du redan har skapat och återanvända dem.

Ta bort hela Azure IoT Central-exempelprogrammet och alla dess enheter och resurser:
1. Välj **Administration**  >  **ditt program**.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en anpassad avbildning som innehåller kod för Azure återställnings tider-exempel och sedan blinkar bilden till MXCHIP DevKit-enheten. Du använde också IoT Central Portal för att skapa Azure-resurser, ansluter MXCHIP-DevKit på ett säkert sätt till Azure, visar telemetri och skickar meddelanden.

* För enhets utvecklare är det föreslagna nästa steg att se de andra självstudierna i serien [komma igång med Azure IoT Embedded-enhets utveckling](quickstart-device-development.md).
* Om du har problem med att få enheten att initiera eller ansluta när du har följa stegen i den här hand boken, se [fel sökning](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Mer information om hur Azure återställnings tider-komponenter används i exempel koden för den här själv studie kursen finns i [använda Azure återställnings tider i komma igångs guiden](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Azure återställnings tider förser OEM-tillverkare med komponenter för att skydda kommunikationen och skapa kod-och data isolering med hjälp av underliggande MCU/MPU-funktioner för maskin varu skydd. Varje tillverkare ansvarar dock för att säkerställa att deras enhet uppfyller de säkerhets krav som utvecklas.

