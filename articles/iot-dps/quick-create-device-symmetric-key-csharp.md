---
title: 'Snabbstart – Använda symmetrisk nyckel för att etablera en enhet till Azure IoT Hub med C #'
description: I den här snabbstarten ska du använda C#-enhets-SDK för Device Provisioning Service (DPS) för att etablera en symmetrisk nyckelenhet till en IoT-hubb
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: a38d58eecd200ec312e7677a05531e27bf4ba6b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868707"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Snabbstart: Etablera en enhet med symmetrisk nyckel med C #

I den här snabbstarten lär du dig att etablera en Windows-utvecklingsdator som en enhet till en IoT Hub med C#. Den här enheten använder en symmetrisk nyckel och en enskild registrering för att autentisera med en DPS-instans (Device Provisioning Service) för att kunna tilldelas till en IoT-hubb. Exempelkod från [Azure IoT-exempel för C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) används för att etablera enheten. 

Även om den här artikeln visar etablering med en enskild registrering kan du också använda registreringsgrupper. Det finns några skillnader när du använder registreringsgrupper. Du måste till exempel använda en härledd enhetsnyckel med ett unikt registrerings-ID för enheten. [Etablering av enheter med symmetriska nycklar](how-to-legacy-device-symm-key.md) är ett exempel på en registreringsgrupp. Mer information om registreringsgrupper finns i [Gruppregistreringar för symmetrisk nyckel attestation.](concepts-symmetric-key-attestation.md#group-enrollments)

Om du inte är bekant med processen för automatisk etablering kan du läsa [etableringsöversikten.](about-iot-dps.md#provisioning-process) 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns [i Etablera för flera innehavare.](how-to-provision-multitenant.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Kontrollera att du har [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) eller senare installerat på din Windows-baserade dator.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Skapa en enhetsregistrering

1. Logga in på [Azure Portal](https://portal.azure.com), välj **knappen Alla** resurser på menyn till vänster och öppna DPS-instansen (Device Provisioning Service).

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till enskild** registrering högst upp. 

3. I panelen **Lägg till** registrering anger du följande information och trycker på **knappen** Spara.

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings *mekanism*.

   - **Generera nycklar automatiskt: Markera** den här kryssrutan.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel **symm-key-csharp-device-01**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **csharp-device-01**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. När du har sparat registreringen genereras **primärnyckeln och** den **sekundära** nyckeln och läggs till i registreringsposten. Din enhetsregistrering med symmetrisk nyckel visas **som symm-key-csharp-device-01** under kolumnen Registrerings-ID *på fliken Enskilda registreringar.*  

5. Öppna registreringen och kopiera värdet för den genererade **primärnyckeln och** den **sekundära nyckeln**. Du kommer att använda det här nyckelvärdet och **registrerings-ID:t** senare när du lägger till miljövariabler för användning med exempelkoden för enhetsetablering.



## <a name="prepare-the-c-environment"></a>Förbereda C#-miljön 

1. Öppna en Git CMD- eller Git Bash-kommandoradsmiljö. Klona [Azure IoT-exempel för C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen med följande kommando:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Förbereda enhetsetableringskoden

I det här avsnittet lägger du till följande fyra miljövariabler som ska användas som parametrar för enhetsetableringsexempelkoden för att etablera din symmetriska nyckelenhet. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Etableringskoden kontaktar DPS-instansen baserat på dessa variabler för att autentisera din enhet. Enheten tilldelas sedan till en IoT-hubb som redan är länkad till DPS-instansen baserat på den enskilda registreringskonfigurationen. När exempelkoden har etablerats skickar den viss testtelemetri till IoT-hubben.

1. I Azure Portal [väljer](https://portal.azure.com)du Översikt på menyn Device Provisioning Service (Enhetsetableringstjänst) **och** kopierar _tjänstslutpunkten och_ _ID-omfånget_. Du kommer att använda dessa värden för `PROVISIONING_HOST` `DPS_IDSCOPE` miljövariablerna och .

    ![Tjänstinformation](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Öppna en kommandotolk och navigera till *SymmetricKeySample på lagringsplatsen* för klonade exempel:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. I mappen *SymmetricKeySample* öppnar du *Program.cs* i en textredigerare och hittar de rader med kod som anger `individualEnrollmentPrimaryKey` `individualEnrollmentSecondaryKey` strängarna och . Uppdatera dessa kodrader på följande sätt så att miljövariablerna används i stället för att hårdkoda nycklarna.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "&quot;;
        //private const string individualEnrollmentSecondaryKey = &quot;&quot;;

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable(&quot;PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Leta också reda på kodraden som anger strängen och uppdatera den på `registrationId` följande sätt så att du även använder en miljövariabel på följande sätt:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    Spara ändringarna i *Program.cs*.

3. I kommandotolken lägger du till miljövariablerna för ID-omfång, registrerings-ID, primära och sekundära symmetriska nycklar som du kopierade från den enskilda registreringen i föregående avsnitt.  

    Följande kommandon är exempel på kommandosyntax. Se till att använda rätt värden.

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


4. Skapa och kör exempelkoden med följande kommando.

    ```console
    dotnet run
    ```

5. Förväntade utdata bör se ut ungefär så här, vilket visar den länkade IoT-hubb som enheten har tilldelats till baserat på de enskilda registreringsinställningarna. Ett exempel på en "TestMessage"-sträng skickas till hubben som ett test:

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
    
6. I den Azure Portal navigerar du till den IoT-hubb som är länkad till etableringstjänsten och öppnar **bladet IoT-enheter.** När den symmetriska nyckeln har etablerats på hubben visas enhets-ID:t med *STATUS* som **aktiverat.** Du kan behöva trycka på **knappen** Uppdatera längst upp om du redan har öppnat bladet innan du kör enhetens exempelkod. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexempel ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som har skapats i den här snabbstarten.

1. På den vänstra menyn i Azure Portal väljer du **Alla resurser** och sedan din enhetsetableringstjänst. Öppna **Hantera registreringar** för din tjänst och välj sedan **fliken Enskilda registreringar.** Markera kryssrutan bredvid REGISTRERINGS-ID *för* den enhet som du har registrerat  i den här snabbstarten och tryck på knappen Ta bort längst upp i fönstret. 
1. På den vänstra menyn i den Azure Portal väljer du **Alla resurser** och sedan din IoT-hubb. Öppna **IoT-enheter** för din hubb, markera kryssrutan bredvid *ENHETS-ID* för den enhet som du registrerade i den här snabbstarten och tryck sedan på knappen **Ta** bort längst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten etablerade du en Windows-baserad symmetrisk nyckelenhet till din IoT-hubb med hjälp av IoT Hub Device Provisioning Service. Om du vill lära dig hur du etablerar X.509-certifikatenheter med C# fortsätter du med snabbstarten nedan för X.509-enheter. 

> [!div class="nextstepaction"]
> [Azure-snabbstart – Etablera X.509-enheter med DPS och C #](quick-create-simulated-device-x509-csharp.md)
