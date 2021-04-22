---
title: 'Snabbstart – Etablera en simulerad X.509-enhet som ska Azure IoT Hub med C #'
description: Snabbstart – Skapa och etablera en X.509-enhet med C#-enhets-SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 7aca75d1abed5470d51de22f9285459381f684bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868599"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en X.509-enhet med C#-enhets-SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

De här stegen visar hur du använder enhetskod från [Azure IoT-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp) för C# för att etablera en X.509-enhet. I den här artikeln ska du köra enhetsexempelkod på utvecklingsdatorn för att ansluta till en IoT Hub med enhetsetableringstjänsten.

## <a name="prerequisites"></a>Förutsättningar

Om du inte är bekant med processen för automatisk etablering kan du läsa [etableringsöversikten.](about-iot-dps.md#provisioning-process) Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal) innan du fortsätter.

Azure IoT Device Provisioning Service stöder två typer av registreringar:
- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Öppna en kommandotolk eller Git Bash. Klona Azure IoT-exempel för C# GitHub-lagringsplatsen:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Kontrollera att du har [.NET Core 3.1 SDK eller senare](https://dotnet.microsoft.com/download) installerat på datorn. Du kan använda följande kommando för att kontrollera din version.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Skapa ett självsignerat X.509-enhetscertifikat

I det här avsnittet skapar du ett själv signerat X.509-testcertifikat `iothubx509device1` med som ämnesnamn. Det är viktigt att tänka på följande punkter:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.
* IoT-enhetens enhets-ID är certifikatets ämnesnamn. Se till att använda ett ämnesnamn som uppfyller kraven för [enhets-ID-sträng.](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)

Du använder exempelkod från [X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) för att skapa certifikatet som ska användas med posten för enskild registrering för enheten.


1. I en PowerShell-prompt ändrar du kataloger till projektkatalogen för X.509-enhetsetableringsexempel.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Exempelkoden konfigureras att använda X.509-certifikat som lagras i en lösenordsskyddad PKCS12-formaterad fil (certificate.pfx). Dessutom behöver du en certifikatfil med offentlig nyckel (certificate.cer) för att skapa en enskild registrering senare i den här snabbstarten. Generera det själv signerade certifikatet och dess associerade .cer- och .pfx-filer genom att köra följande kommando:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Med skriptet uppmanas du att ange ett PFX-lösenord. Kom ihåg det här lösenordet. Du måste använda det senare när du kör exemplet. Du kan köra `certutil` för att dumpa certifikatet och verifiera ämnesnamnet.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Skapa en enskild registreringspost för enheten


1. Logga in på Azure Portal, välj **knappen Alla** resurser på menyn till vänster och öppna etableringstjänsten.

2. På menyn Device Provisioning Service (Enhetsetableringstjänst) **väljer du Manage enrollments (Hantera registreringar).** Välj **fliken Enskilda registreringar** och välj **knappen Lägg till enskild** registrering längst upp. 

3. I panelen **Lägg till** registrering anger du följande information:
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under den *primära .pem- eller .cer-certifikatfilen* väljer du *Välj* en fil för att välja certifikatfilen **certificate.cer som** skapades i föregående steg.
   - Lämna **Enhets-ID** tomt. Enheten etableras med dess enhet-ID inställt på nätverksnamnet (CN) i X.509-certifikatet **iothubx509device1**. Det här gemensamma namnet är också det namn som används för registrerings-ID:t för den enskilda registreringsposten. 
   - Du kan även ange följande information:
       - Välj en IoT hub som är länkad till din etableringstjänst.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du **på knappen** Spara. 

     [![Lägga till enskild registrering för X.509-attestation i portalen](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Vid lyckad registrering visas X.509-registreringsposten som **iothubx509device1** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 



## <a name="provision-the-device"></a>Etablera enheten

1. På **bladet Översikt** för etableringstjänsten noterar du värdet **_för ID-omfång._**

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Skriv följande kommando för att skapa och köra X.509-enhetsetableringsexemplet. Ersätt `<IDScope>`-värdet med ID-omfånget för etableringstjänsten. 

    Certifikatfilen får som standard *./certificate.pfx* och frågar efter PFX-lösenordet.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Om du vill skicka allt som en parameter kan du använda följande exempelformat.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Enheten ansluter till DPS och tilldelas till en IoT Hub. Enheten skickar dessutom ett telemetrimeddelande till hubben.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Kontrollera att enheten har etablerats. Vid lyckad etablering av enheten till den IoT-hubb som är länkad till etableringstjänsten visas enhets-ID:t på hubbens **blad för IoT-enheter.** 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Förstå och använda enhetstvillingarna i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexempel ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. På den vänstra menyn i Azure Portal väljer du **Alla resurser** och sedan din Enhetsetableringstjänst. Längst upp på **bladet Översikt** trycker du **på Ta** bort överst i fönstret.  
1. På den vänstra menyn i Azure Portal väljer du **Alla resurser och** sedan din IoT-hubb. Längst upp på **bladet Översikt** trycker du **på Ta** bort överst i fönstret.  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten etablerade du en X.509-enhet till din IoT-hubb med hjälp Azure IoT Hub Device Provisioning-tjänsten. Om du vill lära dig hur du registrerar X.509-enheten programmatiskt fortsätter du till snabbstarten för programmatisk registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Azure-snabbstart – Registrera X.509-enheter Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-x509-csharp.md)
