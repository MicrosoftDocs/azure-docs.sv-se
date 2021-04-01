---
title: Snabb start – Använd symmetrisk nyckel för att etablera simulerad enhet till Azure IoT Hub med Java
description: I den här snabb starten ska du använda Java Device SDK för att skapa en simulerad enhet som använder symmetrisk nyckel med Azure-IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc, devx-track-java
ms.openlocfilehash: a32811b439a569a8f8f82fcc046e0f4b89e47a38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966708"
---
# <a name="quickstart-provision-a-simulated-device-to-iot-hub-with-symmetric-keys"></a>Snabb start: etablera en simulerad enhet för att IoT Hub med symmetriska nycklar

I den här snabbstarten får lära dig att skapa och köra en enhetssimulator på en Windows-utvecklingsdator. Du konfigurerar den här simulerade enheten för att använda en symmetrisk nyckel för att autentisera med en tjänst för enhets etablerings tjänsten (DPS) och tilldelas till en IoT-hubb. Exempel kod från [Microsoft Azure IoT SDK: er för Java](https://github.com/Azure/azure-iot-sdk-java) används för att simulera en startsekvens för enheten som initierar etablering. Enheten kommer att identifieras baserat på en enskild registrering med en DPS-tjänstinstans och tilldelas till en IoT-hubb.

Även om den här artikeln visar etablering med en enskild registrering kan du använda registrerings grupper. Det finns vissa skillnader när du använder registrerings grupper. Du måste till exempel använda en härledd enhets nyckel med ett unikt registrerings-ID för enheten. Även om registreringsgrupper för symmetrisk nyckel inte är begränsade till äldre enheter finns det ett exempel för registreringsgrupper i avsnittet om [hur du etablerar äldre enheter med symmetrisk nyckelattestering](how-to-legacy-device-symm-key.md). Mer information finns i avsnittet om [gruppregistreringar för symmetrisk nyckelattestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten. 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Kontrol lera att du har [Java se Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) eller senare installerat på datorn.

* Ladda ned och installera [Maven](https://maven.apache.org/install.html).

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Förbereda Java SDK-miljön 

1. Kontrollera att Git är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

2. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enhetssimuleringens kodexempel:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navigera till rot `azure-iot-sdk-java` katalogen och bygg projektet för att ladda ned alla nödvändiga paket.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering

1. Logga in på [Azure Portal](https://portal.azure.com), Välj knappen **alla resurser** i den vänstra menyn och öppna DPS-instansen (Device Provisioning service).

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till individuell registrering** överst. 

3. Ange följande information på panelen **Lägg till registrering** och tryck på knappen **Spara** .

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings *mekanism*.

   - **Generera nycklar automatiskt**: Markera den här kryss rutan.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel **symm-Key-Java-Device-007**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **Java-Device-007**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. När du har sparat registreringen genereras **primär nyckeln** och den **sekundära nyckeln** och läggs till i registrerings posten. Den symmetriska nyckeln enhets registrering visas som **symm-Key-Java-Device-007** under kolumnen *registrerings-ID* på fliken *enskilda* registreringar. 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Du kommer att använda det här nyckelvärdet och **registrerings-ID: t** senare när du uppdaterar Java-koden för enheten.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simulera startsekvens för enhet

I det här avsnittet ska du uppdatera enhets exempel koden för att skicka enhetens startsekvens till din DPS-instans. Den här startsekvensen gör att enheten kan identifieras, autentiseras och tilldelas till en IoT-hubb som är länkad till DPS-instansen.

1. Från menyn enhets etablerings tjänst väljer du **Översikt** och noterar ditt _ID-omfång_ och en _Global slut punkt för etablerings tjänsten_.

    ![Tjänstinformation](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Öppna exempel koden för Java-enheten för redigering. Den fullständiga sökvägen till enhets exempel koden är:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Lägg till _ID-omfånget_ och den _globala slut punkten för etablerings tjänsten_ för din DPS-instans. Inkludera även den primära symmetriska nyckeln och det registrerings-ID som du har valt för din enskilda registrering. Spara ändringarna. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Öppna en kommando tolk för att skapa. Navigera till mappen etablerings exempel projekt i Java SDK-lagringsplatsen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Skapa exemplet och navigera sedan till `target` mappen för att köra den skapade. jar-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Förväntade utdata bör se ut ungefär så här:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. I Azure Portal går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. Efter lyckad etablering av den simulerade symmetriska nyckel enheten i hubben visas dess enhets-ID på bladet **Device Explorer** ,  med status **aktive rad**.  Du kan behöva klicka på knappen **Uppdatera** längst upp om du redan har öppnat bladet innan du kör programmet för enhets exempel. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Öppna **Hantera registreringar** för din tjänst och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för enheten som du har registrerat i den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret. 
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Öppna **IoT-enheter** för navet, markera kryss rutan bredvid *enhets-ID* för enheten som du registrerade i den här snabb starten och tryck sedan på knappen **ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en simulerad enhet på Windows-datorn och allokerat den till IoT-hubben med hjälp av symmetrisk nyckel med Azure-IoT Hub Device Provisioning Service på portalen. Om du vill lära dig hur du registrerar enheten program mässigt fortsätter du till snabb starten för program mässig registrering av X. 509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabb start – registrera X. 509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)