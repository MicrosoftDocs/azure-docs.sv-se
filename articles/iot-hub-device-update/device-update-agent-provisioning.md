---
title: Etablering av enhets uppdatering för Azure IoT Hub agent | Microsoft Docs
description: Etablering av enhets uppdatering för Azure IoT Hub agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a5f4b23196a04d88e4329cb5ebf26d0b0a477444
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307277"
---
# <a name="device-update-agent-provisioning"></a>Agent etablering för enhets uppdatering

Agenten för enhets uppdaterings modulen kan köras tillsammans med andra system processer och [IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) som ansluter till din IoT Hub som en del av samma logiska enhet. I det här avsnittet beskrivs hur du etablerar enhets uppdaterings agenten som en modul identitet. 


## <a name="module-identity-vs-device-identity"></a>Modul identitet vs enhets identitet

I IoT Hub kan du under varje enhets identitet skapa upp till 50 modul identiteter. Varje moduls identitet genererar implicit en modul med dubbla. På enhets sidan kan du med IoT Hub enhets-SDK: er skapa moduler där var och en öppnar en oberoende anslutning till IoT Hub. Modul identitet och modul dubbla ger liknande funktioner som enhets identitet och enhet, men med en bättre precision. [Lär dig mer om modul identiteter i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Stöd för enhets uppdatering

Följande IoT-enhets typer stöds för närvarande med enhets uppdatering:

* Linux-enheter (IoT Edge-och icke-IoT Edge enheter):
    * Bild A/B-uppdatering:
        - Yocto-ARM64 (referens avbildning), utöknings bar via öppen källkod för att [skapa egna avbildningar](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) för annan arkitektur vid behov.
        - Ubuntu 18,04-Simulator
       
    * Paket agent stöds versioner för följande plattformar/arkitekturer:
        - Ubuntu Server 18,04 x64 Package agent 
        - Debian 9 
        
* Begränsade enheter:
    * AzureRTOS för enhets uppdaterings agent: [enhets uppdatering för azure IoT Hub självstudie för Azure – real tids operativ system](device-update-azure-real-time-operating-system.md)

* Frånkopplade enheter: 
    * [Förstå stöd för frånkopplad enhets uppdatering](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Förutsättningar  

Om du konfigurerar IoT-enheten/IoT Edge enheten för [paketbaserade uppdateringar](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)lägger du till packages.Microsoft.com i din dators databaser genom att följa dessa steg:

1. Logga in på datorn eller IoT-enheten som du vill installera enhets uppdaterings agenten på.

1. Öppna ett terminalfönster.

1. Installera den lagrings plats konfiguration som matchar enhetens operativ system.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Kopiera den genererade listan till katalogen sources. list. d.
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

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Så här etablerar du enhets uppdaterings agenten som en modul identitet

I det här avsnittet beskrivs hur du etablerar enhets uppdaterings agenten som en modul identitet på IoT Edge aktiverade enheter, icke-Edge IoT-enheter och andra IoT-enheter.


### <a name="on-iot-edge-enabled-devices"></a>På IoT Edge aktiverade enheter

Följ dessa anvisningar för att etablera enhets uppdaterings agenten på [IoT Edge aktiverade enheter](https://docs.microsoft.com/azure/iot-edge).

1. Följ anvisningarna för att [Installera och etablera Azure IoT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Installera uppdaterings agenten för enhets uppdatering
    - Vi tillhandahåller exempel bilder i [artefakter](https://github.com/Azure/iot-hub-device-update/releases) för att prova avbildnings uppdaterings distributioner till olika versioner med en bas avbildning (ADU-Base-image) och en uppdaterings avbildning (ADU-Update-image). Se exempel på [hur du blinkar avbildningen till din IoT Hub-enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).  

1. Installera uppdaterings agenten för enhets uppdaterings paketet  
    - För de senaste agent versionerna från packages.miscrosoft.com: uppdatera paket listor på enheten och installera enhets uppdaterings agent paketet och dess beroenden med:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - För kommande versions kandidat versioner från [artefakter](https://github.com/Azure/iot-hub-device-update/releases) : Hämta DEP-filen till den dator som du vill installera enhets uppdaterings agenten på och sedan:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Nu kan du starta enhets uppdaterings agenten på din IoT Edge enhet. 

### <a name="on-non-edge-iot-linux-devices"></a>På icke-Edge IoT Linux-enheter

Följ de här anvisningarna för att etablera enhets uppdaterings agenten på IoT Linux-enheter.

1. Installera IoT Identity service och Lägg till den senaste versionen på din IoT-enhet. 
    1. Logga in på datorn eller IoT-enheten.
    1. Öppna ett terminalfönster.
    1.  Installera den senaste [IoT-identitets tjänsten](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) på din IoT-enhet med hjälp av det här kommandot:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Etablering av IoT Identity service för att hämta information om IoT-enheter.
    * Skapa en anpassad kopia av konfigurations mal len så att vi kan lägga till etablerings information. I en Terminal anger du kommandot nedan.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Redigera sedan konfigurations filen så att den innehåller anslutnings strängen för den enhet som du vill agera som etablerings enhet för enheten eller datorn. I en Terminal anger du kommandot nedan.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Du bör se ett meddelande som liknar följande exempel:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagram över konfigurations filen för IoT Identity service." lightbox="media/understand-device-update/config.png":::

    1. I samma nano-fönster hittar du blocket med "manuell etablering med anslutnings strängen".
    1. I fönstret tar du bort "#"-symbolen före ' etableringen '
    1. I fönstret tar du bort "#"-symbolen före källan 
    1. I fönstret tar du bort "#"-symbolen före "connection_string"
    1. Ta bort strängen inom citat tecken till höger om "connection_string" i fönstret och Lägg sedan till anslutnings strängen där 
    1. Spara ändringarna i filen med Ctrl + X och sedan Y och tryck på RETUR för att spara ändringarna. 
    
1.  Tillämpa nu och starta om IoT Identity service med kommandot nedan. Nu bör du se en "färdig!" utskrift som innebär att du har konfigurerat IoT Identity service.

    > [!Note]
    > IoT Identity service registrerar modul identiteter med IoT Hub med hjälp av symmetriska nycklar för närvarande.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Installera slutligen enhets uppdaterings agenten. Vi tillhandahåller exempel bilder i [artefakter](https://github.com/Azure/iot-hub-device-update/releases) för att prova avbildnings uppdaterings distributioner till olika versioner med en bas avbildning (ADU-Base-image) och en uppdaterings avbildning (ADU-Update-image). Se exempel på [hur du blinkar avbildningen till din IoT Hub-enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Nu kan du starta enhets uppdaterings agenten på din IoT-enhet. 

### <a name="other-iot-devices"></a>Andra IoT-enheter

Enhets uppdaterings agenten kan också konfigureras utan IoT Identity service för testning eller på begränsade enheter. Följ stegen nedan för att etablera enhets uppdaterings agenten med hjälp av en anslutnings sträng (från modulen eller enheten).

1.  Vi tillhandahåller exempel bilder i [artefakter](https://github.com/Azure/iot-hub-device-update/releases) för att prova avbildnings uppdaterings distributioner till olika versioner med en bas avbildning (ADU-Base-image) och en uppdaterings avbildning (ADU-Update-image). Se exempel på [hur du blinkar avbildningen till din IoT Hub-enhet](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image).

1.  Logga in på datorn eller IoT Edge enhet/IoT-enhet.
    
1.  Öppna ett terminalfönster.

1.  Lägg till anslutnings strängen i [konfigurations filen för enhets uppdatering](device-update-configuration-file.md):
    1. Ange följande i terminalfönstret:
        - [Paket uppdateringar](device-update-ubuntu-agent.md) använder: sudo nano/etc/ADU/adu-conf.txt
        - Använd [avbildnings uppdateringar](device-update-raspberry-pi.md) : sudo nano/ADU/adu-conf.txt
       
    1. Du bör se ett fönster med lite text. Ta bort hela strängen som följer connection_String = första gången du etablerar enhets uppdaterings agenten på IoT-enheten. Den placerar bara företagarens text.
    
    1. I terminalen ersätter du "<anslutnings strängen>" med anslutnings strängen för enheten för din instans av enhets uppdaterings agenten.
    
        > [!Important]
        > Lägg inte till citat tecken runt anslutnings strängen.
        ```shell
        - connection_string=<ADD CONNECTION STRING HERE>
       ```
       
    1. Ange och spara.
    
1.  Nu är du redo att starta enhets uppdaterings agenten på din IoT-enhet. 


## <a name="how-to-start-the-device-update-agent"></a>Så här startar du enhets uppdaterings agenten

I det här avsnittet beskrivs hur du startar och verifierar enhets uppdaterings agenten när en modul identitet körs på din IoT-enhet.

1.  Logga in på datorn eller enheten där enhets uppdaterings agenten är installerad.

1.  Öppna ett terminalfönster och ange kommandot nedan.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Du kan kontrol lera agentens status med hjälp av kommandot nedan. Om du ser några problem kan du läsa den här [fel söknings guiden](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Du bör se status OK.

1.  På IoT Hub portal går du till IoT-enheten eller IoT Edge enheter för att hitta den enhet som du konfigurerade med enhets uppdaterings agenten. Där visas enhets uppdaterings agenten som körs som en modul. Exempel:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagram över namn på enhets uppdaterings modul." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Skapa och köra enhets uppdaterings agenten

Du kan också bygga och ändra din egen kund enhets uppdaterings agent.

Följ anvisningarna för att [bygga](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) enhets uppdaterings agenten från källan.

När agenten har börjat skapas är det dags att [köra](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) agenten.

Nu ska du göra ändringarna som krävs för att införliva agenten i din avbildning.  Se hur du [ändrar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) enhets uppdaterings agenten för vägledning.


## <a name="troubleshooting-guide"></a>Felsökningsguide

Om du stöter på problem granskar du enhets uppdateringen för IoT Hub [fel söknings guide](troubleshoot-device-update.md) för att förhindra eventuella problem och samla in nödvändig information för att ge Microsoft.


## <a name="next-steps"></a>Nästa steg

Du kan använda följande färdiga avbildningar och binärfiler för att få en enkel demonstration av enhets uppdatering för IoT Hub:

- [Avbildnings uppdatering: komma igång med Raspberry Pi 3 B + Reference yocto-avbildningen](device-update-raspberry-pi.md) som är utöknings bar via öppen källkod för att skapa egna avbildningar för annan arkitektur vid behov.

- [Komma igång med hjälp av Ubuntu (18,04 x64) Simulator referens agent](device-update-simulator.md)

- [Paket uppdatering: Komma igång med Ubuntu Server 18,04 x64 Package agent](device-update-ubuntu-agent.md)

- [Uppdatering av enhets uppdateringar för Azure IoT Hub-självstudier för Azure – real tids operativ system](device-update-azure-real-time-operating-system.md)

