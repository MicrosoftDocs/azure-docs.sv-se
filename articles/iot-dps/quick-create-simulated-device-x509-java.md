---
title: Snabb start – etablera simulerad X. 509-enhet till Azure IoT Hub med Java
description: Azure snabb start – skapa och etablera en simulerad X. 509-enhet med hjälp av Java-enhets-SDK för IoT Hub Device Provisioning Service (DPS). I den här snabb starten används enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 2ed458ee0267a65bb7551940fc522509dc310311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966623"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabb start: skapa och etablera en simulerad X. 509-enhet med Java-enhets-SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

I den här snabb starten skapar du en simulerad X. 509-enhet på en Windows-dator. Du använder Device-exempel-Java-kod för att ansluta den här simulerade enheten med IoT-hubben med hjälp av en enskild registrering med enhets etablerings tjänsten (DPS).

## <a name="prerequisites"></a>Förutsättningar

- Bekant med [etablerings](about-iot-dps.md#provisioning-process) koncept.
- Slut för ande av [konfigurations IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md).
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java se Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Kontrollera att [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) är installerat på datorn.

2. Ladda ned och installera [Maven](https://maven.apache.org/install.html).

3. Kontrollera att Git är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

4. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enhetssimuleringens kodexempel:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Gå till rotkatalogen `azure-iot-sdk-`java` och skapa projektet om du vill hämta alla nödvändiga paket.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navigera till certifikatgeneratorprojektet och skapa projektet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Skapa ett självsignerat X.509-enhetscertifikat och en post för enskild registrering

I det här avsnittet använder du ett självsignerat X.509-certifikat. Det är viktigt att tänka på följande:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Du kommer att använda exempelkoden från [Azure IoT SDK för Java](https://github.com/Azure/azure-iot-sdk-java.git) till att skapa det certifikat som ska användas med posten för enskild registrering för den simulerade enheten.

Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

1. Använd kommando tolken från föregående steg, navigera till `target` mappen och kör sedan. jar-filen som skapades i föregående steg.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Ange **N** för _Do you want to input common name_ (Vill du ange ett eget namn). Kopiera utdata för `Client Cert` med start från *-----BEGIN CERTIFICATE-----* och till och med *-----END CERTIFICATE-----* till Urklipp.

   ![Generator för enskilt certifikat](./media/java-quick-create-simulated-device-x509/individual.png)

3. Skapa en fil med namnet **_X509individual.pem_** på din Windows-dator, öppna den i valfritt redigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen och stäng redigeringsprogrammet.

4. I kommando tolken anger du **N** för vill _du ha indata från verifierings koden_ och se till att programutdata är öppna för referens senare i snabb starten. Senare ska du kopiera värdena `Client Cert` och `Client Cert Private Key` och använda dem i nästa avsnitt.

5. Logga in på [Azure Portal](https://portal.azure.com), Välj knappen **alla resurser** i den vänstra menyn och öppna din enhets etablerings tjänst instans.

6. Från menyn enhets etablerings tjänst väljer du **Hantera registreringar**. Välj fliken **enskilda registreringar** och välj knappen **Lägg till individuell registrering** överst. 

7. Ange följande information på panelen **Lägg till registrering** :
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under *filen Primary Certificate. pem eller. cer* väljer du *Välj en fil* för att välja certifikat filen **X509individual. pem** som skapades i föregående steg.  
   - Du kan även ange följande information:
     - Välj en IoT hub som är länkad till din etableringstjänst.
     - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet. 
     - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
     - När du är klar trycker du på knappen **Spara** . 

     [![Lägg till enskild registrering för X. 509-attestering i portalen](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Om registreringen har lyckats visas din X.509-enhet som **microsoftriotcore** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 



## <a name="simulate-the-device"></a>Simulera enheten

1. Från menyn enhets etablerings tjänst väljer du **Översikt** och noterar ditt _ID-omfång_ och en _Global slut punkt för etablerings tjänsten_.

    ![Tjänstinformation](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Öppna en kommandotolk. Navigera till exempelprojektmappen för Java SDK-lagringsplatsen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Ange etableringstjänsten och X.509-identitetsinformation i koden. Den används vid automatisk etablering, för attestering av den simulerade enheten, före enhetsregistrering:

   - Redigera filen `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` så att _ID-omfång_ och _Global slutpunkt för etableringstjänsten_ inkluderas enligt tidigare notering. Inkludera även _Client Cert_ (Klientcertifikat) och _Client Cert Private Key_ (Privat nyckel för klientcertifikat) enligt notering i föregående avsnitt.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Använd följande format när du kopierar och klistrar in certifikatet och den privata nyckeln:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Skapa exemplet. Navigera till `target` mappen och kör den skapade jar-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. I Azure Portal går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **Device Explorer** med *STATUS***aktiverad**.  Du kan behöva klicka på knappen **Uppdatera** längst upp om du redan har öppnat bladet innan du kör programmet för enhets exempel. 

    ![Enheten är registrerad på IoT-hubben](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
2. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna bladet **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
3. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna bladet **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för den enhet som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en simulerad X. 509-enhet på Windows-datorn. Du konfigurerade registreringen i Azure IoT Hub Device Provisioning Service och etablerade sedan automatiskt enheten i din IoT-hubb. Om du vill lära dig hur du registrerar X. 509-enheten program mässigt fortsätter du till snabb starten för program mässig registrering av X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – registrera X. 509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)