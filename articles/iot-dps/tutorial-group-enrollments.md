---
title: Självstudie – etablera simulerad X. 509-enhet till Azure IoT Hub med hjälp av Java-och registrerings grupper
description: I den här självstudien skapar och etablerar du en simulerad X. 509-enhet med Java-enhets-och tjänst-SDK och registrerings grupper för IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4cfbfe3e3e3ba620d8292767012c9bb866d8a878
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968102"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Självstudie: skapa och etablera en simulerad X. 509-enhet med Java-enhets-och tjänst-SDK och grupp registreringar för IoT Hub Device Provisioning Service

Dessa steg visar hur du simulerar en X.509-enhet på utvecklingsdatorn som kör Windows OS och använder kodexemplet för att ansluta till denna simulerade enhet med Device Provisioning-tjänsten och IoT-hubben med registreringsgrupper. 

Se till att slutföra stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen](./quick-setup-auto-provision.md) innan du fortsätter.


## <a name="prerequisites"></a>Förutsättningar

1. Kontrollera att [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) är installerat på datorn.

1. Ladda ned och installera [Maven](https://maven.apache.org/install.html).

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Använd följande [certifikatöversikt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) för att skapa dina testcertifikat.

    > [!NOTE]
    > Det här steget kräver [OpenSSL](https://www.openssl.org/), som antingen kan byggas och installeras från källkoden eller laddas ned och installeras från en [tredje part](https://wiki.openssl.org/index.php/Binaries), till exempel [den här](https://sourceforge.net/projects/openssl/). Om du redan har skapat dina _rot-_, _mellanliggande_ och _enhets_-certifikat kan du hoppa över det här steget.
    >

    1. Gå igenom de två första stegen för att skapa dina _rot-_ och _mellanliggande_ certifikat.

    1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna din distributionstjänst.

        1. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Certifikat** och klickar på knappen **Lägg till** högst upp.

        1. Under **Lägg till certifikat** anger du följande information:
            - Ange ett unikt certifikatnamn.
            - Välj filen **_RootCA.pem_** som du skapade.
            - Klicka på knappen **Spara** när det är klart.

           ![Lägga till certifikat](./media/tutorial-group-enrollments/add-certificate.png)

        1. Välj det nya certifikatet:
            - Klicka på **Generera en verifieringskod**. Kopiera den skapade koden.
            - Kör verifieringsstegen. Ange _verifieringskoden_ eller högerklicka för att klistra in PowerShell-fönstret som körs.   Tryck på **Retur**.
            - Välj den nyligen skapade filen **_verifyCert4.pem_** i Azure-portalen. Klicka på **Verifiera**.

              ![Validera certifikat](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Avsluta genom att köra stegen för att skapa dina enhetscertifikat och rensa resurser.

       > [!NOTE]
       > När du skapar enhetscertifikat ska du se till att endast använda gemena alfanumeriska tecken och bindestreck i enhetens namn.
       >


## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för att hämta Java SDK-exempelkoden:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. I den nedladdade källkoden går du till exempelmappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Öppna filen **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** i valfritt redigeringsprogram och lägg till följande information:

    1. Lägg till `[Provisioning Connection String]` för etableringstjänsten från portalen enligt följande:

        1. Navigera till etablerings tjänsten i [Azure Portal](https://portal.azure.com).

        1. Öppna **Policyer för delad åtkomst** och välj en princip som har behörigheten *EnrollmentWrite*.

        1. Kopiera **Anslutningssträng – primär nyckel**.

            ![Hämta etableringsanslutningssträngen från portalen](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. I exempelkodfilen **_ServiceEnrollmentGroupSample.java_** ersätter du `[Provisioning Connection String]` med **Anslutningssträng – primär nyckel**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Öppna filen med det mellanliggande signeringscertifikatet i ett redigeringsprogram. Uppdatera värdet `PUBLIC_KEY_CERTIFICATE_STRING` med värdet för det mellanliggande signeringscertifikatet.

        Om du har genererat enhetscertifikaten med Bash-gränssnittet innehåller *./certs/azure-iot-test-only.intermediate.cert.pem* nyckeln för det mellanliggande certifikatet. Om certifikaten har genererats med PowerShell är *./Intermediate1.pem* filen med det mellanliggande certifikatet.

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. Navigera till IoT-hubben som är länkad till din etableringstjänst i [Azure Portal](https://portal.azure.com). Öppna fliken **Översikt** för hubben och kopiera **värdnamnet**. Tilldela **värdnamnet** till parametern *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Studera exempelkoden. Den skapar, uppdaterar, frågar och tar bort en gruppregistrering för X.509-enheter. Du kan verifiera att registreringen har lyckats i portalen genom att tillfälligt kommentera ut följande rader med kod i filen _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Spara filen _ServiceEnrollmentGroupSample.java_.

1. Öppna ett kommandofönster och navigera till mappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Skapa exempelkoden genom att använda följande kommando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Kör exemplet genom att använda de här kommandona i kommandofönstret:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Se utdatafönstret om registreringen har lyckats.

    ![Lyckad registrering](./media/tutorial-group-enrollments/enrollment.png) 

1. Navigera till etableringstjänsten i Azure Portal. Klicka på **Hantera registreringar**. Observera att gruppen med X. 509-enheter visas under fliken **registrerings grupper** med ett automatiskt genererat *grupp namn*.

## <a name="simulate-the-device"></a>Simulera enheten

1. På sammanfattnings bladet för enhets etablerings tjänsten väljer du **Översikt** och noterar ditt _ID-omfång_ och en _Global slut punkt för etablerings tjänsten_.

    ![Tjänstinformation](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Öppna en kommandotolk. Navigera till exempelprojektmappen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Redigera `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` så att _ID-omfång_ och _Global slutpunkt för etableringstjänsten_ som du noterade tidigare inkluderas.

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. Uppdatera variablerna `leafPublicPem` och `leafPrivateKey` med de offentliga och privata enhetscertifikaten.

    Om du har genererat enhetscertifikaten med PowerShell innehåller filerna mydevice* den offentliga nyckeln, den privata nyckeln och PFX för enheten.

    Om du har genererat enhetscertifikaten med Bash-gränssnittet innehåller ./certs/new-device.cert.pem den offentliga nyckeln. Enhetens privata nyckel finns i filen ./private/new-device.key.pem.

    Öppna filen med den offentliga nyckeln och uppdatera variabeln `leafPublicPem` med det värdet. Kopiera texten från _-----BEGIN PRIVATE KEY-----_ till _-----END PRIVATE KEY-----_.

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    Öppna filen med den privata nyckeln och uppdatera variabeln `leafPrivatePem` med det värdet. Kopiera texten från _-----BEGIN RSA PRIVATE KEY-----_ till _-----END RSA PRIVATE KEY-----_.

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. Lägg till en ny variabel nedanför `leafPrivateKey` för det mellanliggande certifikatet. Ge den här nya variabeln namnet `intermediateKey`. Ge den värdet för det mellanliggande signeringscertifikatet.

    Om du har genererat enhetscertifikaten med Bash-gränssnittet innehåller *./certs/azure-iot-test-only.intermediate.cert.pem* nyckeln för det mellanliggande certifikatet. Om certifikaten har genererats med PowerShell är *./Intermediate1.pem* filen med det mellanliggande certifikatet.

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. I funktionen `main` lägger du till `intermediateKey` i samlingen `signerCertificates` före initieringen av `securityProviderX509`.

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. Spara dina ändringar och skapa exemplet. Navigera till målmappen och kör den skapade jar-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Lyckad registrering](./media/tutorial-group-enrollments/registration.png)

1. I portalen går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **Device Explorer** med *STATUS***aktiverad**. Observera att du kan behöva klicka på **uppdateringsknappen** högst upp om du redan har öppnat bladet innan du kör programmet på exempelenheten. 

    ![Enheten är registrerad på IoT-hubben](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna bladet **Hantera registreringar** för tjänsten och klicka sedan på fliken **enskilda registreringar** . Välj *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna bladet **IoT-enheter** för din hubb, välj *ENHETS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en X.509-simulerad enhet på Windows-datorn och etablerat den på IoT-hubben med hjälp av Azure IoT Hub Device Provisioning-tjänsten och registreringsgrupper. Om du vill läsa mer om X.509-enheten fortsätter du till enhetsbegreppen. 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service begrepp](concepts-service.md)