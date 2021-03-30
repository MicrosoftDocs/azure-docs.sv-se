---
title: Snabb start – registrera TPM-enhet på Azure Device Provisioning-tjänsten med Java
description: Snabb start – registrera TPM-enhet till Azure IoT Hub Device Provisioning Service (DPS) med Java-tjänst-SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0a1f4ed46ab9e467a19cfa722a2d345284fdc94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96463053"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Snabb start: registrera TPM-enhet för att IoT Hub Device Provisioning Service med Java-tjänst-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

I den här snabb starten skapar du program mässigt en enskild registrering för en simulerad TPM-enhet i Azure IoT Hub Device Provisioning Service med hjälp av Java-tjänst-SDK med hjälp av ett Java-exempelprogram.

## <a name="prerequisites"></a>Förutsättningar

- Konfiguration av [IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md)slutförs.
- Avslut av [lästa kryptografiska nycklar från TPM-enheten](quick-create-simulated-device.md#simulatetpm).
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java se Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). I den här snabb starten installeras [Java service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) nedan. Det fungerar både i Windows och Linux. I den här snabb starten används Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) är installerat på datorn. 

2. Konfigurera miljövariabler för Java-installationen. Variabeln `PATH` ska innehålla den fullständiga sökvägen till katalogen *jdk1.8.x\bin*. Om det här är datorns första Java-installation skapar du en ny miljövariabeln med namnet `JAVA_HOME` och gör så att den pekar på den fullständiga sökvägen till katalogen *jdk1.8.x*. På Windows-datorer finns den här katalogen i mappen *C:\\Program\\Java\\* och du kan skapa eller redigera miljövariabler genom att söka efter **Redigera systemets miljövariabler** i **Kontrollpanelen** på Windows-datorn. 

   Du kan kontrollera om Java har konfigurerats på datorn genom att köra följande kommando i kommandofönstret:

    ```cmd\sh
    java -version
    ```

3. Ladda ned och extrahera [Maven 3](https://maven.apache.org/download.cgi) på datorn. 

4. Redigera miljövariabeln `PATH` så att den pekar på mappen *apache-maven-3.x.x\\bin* i mappen där Maven har extraherats. Du kan bekräfta att Maven har installerats genom att köra följande kommando i kommandofönstret:

    ```cmd\sh
    mvn --version
    ```

5. Kontrollera att [git](https://git-scm.com/download/) är installerad på datorn och har lagts till i miljövariabeln `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Ladda ned och ändra Java-exempelkoden

I det här avsnittet visas hur du lägger till etableringsinformationen för TPM-enheten i exempelkoden. 

1. Öppna en kommandotolk. Klona GitHub-lagrings platsen för enhets registrerings kod exempel med [Java-tjänst-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. I den nedladdade källkoden går du till exempelmappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Öppna filen **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** i valfritt redigeringsprogram och lägg till följande information:

   1. Lägg till `[Provisioning Connection String]` för etableringstjänsten från portalen enligt följande:
       1. Navigera till etablerings tjänsten i [Azure Portal](https://portal.azure.com). 
       2. Öppna **Policyer för delad åtkomst** och välj en princip som har behörigheten *EnrollmentWrite*.
       3. Kopiera **Anslutningssträng – primär nyckel**. 

           ![Hämta etableringsanslutningssträngen från portalen](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. I exempelkodfilen **_ServiceEnrollmentSample.java_** ersätter du `[Provisioning Connection String]` med **Anslutningssträng – primär nyckel**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Lägg till TPM-enhetsinformation:
       1. Hämta *registrerings-ID* och *TPM-bekräftelsenyckeln* för en TPM-enhetssimulering genom att följa stegen som leder till avsnittet [Simulera TPM-enhet](quick-create-simulated-device.md#simulatetpm).
       2. Använd **_registrerings-ID_** och **_bekräftelsenyckeln_** från utdata i det föregående steget för att ersätta `[RegistrationId]` och `[TPM Endorsement Key]` i exempelkodfilen **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Alternativt kan du konfigurera etableringstjänsten genom exempelkoden:
      - Om du vill lägga till konfigurationen i exemplet gör du följande:
        1. Navigera till IoT-hubben som är länkad till din etableringstjänst i [Azure Portal](https://portal.azure.com). Öppna fliken **Översikt** för hubben och kopiera **värdnamnet**. Tilldela **värdnamnet** till parametern *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Tilldela ett eget namn till parametern *DEVICE_ID* och behåll standardvärdet för *PROVISIONING_STATUS*, *ENABLED*. 
    
      - Eller: om du väljer att inte konfigurera etableringstjänsten kommentera ut eller ta bort följande instruktioner i filen _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Studera exempelkoden. Den skapar, uppdaterar, frågar och tar bort en enskild TPM-enhetsregistrering. Du kan verifiera att registreringen har lyckats i portalen genom att tillfälligt kommentera ut följande rader med kod i filen _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Spara filen _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Skapa och kör Java-exempelkoden

1. Öppna ett kommandofönster och navigera till mappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Skapa exempelkoden genom att använda följande kommando:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Det här kommandot laddar ned maven-paketet [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) till din dator. Det här paketet innehåller binärfilerna för [Java-tjänst-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/), som exempel koden måste bygga. 

3. Kör exemplet genom att använda de här kommandona i kommandofönstret:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Se utdatafönstret om registreringen har lyckats. 

5. Navigera till etableringstjänsten i Azure Portal. Välj **Hantera registreringar** och välj fliken **enskilda registreringar** . Observera att *registrerings-ID: t* för den SIMULERADE TPM-enheten visas nu. 

    ![Verifiera lyckad TPM-registrering i portalen](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att utforska Java-tjänsteexemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för Java-exemplet på datorn.
1. Stäng TPM-simulatorfönstret som du kan ha skapat för att simulera TPM-enheten.
1. Gå till enhets etablerings tjänsten i Azure Portal, Välj **Hantera registreringar** och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för registrerings posten som du skapade med den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du registrerat en simulerad TPM-enhet för enhets etablerings tjänsten. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)