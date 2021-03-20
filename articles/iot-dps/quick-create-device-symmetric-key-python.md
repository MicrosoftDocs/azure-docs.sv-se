---
title: Snabb start – Använd symmetrisk nyckel för att etablera en enhet till Azure IoT Hub med python
description: I den här snabb starten ska du använda Azure IoT python SDK för att etablera en symmetrisk nyckel enhet till en IoT-hubb med Azure-IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 8b87d9d487257504a438cf13a5b94e3ca4f9233d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92426373"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>Snabb start: etablera en python-enhet med symmetriska nycklar

I den här snabb starten får du lära dig hur du etablerar en Windows-utvecklings maskin som en enhet i en IoT-hubb med python. Den här enheten använder en symmetrisk nyckel och en enskild registrering för att autentisera med en tjänst för enhets etablerings tjänsten (DPS) för att kunna tilldelas en IoT-hubb. Exempel kod från [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python) kommer att användas för att etablera enheten. 

Även om den här artikeln visar etablering med en enskild registrering, kan du även använda registrerings grupper. Det finns vissa skillnader när du använder registrerings grupper. Du måste till exempel använda en härledd enhets nyckel med ett unikt registrerings-ID för enheten. Även om registreringsgrupper för symmetrisk nyckel inte är begränsade till äldre enheter finns det ett exempel för registreringsgrupper i avsnittet om [hur du etablerar äldre enheter med symmetrisk nyckelattestering](how-to-legacy-device-symm-key.md). Mer information finns i avsnittet om [gruppregistreringar för symmetrisk nyckelattestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten. 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Se till att du har [Python 3,7](https://www.python.org/downloads/) eller senare installerat på din Windows-baserade dator. Du kan kontrol lera din version av python genom att köra `python --version` .

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>Förbered python SDK-miljön 

1. Kontrollera att Git är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

2. Öppna en kommandotolk. Klona GitHub-lagrings platsen för Azure IoT python SDK:
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. Navigera till den `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` katalog där exempel filen _provision_symmetric_key. py_ finns.
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. Installera _Azure-IoT-Device-_ biblioteket genom att köra följande kommando.

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering

1. Logga in på [Azure Portal](https://portal.azure.com), Välj knappen **alla resurser** i den vänstra menyn och öppna DPS-instansen (Device Provisioning service).

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till individuell registrering** överst. 

3. Ange följande information på panelen **Lägg till registrering** och tryck på knappen **Spara** .

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings *mekanism*.

   - **Generera nycklar automatiskt**: Markera den här kryss rutan.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel **symm-Key-python-Device-008**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **python-Device-008**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. När du har sparat registreringen genereras **primär nyckeln** och den **sekundära nyckeln** och läggs till i registrerings posten. Den symmetriska nyckeln enhets registrering visas som **symm-Key-python-Device-008** under kolumnen *registrerings-ID* på fliken *enskilda* registreringar. 

5. Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Du kommer att använda det här nyckelvärdet och **registrerings-ID: t** senare när du lägger till miljövariabler som ska användas med enhets etableringens exempel kod.



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Förbered enhets etablerings koden

I det här avsnittet lägger du till följande fyra miljövariabler som ska användas som parametrar för enhetens etablerings exempel kod för den symmetriska nyckel enheten. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

Etablerings koden kommer att kontakta DPS-instansen baserat på dessa variabler för att autentisera din enhet. Enheten tilldelas sedan till en IoT-hubb som redan är länkad till DPS-instansen baserat på den enskilda registrerings konfigurationen. När den har skapats kommer exempel koden att skicka en test-telemetri till IoT Hub.

1. I [Azure Portal](https://portal.azure.com)på menyn enhets etablerings tjänst väljer du **Översikt** och kopierar _tjänstens slut punkt_ och _ID-omfång_. Du kommer att använda värdena för `PROVISIONING_HOST` `PROVISIONING_IDSCOPE` variablerna och.

    ![Tjänstinformation](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. I python-Kommandotolken lägger du till miljövariablerna med hjälp av de värden som du kopierade. 

    Följande kommandon är exempel för att Visa kommandosyntaxen. Se till att du använder rätt värden.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. I python-Kommandotolken lägger du till miljövariablerna för registrerings-ID: t och den symmetriska nyckeln som du kopierade från den enskilda registreringen i föregående avsnitt. 

    Följande kommandon är exempel för att Visa kommandosyntaxen. Se till att du använder rätt värden.

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. Kör python-exempel koden i _provision_symmetric_key. py_.

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. Förväntade utdata bör se ut ungefär så här som visar den länkade IoT-hubben som enheten tilldelats baserat på de enskilda registrerings inställningarna. Vissa exempel på lindnings hastighet för telemetri skickas också till hubben som ett test:

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. I Azure Portal navigerar du till IoT-hubben som är länkad till din etablerings tjänst och öppnar bladet **IoT-enheter** . När du har slutfört etableringen av den symmetriska nyckel enheten till hubben visas enhets-ID med *statusen* **aktive rad**. Du kan behöva klicka på knappen **Uppdatera** längst upp om du redan har öppnat bladet innan du kör koden för enhets exempel. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för enheten som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten etablerade du en Windows-baserad symmetrisk nyckel enhet till IoT Hub med hjälp av IoT Hub Device Provisioning Service. Om du vill lära dig hur du etablerar X. 509-certifikat enheter med python kan du fortsätta med snabb starten nedan för X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – etablera X. 509-enheter med hjälp av DPS och python](quick-create-simulated-device-x509-python.md)
