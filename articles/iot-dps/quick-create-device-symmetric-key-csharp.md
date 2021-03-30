---
title: 'Snabb start – Använd symmetrisk nyckel för att etablera en enhet till Azure IoT Hub med C #'
description: 'I den här snabb starten ska du använda C#-enhets-SDK: n för enhets etablerings tjänsten (DPS) för att etablera en symmetrisk nyckel enhet till en IoT-hubb'
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: f97840a05115bf5659a6f7579b72786e890051a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429395"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Snabb start: etablera en symmetrisk nyckel enhet med hjälp av C #

I den här snabb starten får du lära dig hur du etablerar en Windows-utvecklingsplattformen som en enhet till en IoT-hubb med C#. Den här enheten använder en symmetrisk nyckel och en enskild registrering för att autentisera med en tjänst för enhets etablerings tjänsten (DPS) för att kunna tilldelas en IoT-hubb. Exempel kod från [Azure IoT-exempel för C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) kommer att användas för att etablera enheten. 

Även om den här artikeln visar etablering med en enskild registrering, kan du även använda registrerings grupper. Det finns vissa skillnader när du använder registrerings grupper. Du måste till exempel använda en härledd enhets nyckel med ett unikt registrerings-ID för enheten. [Etablera enheter med symmetriska nycklar](how-to-legacy-device-symm-key.md) ger ett exempel på en registrerings grupp. Mer information om registrerings grupper finns i [grupp registreringar för symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten. 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Kontrol lera att du har [.net Core 2,1 SDK](https://www.microsoft.com/net/download/windows) eller senare installerad på en Windows-baserad dator.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering

1. Logga in på [Azure Portal](https://portal.azure.com), Välj knappen **alla resurser** i den vänstra menyn och öppna DPS-instansen (Device Provisioning service).

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till individuell registrering** överst. 

3. Ange följande information på panelen **Lägg till registrering** och tryck på knappen **Spara** .

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings *mekanism*.

   - **Generera nycklar automatiskt**: Markera den här kryss rutan.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel **symm-Key-csharp-Device-01**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **csharp-Device-01**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. När du har sparat registreringen genereras **primär nyckeln** och den **sekundära nyckeln** och läggs till i registrerings posten. Den symmetriska nyckeln enhets registrering visas som **symm-Key-csharp-Device-01** under kolumnen *registrerings-ID* på fliken *enskilda* registreringar. 

5. Öppna registreringen och kopiera värdet för den genererade **primär nyckeln** och **sekundär nyckeln**. Du kommer att använda det här nyckelvärdet och **registrerings-ID: t** senare när du lägger till miljövariabler som ska användas med enhets etableringens exempel kod.



## <a name="prepare-the-c-environment"></a>Förbereda C#-miljön 

1. Öppna en git CMD-eller git-bash kommando rads miljö. Klona [Azure IoT-exemplen för C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen med följande kommando:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Förbered enhets etablerings koden

I det här avsnittet lägger du till följande fyra miljövariabler som ska användas som parametrar för enhetens etablerings exempel kod för att etablera den symmetriska nyckel enheten. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Etablerings koden kommer att kontakta DPS-instansen baserat på dessa variabler för att autentisera din enhet. Enheten tilldelas sedan till en IoT-hubb som redan är länkad till DPS-instansen baserat på den enskilda registrerings konfigurationen. När den har skapats kommer exempel koden att skicka en test-telemetri till IoT Hub.

1. I [Azure Portal](https://portal.azure.com)på menyn enhets etablerings tjänst väljer du **Översikt** och kopierar _tjänstens slut punkt_ och _ID-omfång_. Du kommer att använda värdena för `PROVISIONING_HOST` `DPS_IDSCOPE` variablerna och.

    ![Tjänstinformation](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Öppna en kommando tolk och navigera till *SymmetricKeySample* i lagrings platsen för klonade prover:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. I mappen *SymmetricKeySample* öppnar du *program. cs* i en text redigerare och söker efter de kodrader som har angett- `individualEnrollmentPrimaryKey` och- `individualEnrollmentSecondaryKey` strängarna. Uppdatera de här kod raderna enligt följande så att miljövariablerna används i stället för hård kodning av nycklarna.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "";
        //private const string individualEnrollmentSecondaryKey = "";

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable("PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Du hittar också den kodrad som anger `registrationId` strängen och uppdaterar den enligt följande för att även använda en miljö variabel enligt följande:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Spara ändringarna i *program. cs*.

3. I kommando tolken lägger du till miljövariablerna för ID-omfånget, registrerings-ID, primär och sekundära symmetriska nycklar som du kopierade från den enskilda registreringen i föregående avsnitt.  

    Följande kommandon är exempel för att Visa kommandosyntaxen. Se till att du använder rätt värden.

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. Skapa och kör exempel koden med hjälp av följande kommando.

    ```console
    dotnet run
    ```

5. Förväntade utdata bör se ut ungefär så här som visar den länkade IoT-hubben som enheten tilldelats baserat på de enskilda registrerings inställningarna. Ett exempel på en "TestMessage"-sträng skickas till hubben som ett test:

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. I Azure Portal navigerar du till IoT-hubben som är länkad till din etablerings tjänst och öppnar bladet **IoT-enheter** . När du har slutfört etableringen av den symmetriska nyckel enheten till hubben visas enhets-ID med *statusen* **aktive rad**. Du kan behöva klicka på knappen **Uppdatera** längst upp om du redan har öppnat bladet innan du kör koden för enhets exempel. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för enheten som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten etablerade du en Windows-baserad symmetrisk nyckel enhet till IoT Hub med hjälp av IoT Hub Device Provisioning Service. Om du vill lära dig hur du etablerar X. 509-certifikat enheter med C# fortsätter du med snabb starten nedan för X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – etablera X. 509-enheter med DPS och C #](quick-create-simulated-device-x509-csharp.md)
