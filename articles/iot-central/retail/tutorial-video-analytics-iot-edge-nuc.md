---
title: Självstudie – Skapa en video analys IoT Edge instans i Azure IoT Central (Intel NUC)
description: Den här självstudien visar hur du skapar en video analys IoT Edge-instans som ska användas med program mal len video analys – objekt och rörelse identifiering.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831934"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Självstudie: skapa en IoT Edge-instans för video analys (Intel NUC)

Azure IoT Edge är en fullständigt hanterad tjänst som levererar Cloud Intelligence lokalt genom att distribuera och köra:

* Anpassad logik
* Azure-tjänster
* Artificiell intelligens

I IoT Edge körs dessa tjänster direkt på IoT-enheter över plattformar, så att du kan köra IoT-lösningen på ett säkert sätt och i skala i molnet eller offline.

Den här självstudien visar hur du installerar och konfigurerar IoT Edge runtime på en Intel NUC-enhet.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Uppdatera och konfigurera IoT Edge
> * Konfigurera IoT Edge Gateway
> * Ansluta en lokal ONVIF-kompatibel kamera till din Intel NUC-enhet

## <a name="prerequisites"></a>Förutsättningar

* Innan du börjar bör du slutföra själv studie kursen [skapa ett video analys program i azure IoT Central (Yolo v3)](./tutorial-video-analytics-create-app-yolo-v3.md) eller [skapa en video analys i Azure IoT Central (openproduktion &trade; )](tutorial-video-analytics-create-app-openvino.md).
* En enhet, till exempel en Intel-NUC, som kör Linux, som kan köra Docker-behållare och har tillräckligt med processor kraft för att köra video analys.
* Den [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md) som är installerad och körs på enheten.
* För att kunna ansluta till den IoT Edge enheten från din Windows-dator behöver du den [SPARATILLFIL SSH-klienten](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) eller ett motsvarande verktyg.
* Du behöver också en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en kostnads fri på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Konfigurera IoT Edge-enheten

Om du inte har installerat IoT Edge runtime på din Intel NUC-dator kan du läsa mer i [installera Azure IoT Edge runtime på Debian-baserade Linux Systems-](../../iot-edge/how-to-install-iot-edge.md) instruktioner.

Så här uppdaterar du IoT Edge Runtime:

1. Använd verktyget SparaTillFil för att ansluta till den IoT Edge enheten.

1. Kör följande kommandon för att uppdatera och kontrol lera versionen av IoT Edge Runtime. Vid tidpunkten för skrivning är versionen 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Använd följande kommandon för att skapa de mappar som distributionen använder med de behörigheter som krävs:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Så här lägger du till *state.jsi* konfigurations filen till mappen */data/storage* på din IoT Edge-enhet:

1. Använd en text redigerare för att öppna *state.js* filen i mappen *lva* på din lokala dator.

1. Uppdatera `system` `iotCentral > properties` plats hållarna och med sträng värden som beskriver din gateway-enhet. Du kan visa dessa värden senare i instrument panelen för IoT Central-programmet.

1. Uppdatera `iotCentral > appKeys` plats hållarna med de värden som du antecknade i *scratchpad.txt* -filen i föregående självstudie. Spara ändringarna.

1. Använd `scp` verktyget SparaTillFil i en kommando tolk för att kopiera *state.jspå* en fil som du just har redigerat till mappen */data/Storage* på din IoT Edge-enhet. Det här exemplet använder `192.168.0.144` som ett exempel på en IP-adress, ersätter det med IP-adressen för din IoT Edge-enhet:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Konfigurera IoT Edge för att registrera och ansluta till ditt IoT Central-program:

1. Använd verktyget SparaTillFil för att ansluta till den IoT Edge enheten.

1. Använd en text redigerare, till exempel `nano` , för att öppna filen IoT Edge config. yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML-filer kan inte använda tabbar för indrag, Använd två blank steg i stället. Toppnivå objekt får inte ha inledande blank steg.

1. Rulla nedåt tills du ser `# Manual provisioning configuration` . Kommentera ut följande tre rader som du ser i följande kodfragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Rulla nedåt tills du ser `# DPS symmetric key provisioning configuration` . Ta bort kommentaren till de efterföljande åtta raderna som visas i följande kodfragment:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Ersätt `{scope_id}` med **ID-omfånget** som du gjorde en anteckning om i *scratchpad.txt* -filen i föregående självstudie.

1. Ersätt `{registration_id}` med *Gateway – 001* den enhet som du skapade i föregående självstudie.

1. Ersätt `{symmetric_key}` med den **primära nyckeln** för den **Gateway-001-** enhet som du antecknade i *scratchpad.txt* -filen i föregående självstudie.

1. Kör följande kommando för att starta om IoT Edge daemon:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Kör följande kommando för att kontrol lera status för IoT Edge moduler:

    ```bash
    iotedge list
    ```

    Utdata från kommandot tidigare visar fem moduler som körs. Du kan också visa status för de moduler som körs i IoT Central-programmet.

    > [!TIP]
    > Du kan köra det här kommandot igen för att kontrol lera statusen. Du kan behöva vänta tills alla moduler börjar köras.

Om IoT Edge moduler inte startar på rätt sätt går [du till felsöka IoT Edge enheten](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Samla in RTSP-strömmen från kameran

Identifiera RTSP-Stream-URL: erna för de kameror som är anslutna till din IoT Edge-enhet, till exempel:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Försök att Visa kamera strömmen på den IoT Edge datorn med en medie spelare som VLC.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med programmet kan du ta bort alla resurser som du har skapat på följande sätt:

1. I IoT Central programmet navigerar du till sidan **program** i avsnittet **Administration** . Välj sedan **Ta bort**.
1. Ta bort resurs gruppen **lva-RG** i Azure Portal.
1. På den lokala datorn stoppar du Docker-behållaren för **amp-visningsprogrammet** .

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat IoT Edge Runtime och LVA-modulerna till Intel NUC gateway-enheten.

Följ nästa självstudie om du vill hantera kameror:

> [!div class="nextstepaction"]
> [Övervaka och hantera ett program för video analys – identifiering av objekt och motion](./tutorial-video-analytics-manage.md)