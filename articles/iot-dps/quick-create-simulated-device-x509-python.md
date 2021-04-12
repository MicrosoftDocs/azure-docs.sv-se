---
title: Snabb start – etablera en simulerad X. 509-enhet till Azure IoT Hub med python
description: Snabb start – skapa och etablera en simulerad X. 509-enhet med python-enhets-SDK för IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 5db02f8ca1f0c311617a787525ee2fa5856eb5dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045271"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabb start: skapa och etablera en simulerad X. 509-enhet med python-enhets-SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

I den här snabb starten etablerar du en utvecklings dator som en python X. 509-enhet. Du kan använda exempel enhets kod från [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python) för att ansluta enheten till din IoT-hubb. En enskild registrering används med enhets etablerings tjänsten (DPS) i det här exemplet.

## <a name="prerequisites"></a>Förutsättningar

- Bekant med [etablerings](about-iot-dps.md#provisioning-process) koncept.
- Slut för ande av [konfigurations IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md).
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3,6 eller senare](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

2. Öppna en git bash-prompt. Klona GitHub-lagrings platsen för [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Skapa ett självsignerat X.509-enhetscertifikat 

I det här avsnittet ska du skapa ett självsignerat X. 509-certifikat. Det är viktigt att tänka på följande punkter:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Om du inte redan har enhets certifikat för att autentisera en enhet kan du skapa ett självsignerat certifikat med OpenSSL för testning med den här artikeln.  OpenSSL ingår i git-installationen. 

1. Kör följande kommando i git-bash-prompten.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > Det extra snedstreck som anges för ämnes namnet ( `//CN=Python-device-01` ) krävs bara för att undanta strängen med git på Windows-plattformar. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. När du uppmanas att **Ange PEM-pass frasen:** Använd pass frasen `1234` för testning med den här artikeln.    

3. När du uppmanas **att bekräfta genom att ange PEM pass fras:** använder du pass frasen `1234` igen.    

En test certifikat fil (*python-Device. pem*) och den privata nyckel filen (*python-Device. Key. pem*) skapas i katalogen där du körde `openssl` kommandot.


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Skapa en enskild registrerings post i DPS


Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar en enskild registrering för en enskild enhet som ska tillhandahållas med en IoT-hubb.

1. Logga in på Azure Portal, Välj knappen **alla resurser** i den vänstra menyn och öppna etablerings tjänsten.

2. Från menyn enhets etablerings tjänst väljer du **Hantera registreringar**. Välj fliken **enskilda registreringar** och välj knappen **Lägg till individuell registrering** överst. 

3. Ange följande information på panelen **Lägg till registrering** :
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under *filen Primary Certificate. pem eller. cer* väljer du *Välj en fil* för att välja certifikat filen **python-Device. pem** om du använder test certifikatet som skapades tidigare.
   - Du kan även ange följande information:
     - Välj en IoT hub som är länkad till din etableringstjänst.
     - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du på knappen **Spara** . 

     [![Lägg till enskild registrering för X. 509-attestering i portalen](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Vid lyckad registrering visas din X. 509-enhet som **python-Device-01** under kolumnen *registrerings-ID* på fliken *enskilda registreringar* . Detta registrerings värde kommer från ämnes namnet för enhets certifikatet. 

## <a name="simulate-the-device"></a>Simulera enheten

I python-etablerings exemplet [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) finns i `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` katalogen. I det här exemplet används sex miljövariabler för att autentisera och etablera en IoT-enhet med hjälp av DPS. Följande miljövariabler är:

| Variabelnamn              | Beskrivning                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Det här värdet är den globala slut punkt som används för att ansluta till din DPS-resurs |    
| `PROVISIONING_IDSCOPE`     |  Det här värdet är ID-omfånget för din DPS-resurs |    
| `DPS_X509_REGISTRATION_ID` |  Det här värdet är ID: t för enheten. Det måste också matcha ämnes namnet för enhets certifikatet |    
| `X509_CERT_FILE`           |  Enhetens certifikat fil namn |    
| `X509_KEY_FILE`            |  Namnet på den privata nyckeln för ditt enhets certifikat |
| `PASS_PHRASE`              |  Den pass fras som du använde för att kryptera certifikatet och filen med den privata nyckeln ( `1234` ). |    

1. Från menyn enhets etablerings tjänst väljer du **Översikt**. Anteckna _ID-omfattning_ och _Global enhets slut punkt_.

    ![Tjänstinformation](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. I din git bash-prompt använder du följande kommandon för att lägga till miljövariablerna för den globala enhetens slut punkt och ID-omfång.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. Registrerings-ID: t för IoT-enheten måste matcha ämnes namnet i enhetens certifikat. Om du har genererat ett självsignerat test certifikat `Python-device-01` är enhetens ämnes namn och registrerings-ID. 

    Om du redan har ett enhets certifikat kan du använda `certutil` för att verifiera ämnes namnet som används för enheten enligt nedan för ett självsignerat test certifikat:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    I git bash-prompten anger du miljövariabeln för registrerings-ID: t enligt följande:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. I git bash-prompten anger du miljövariabler för certifikat filen, den privata nyckel filen och pass frasen.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Granska koden för [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) om du inte använder **python version 3,7** eller senare, gör du [kod ändringen som anges här](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) för att ersätta `asyncio.run(main())` och spara ändringen. 

6. Kör exemplet. Exemplet ansluter, etablerar enheten till en hubb och skickar test meddelanden till hubben.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. I portalen navigerar du till IoT-hubben som är länkad till din etablerings tjänst och öppnar bladet **IoT-enheter** som finns under avsnittet **Explorer** på den vänstra menyn. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **Device Explorer** med *STATUS***aktiverad**. Du kan behöva klicka på knappen **Uppdatera** längst upp om du redan har öppnat bladet innan du kör programmet för enhets exempel. 

    ![Enheten är registrerad på IoT-hubben](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
2. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna bladet **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
3. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna bladet **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för den enhet som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en simulerad X. 509-enhet på utvecklings datorn och etablerad den till IoT-hubben med hjälp av Azure-IoT Hub Device Provisioning Service på portalen. Om du vill lära dig hur du registrerar X. 509-enheten program mässigt fortsätter du till snabb starten för program mässig registrering av X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – registrera X. 509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
