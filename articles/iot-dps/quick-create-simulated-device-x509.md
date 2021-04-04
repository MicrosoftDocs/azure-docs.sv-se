---
title: Snabb start – etablera simulerad X. 509-enhet till Azure IoT Hub att använda C
description: Den här snabbstarten använder enskilda registreringar. I den här snabb starten skapar du och etablerar en simulerad X. 509-enhet med C-enhets-SDK för Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 8f4c35a61edc970f5d4d3beb0c4255ccfe4e336a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968153"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Snabbstart: Etablera en simulerad X.509-enhet med Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

I den här snabbstarten får lära dig att skapa och köra en X.509-enhetssimulator på en Windows-utvecklingsdator. Du konfigurerar denna simulerade enhet för att tilldelas till en IoT-hubb med hjälp av en registrering med en instans av enhetsetableringstjänst. Exempelkoden från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) används för att simulera en startsekvens för enheten. Enheten identifieras baserat på registrering med etableringstjänsten och tilldelas till IoT-hubben.

Om du inte är bekant med processen för autoetablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten. Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:

* [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
* [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande förutsättningar gäller för en Windows-utvecklings miljö. För Linux eller macOS, se lämpligt avsnitt i [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med arbets belastningen ["Skriv bords utveckling med C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet ska du förbereda en utvecklings miljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c), som innehåller exempel koden för startsekvensen X. 509.

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Hitta taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som `-b` parameter värde:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. I kodexemplet används ett X.509-certifikat för att tillhandahålla attestering via X.509-autentisering. Kör följande kommando för att skapa en version av SDK som är specifika för din utvecklings plattform som innehåller enhets etablerings klienten. En Visual Studio-lösning för den simulerade enheten genereras i `cmake` katalogen.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    När bygget lyckas ser de sista utmatnings linjerna ut ungefär som i följande utdata:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Skapa ett självsignerat X.509-enhetscertifikat

I det här avsnittet använder du ett självsignerat X.509-certifikat. Då måste du tänka på det här:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Du kommer att använda exempelkoden från Azure IoT C SDK för att skapa det certifikat som ska användas med posten för enskild registrering för den simulerade enheten.

1. Starta Visual Studio och öppna den nya lösningsfilen med namnet `azure_iot_sdks.sln`. Den här lösningsfilen finns i den `cmake`-mapp som du tidigare skapade i roten på azure-iot-sdk-c git-lagringsplatsen.

2. På menyn Visual Studio väljer du **bygge**  >  **build-lösning** för att bygga alla projekt i lösningen.

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **dice\_device\_enrollment** (dice-enhetsregistrering) och markera **Set as Startup Project** (Ange som startprojekt).

4. På Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra lösningen. I utdatafönstret anger du **i** för individuell registrering när du blir uppmanad till det.

    I utdatafönstret visas ett lokalt genererat, självsignerat X.509-certifikat för din simulerade enhet. Kopiera utdata till Urklipp som börjar på **-----BEGIN CERTIFICATE-----** och slutar på den första **-----END CERTIFICATE-----**, och se till att du får med båda raderna. Du behöver bara det första certifikatet från utdatafönstret.

5. Använd en textredigerare och spara certifikatet i en ny fil med namnet **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Skapa en post för enhetsregistrering i portalen

1. Logga in på Azure Portal, Välj knappen **alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till individuell registrering** överst.

3. I panelen **Lägg till registrering** anger du följande information och trycker sedan på knappen **Spara** .

    * **Mekanism:** Välj **X.509** som identitetsattesterings *mekanism*.
    * **Primär Certificate. pem-eller. cer-fil:** Välj **Välj en fil** för att välja certifikat filen X509testcert. pem som du skapade tidigare.
    * **Enhets-ID för IoT Hub:** ange **test-docs-cert device** för att ge enheten ett ID.

      [![Lägg till enskild registrering för X. 509-attestering i portalen](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Om registreringen har lyckats visas din X.509-enhet som **riot-device-cert** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulera första startsekvens för enheten

I det här avsnittet uppdaterar du exempelkoden för att skicka enhetens startsekvens till din instans av enhetsetableringstjänsten. Den här startsekvensen medför att enheten identifieras och tilldelas till en IoT-hubb som är länkad till instansen av enhetsetableringstjänsten.

1. I Azure Portal väljer du fliken **Översikt** för enhets etablerings tjänsten och noterar värdet för **_ID-omfång_** .

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

3. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` anges till `SECURE_DEVICE_TYPE_X509` i stället för `SECURE_DEVICE_TYPE_TPM` enligt nedan.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt).

6. På Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra lösningen. I prompten för att återskapa projektet väljer du **Ja** för att återskapa projektet innan det körs.

    Följande utdata är ett exempel på när etableringsenhetens klientprov lyckas med starten och ansluter till etableringstjänstinstansen för att hämta IoT-hubbinformation och registrera:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. I portalen navigerar du till IoT-hubben som är länkad till etablerings tjänsten och väljer fliken **IoT-enheter** . Vid lyckad etablering av den simulerade X. 509-enheten till hubben visas dess enhets-ID på bladet **IoT-enheter** med *status* **aktive rad**. Du kan behöva klicka på knappen **Uppdatera** längst upp. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för enheten som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en simulerad X. 509-enhet på din Windows-dator och etablerade den till IoT-hubben med hjälp av Azure-IoT Hub Device Provisioning Service på portalen. Om du vill lära dig hur du registrerar X. 509-enheten program mässigt fortsätter du till snabb starten för program mässig registrering av X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – registrera X. 509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)