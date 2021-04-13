---
title: Etablering av enhetsuppdatering för Azure IoT Hub Agent| Microsoft Docs
description: Etablering av enhetsuppdatering för Azure IoT Hub agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365458"
---
# <a name="device-update-agent-provisioning"></a>Etablering av enhetsuppdateringsagent

Agenten för enhetsuppdateringsmodulen kan köras tillsammans [med andra systemprocesser IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) som ansluter till din IoT Hub som en del av samma logiska enhet. I det här avsnittet beskrivs hur du etablerar enhetsuppdateringsagenten som en modulidentitet. 


## <a name="module-identity-vs-device-identity"></a>Modulidentitet jämfört med enhetsidentitet

I IoT Hub kan du skapa upp till 50 modulidentiteter under varje enhetsidentitet. Varje modulidentitet genererar implicit en modultvilling. På enhetssidan kan du IoT Hub enhets-SDK:er så att du kan skapa moduler där var och en öppnar en oberoende anslutning till IoT Hub. Modulidentitet och modultvilling ger liknande funktioner som enhetsidentitet och enhetstvilling men med en finare kornighet. [Läs mer om modulidentiteter i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Stöd för enhetsuppdatering

Följande IoT-enhetstyper stöds för närvarande med Enhetsuppdatering:

* Linux-enheter (IoT Edge och icke-IoT Edge enheter):
    * Bild A/B-uppdatering:
        - Yocto – ARM64 (referensbild), utökningsbar via öppen källkod för att skapa egna [avbildningar](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) för annan arkitektur efter behov.
        - Ubuntu 18.04-simulator
       
    * Paketagenten har stöd för byggen för följande plattformar/arkitekturer:
        - Ubuntu Server 18.04 x64 Package Agent 
        - Debian 9 
        
* Begränsade enheter:
    * AzureRTOS-enhetsuppdateringsagentexempel: Enhetsuppdatering för Azure IoT Hub självstudie [för Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Frånkopplade enheter: 
    * [Förstå stödet för frånkopplad enhetsuppdatering](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Förutsättningar  

Om du ställer in IoT-enheten/IoT Edge-enheten för paketbaserade uppdateringar [lägger](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)du till packages.microsoft.com till datorns lagringsplatsen genom att följa dessa steg:

1. Logga in på den dator eller IoT-enhet där du vill installera enhetsuppdateringsagenten.

1. Öppna ett terminalfönster.

1. Installera den lagringsplatskonfiguration som matchar enhetens operativsystem.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Kopiera den genererade listan till katalogen sources.list.d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Installera den offentliga nyckeln för Microsoft GPG.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Så här etablerar du enhetsuppdateringsagenten som en modulidentitet

I det här avsnittet beskrivs hur du etablerar enhetsuppdateringsagenten som en modulidentitet på IoT Edge-aktiverade enheter, IoT-enheter som inte är Edge-enheter och andra IoT-enheter.


### <a name="on-iot-edge-enabled-devices"></a>På IoT Edge aktiverade enheter

Följ de här instruktionerna för att etablera enhetsuppdateringsagenten [på IoT Edge aktiverade enheter.](https://docs.microsoft.com/azure/iot-edge)

1. Följ instruktionerna för [att installera och etablera Azure IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Installera agenten för avbildningsuppdatering för enhetsuppdatering
    - Vi tillhandahåller exempelavbildningar i Artifacts för att prova att distribuera avbildningsuppdatering till olika versioner med hjälp av en basavbildning (adu-base-image) och en [uppdateringsavbildning](https://github.com/Azure/iot-hub-device-update/releases) (adu-update-image). Se ett exempel [på hur du flashar avbildningen till din IoT Hub enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).  

1. Installera uppdateringsagenten för enhetsuppdateringspaketet  
    - För de senaste agentversionerna packages.miscrosoft.com: Uppdatera paketlistor på enheten och installera agentpaketet för enhetsuppdatering och dess beroenden med hjälp av:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - För kommande versioner av versionskandidater från [Artifacts:](https://github.com/Azure/iot-hub-device-update/releases) Ladda ned .dep-filen till den dator där du vill installera enhetsuppdateringsagenten och gör sedan följande:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Nu är du redo att starta enhetsuppdateringsagenten på din IoT Edge enhet. 

### <a name="on-non-edge-iot-linux-devices"></a>På IoT Linux-enheter som inte är edge-enheter

Följ de här instruktionerna för att etablera enhetsuppdateringsagenten på dina IoT Linux-enheter.

1. Installera IoT Identity Service och lägg till den senaste versionen till din IoT-enhet. 
    1. Logga in på datorn eller IoT-enheten.
    1. Öppna ett terminalfönster.
    1.  Installera den senaste [IoT Identity Service på](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) din IoT-enhet med det här kommandot:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Etablering av IoT Identity-tjänsten för att hämta information om IoT-enheten.
    * Skapa en anpassad kopia av konfigurationsmallen så att vi kan lägga till etableringsinformationen. I en terminal anger du kommandot nedan.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Redigera sedan konfigurationsfilen så att den innehåller anslutningssträngen för den enhet som du vill fungera som etablerare för den här enheten eller datorn. I en terminal anger du kommandot nedan.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Du bör se ett meddelande som liknar följande exempel:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram över konfigurationsfilen för IoT Identity Service." lightbox="media/understand-device-update/config.png":::

    1. I samma nano-fönster hittar du blocket med "Manuell etablering med anslutningssträng".
    1. I fönstret tar du bort symbolen "#" före "etablering"
    1. I fönstret tar du bort symbolen "#" före "källa" 
    1. Ta bort symbolen "#" före "connection_string" i fönstret
    1. I fönstret tar du bort strängen inom citattecken till höger om "connection_string" och lägger sedan till anslutningssträngen där 
    1. Spara ändringarna i filen med Ctrl + X och sedan "Y" och tryck på retur-tangenten för att spara ändringarna. 
    
1.  Tillämpa och starta om IoT Identity-tjänsten med kommandot nedan. Nu bör du se ett "Klar!" innebär att du har konfigurerat IoT Identity Service.

    > [!Note]
    > IoT Identity-tjänsten registrerar modulidentiteter med IoT Hub med hjälp av symmetriska nycklar för närvarande.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Installera slutligen enhetsuppdateringsagenten. Vi tillhandahåller exempelavbildningar i Artifacts för att prova att distribuera avbildningsuppdatering till olika versioner med hjälp av en basavbildning (adu-base-image) och en [uppdateringsavbildning](https://github.com/Azure/iot-hub-device-update/releases) (adu-update-image). Se ett exempel [på hur du flashar avbildningen till din IoT Hub enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Nu är du redo att starta enhetsuppdateringsagenten på din IoT-enhet. 

### <a name="other-iot-devices"></a>Andra IoT-enheter

Agenten för enhetsuppdatering kan också konfigureras utan IoT-identitetstjänsten för testning eller på begränsade enheter. Följ stegen nedan för att etablera enhetsuppdateringsagenten med hjälp av en anslutningssträng (från modulen eller enheten).

1.  Vi tillhandahåller exempelavbildningar i Artifacts för att prova att distribuera avbildningsuppdatering till olika versioner med hjälp av en basavbildning (adu-base-image) och en [uppdateringsavbildning](https://github.com/Azure/iot-hub-device-update/releases) (adu-update-image). Se ett exempel [på hur du flashar avbildningen till din IoT Hub enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Logga in på datorn eller IoT Edge/IoT-enhet.
    
1.  Öppna ett terminalfönster.

1.  Lägg till anslutningssträngen i [konfigurationsfilen för enhetsuppdatering:](device-update-configuration-file.md)
    1. Ange nedanstående i terminalfönstret:
        - [Användning av](device-update-ubuntu-agent.md) paketuppdateringar: sudo nano /etc/adu/adu-conf.txt
        - [Användning av](device-update-raspberry-pi.md) avbildningsuppdateringar: sudo nano /adu/adu-conf.txt
       
    1. Du bör se ett fönster öppet med lite text i det. Ta bort hela strängen efter "connection_String=" första gången du etablerar enhetsuppdateringsagenten på IoT-enheten. Det är bara platshållartext.
    
    1. I terminalen ersätter du "<-your-connection-string>" med anslutningssträngen för enheten för din instans av enhetsuppdateringsagenten.
    
        > [!Important]
        > Lägg inte till citattecken runt anslutningssträngen.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Ange och spara.
    
1.  Nu är du redo att starta enhetsuppdateringsagenten på din IoT-enhet. 


## <a name="how-to-start-the-device-update-agent"></a>Starta enhetsuppdateringsagenten

I det här avsnittet beskrivs hur du startar och verifierar enhetsuppdateringsagenten som en modulidentitet som körs på din IoT-enhet.

1.  Logga in på den dator eller enhet där enhetsuppdateringsagenten är installerad.

1.  Öppna ett terminalfönster och ange kommandot nedan.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Du kan kontrollera status för agenten med hjälp av kommandot nedan. Om du får problem kan du gå till den här [felsökningsguiden.](troubleshoot-device-update.md)
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Du bör se status OK.

1.  På den IoT Hub portalen går du till IoT-enhet eller IoT Edge för att hitta den enhet som du konfigurerade med enhetsuppdateringsagenten. Där ser du att enhetsuppdateringsagenten körs som en modul. Exempel:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram över modulnamn för enhetsuppdatering." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Skapa och köra enhetsuppdateringsagenten

Du kan också skapa och ändra din egen kunds enhetsuppdateringsagent.

Följ anvisningarna för [att skapa](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) enhetsuppdateringsagenten från källan.

När agenten har skapats är det dags att [köra](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) agenten.

Gör nu de ändringar som behövs för att införliva agenten i avbildningen.  Mer information finns i [hur du](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) ändrar enhetsuppdateringsagenten.


## <a name="troubleshooting-guide"></a>Felsökningsguide

Om du får problem kan du läsa felsökningsguiden för enhetsuppdatering för IoT Hub som hjälper dig att avblockera eventuella problem och samla in nödvändig information för att ge Microsoft. [](troubleshoot-device-update.md)


## <a name="next-steps"></a>Nästa steg

Du kan använda följande förbyggda avbildningar och binärfiler för en enkel demonstration av Enhetsuppdatering för IoT Hub:

- [Bilduppdatering: Komma igång med Raspberry Pi 3 B+ Referens yocto image](device-update-raspberry-pi.md) utökningsbar via öppen källkod för att skapa egna avbildningar för annan arkitektur efter behov.

- [Komma igång ubuntu-simulatorreferensagenten (18.04 x64)](device-update-simulator.md)

- [Paketuppdatering:Komma igång ubuntu server 18.04 x64-paketagent](device-update-ubuntu-agent.md)

- [Självstudiekurs Azure IoT Hub enhetsuppdatering för azure-realtidsoperativsystemet](device-update-azure-real-time-operating-system.md)

