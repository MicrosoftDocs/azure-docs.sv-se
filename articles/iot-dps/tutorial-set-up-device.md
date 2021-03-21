---
title: Självstudie – konfigurera enheten för Azure-IoT Hub Device Provisioning Service
description: Den här självstudien visar hur du kan konfigurera enheten för att etablera via IoT Hub Device Provisioning Service (DPS) under processen för enhets tillverkning
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94959806"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Självstudie: Konfigurera en enhet för etablering med hjälp av Azure-IoT Hub Device Provisioning Service

I den tidigare självstudiekursen fick du lära dig att konfigurera Azure IoT Hub Device Provisioning-tjänsten för att automatiskt etablera dina enheter till din IoT-hubb. I den här kursen visar vi hur du konfigurerar din enhet under tillverkningsprocessen och gör så att den kan etableras automatiskt med IoT-hubben. Enheten etableras baserat på dess [attesteringsmetod](concepts-service.md#attestation-mechanism) vid den första starten och anslutningen till etableringstjänsten. Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Bygga ett plattformsspecifikt klient-SDK för enhetsetableringstjänster
> * Extrahera säkerhetsartefakterna
> * Skapa programvara för enhetsregistrering

Den här självstudien förutsätter att du redan har skapat din instans för enhetsetableringstjänsten och en IoT-hubb enligt instruktionerna i den tidigare självstudien [Konfigurera molnresurser](tutorial-set-up-cloud.md).

I den här kursen används [lagringsplatsen för Azure IoT SDK:er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c), som innehåller klient-SDK:t för enhetsetableringstjänsten för C. Detta SDK har för närvarande stöd för TPM och X.509 för enheter som körs på Windows- eller Ubuntu-implementeringar. Den här självstudien baseras på användning av en Windows-utvecklingsprogram, som även förutsätter grundläggande kunskaper i Visual Studio. 

Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten innan du fortsätter. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande förutsättningar gäller för en Windows-utvecklings miljö. För Linux eller macOS, se lämpligt avsnitt i [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med arbets belastningen ["Skriv bords utveckling med C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Skapa en plattformsspecifik version av SDK

Klient-SDK:t för enhetsetableringstjänsten hjälper dig att implementera din programvara för enhetsregistrering. Men innan du kan använda det måste du skapa en version av SDK:t som är specifik för plattformen för din utvecklingsklient och din attesteringsmetod. I den här självstudien skapar du ett SDK som använder Visual Studio på en Windows-utvecklings plattform för en typ av attestering som stöds:

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

5. Bygg SDK för din utvecklingsplattform baserat på de attesteringsmetoder som du kommer att använda. Använd något av följande kommandon (observera även de två efterföljande punkterna för varje kommando). När det är klart bygger CMake `/cmake`-underkatalogen med innehåll som är specifikt för din enhet:
 
    - För enheter som använder TPM-simulatorn för attestering:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - För alla andra enheter (fysisk TPM/HSM/X.509 eller ett simulerat X.509-certifikat):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Nu är du redo att använda SDK för att bygga din enhetsregistreringskod. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrahera säkerhetsartefakterna 

Nästa steg är att extrahera säkerhetsartefakterna för den attesteringsmetod som används av din enhet. 

### <a name="physical-devices"></a>Fysiska enheter 

Beroende på om du har byggt SDK för att använda attestering för en fysisk TPM/HSM eller med X.509-certifikat går insamling av säkerhetsartefakterna till så här:

- För en TPM-enhet måste du bestämma **bekräftelsenyckeln** som är associerad med enheten från TPM-kretstillverkaren. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att hasha bekräftelsenyckeln.  

- För en X.509-enhet måste du erhålla de certifikat som utfärdats till dina enheter. Etableringstjänsten visar två typer av registreringsposter som styr åtkomst för enheter med hjälp av X.509-attesteringsmetoden. Vilka certifikat som krävs beror på de registreringstyper som du kommer att använda.

    - Enskilda registreringar: registrering för en specifik enskild enhet. Den här typen av registreringspost kräver [certifikat för slutentitet, "leaf"](concepts-x509-attestation.md#end-entity-leaf-certificate).
    
    - Registreringsgrupper: Den här typen av registreringspost kräver mellanliggande certifikat eller rotcertifikat. Mer information finns på sidan om att [kontrollera enhetsåtkomst till etableringstjänsten med X.509-certifikat](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Simulerade enheter

Beroende på om du har byggt SDK för att använda attestering för en simulerad enhet som använder TPM eller X.509-certifikat går insamling av säkerhetsartefakterna till så här:

- För en simulerad TPM-enhet:

   1. Öppna en kommandotolk i Windows, navigera till `azure-iot-sdk-c`-underkatalogen och kör TPM-simulatorn. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. Den här simulatorn måste fortsätta att köras till slutet av den här snabbstarten. 

      Kör följande kommando från `azure-iot-sdk-c`-underkatalogen för att starta simulatorn:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Om du använder Git Bash-kommandotolken för det här steget behöver du ändra de omvända snedstrecken till vanliga snedstreck, till exempel: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. I Visual Studio öppnar du lösningen som genererats i *cmake*-mappen med namnet `azure_iot_sdks.sln`, och bygger den med kommandot "Build solution" (Bygg lösning) på menyn "Build" (Bygg).

   1. I rutan *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt). 

   1. Kör lösningen med något av startkommandona på felsökningsmenyn. I utdatafönstret visas TPM-simulatorns **_registrerings-ID_** och **_bekräftelsenyckeln_** som behövs för enhetsregistrering och registrering. Kopiera dessa värden för senare bruk. Du kan stänga det här fönstret (med registrerings-ID och bekräftelsenyckeln), men lämna TPM-simulatorfönstret från steg 1 öppet.

- För en simulerad X.509-enhet:

  1. I Visual Studio öppnar du lösningen som genererats i *cmake*-mappen med namnet `azure_iot_sdks.sln`, och bygger den med kommandot "Build solution" (Bygg lösning) på menyn "Build" (Bygg).

  1. I rutan *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **dice\_device\_enrollment** (dice-enhetsregistrering) och markera **Set as Startup Project** (Ange som startprojekt). 
  
  1. Kör lösningen med något av startkommandona på felsökningsmenyn. I utdatafönstret anger du **i** för individuell registrering när du blir uppmanad till det. I utdatafönstret visas ett lokalt genererat X.509-certifikat för din simulerade enhet. Kopiera utdata till Urklipp som börjar på *-----BEGIN CERTIFICATE-----* och slutar på den första *-----END CERTIFICATE-----*, och se till att du får med båda raderna. Du behöver bara det första certifikatet från utmatningsfönstret.
 
  1. Skapa en fil med namnet **_X509testcert.pem_**, öppna den i valfritt textredigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen. Du kommer att använda den senare för enhetsregistrering. När din registreringsprogramvara körs använder den samma certifikat vid automatisk etablering.    

Dessa säkerhetsuppgifter krävs vid registrering av enheten till enhetsetableringstjänsten. Etableringstjänsten väntar tills enheten har startats och ansluter till den vid en senare tidpunkt. När enheten startar för första gången interagerar logiken hos klient-SDK:t med din krets (eller simulator) för att extrahera säkerhetsartefakterna från enheten och verifierar registreringen med enhetsetableringstjänsten. 

## <a name="create-the-device-registration-software"></a>Skapa programvara för enhetsregistrering

Det sista steget är att skriva ett registreringsprogram som använder klient-SDK:t för enhetsetableringstjänsten för att registrera enheten med IoT Hub-tjänsten. 

> [!NOTE]
> För det här steget förutsätter vi att en simulerad enhet används genom att du kör ett SDK-exempelregistreringsprogram från din arbetsstation. Men samma koncept gäller om du skapar ett registreringsprogram för distribution till en fysisk enhet. 

1. Välj bladet **Översikt** i Azure Portal för enhetsetableringstjänsten och kopiera värdet för **_ID-omfång_**. *ID-omfång* genereras av tjänsten och garanterar unikhet. Det är oföränderligt och används för att unikt identifiera registrerings-ID:n.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. I Visual Studio *Solution Explorer* på datorn går du till mappen **Provision (Etablera)\_Exempel**. Välj exempelprojektet med namnet **prov\_dev\_client\_sample** och öppna källfilen **prov\_dev\_client\_sample.c**.

1. Tilldela värdet för _ID-omfång_ som erhölls i steg 1 till variabeln `id_scope` (tar bort vänster /`[` och höger/`]` hakparentes): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    `global_prov_uri`-variabeln gör att klientregistrerings-API:t för IoT-hubb `IoTHubClient_LL_CreateFromDeviceAuth` kan ansluta till den avsedda instansen för enhetsetableringstjänsten.

1. I **main()**-funktionen i samma fil kommenterar/avkommenterar du `hsm_type`-variabeln som matchar den attesteringsmetod som används av enhetens registreringsprogramvara (TPM eller X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Spara dina ändringar och bygg om exemplet **prov\_dev\_client\_sample** genom att välja "Build solution" (Bygg lösning) på menyn"Build" (Bygg). 

1. Högerklicka på projektet **prov\_dev\_client\_sample** i mappen **Provision\_Samples** och välj **Set as Startup Project** (Ange som startprojekt). Kör INTE exempelprogrammet ännu.

> [!IMPORTANT]
> Kör/starta inte enheten ännu! Du måste slutföra processen genom att registrera enheten med enhetsetableringstjänsten innan du startar enheten. I avsnittet Nästa steg nedan vägleds du till nästa artikel.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>SDK-API:er används under registreringen (endast som referens)

Som referens tillhandahåller SDK följande API:er som du kan använda i ditt program vid registrering. Med dessa API:er kan enheten ansluta till och registrera med enhetsetableringstjänsten när den startas. Som svar får enheten den information som krävs för att etablera en anslutning till din IoT-hubbinstans:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Du kanske också behöver förfina klientregistreringsprogrammet för enhetsetableringstjänsten först med hjälp av en simulerad enhet och en testtjänstinstallation. När ditt program fungerar i testmiljön kan du bygga det för din specifika enhet och kopiera den körbara filen till din enhetsavbildning. 

## <a name="clean-up-resources"></a>Rensa resurser

I det här läget kanske enhetsetablerings- och IoT-hubbtjänsterna körs i portalen. Om du vill avbryta enhetsetableringsinstallationen och/eller vänta med att slutföra den här kursserien rekommenderar vi att du stänger av dem för att undvika onödiga kostnader.

1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Klicka på **Ta bort** överst på bladet **Alla resurser**.  
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Klicka på **Ta bort** överst på bladet **Alla resurser**.  

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Bygga ett plattformsspecifikt klient-SDK för enhetsetableringstjänsten
> * Extrahera säkerhetsartefakterna
> * Skapa programvara för enhetsregistrering

Gå vidare till nästa kurs om du vill lära dig hur du etablerar enheten i din IoT-hubb genom att registrera den till Azure IoT Hub Device Provisioning-tjänsten för automatisk etablering.

> [!div class="nextstepaction"]
> [Etablera enheten till din IoT-hubb](tutorial-provision-device-to-hub.md)