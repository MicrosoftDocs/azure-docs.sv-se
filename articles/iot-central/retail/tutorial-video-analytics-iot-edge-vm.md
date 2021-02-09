---
title: Självstudie – Skapa en video analys IoT Edge instans i Azure IoT Central (Linux VM)
description: Den här självstudien visar hur du skapar en video analys IoT Edge instans på en virtuell Linux-dator som ska användas med program mal len video analys – objekt och rörelse identifiering.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832631"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Självstudie: skapa en IoT Edge-instans för video analys (Linux VM)

Azure IoT Edge är en fullständigt hanterad tjänst som levererar Cloud Intelligence lokalt genom att distribuera och köra:

* Anpassad logik
* Azure-tjänster
* Artificiell intelligens

I IoT Edge körs dessa tjänster direkt på IoT-enheter över plattformar, så att du kan köra IoT-lösningen på ett säkert sätt och i skala i molnet eller offline.

Den här självstudien visar hur du förbereder en IoT Edge-enhet på en virtuell Azure-dator. IoT Edge-instansen kör de direktsända video analys moduler som används av program mal len Azure IoT Central video analys – objekt och rörelse identifiering.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en virtuell Azure-dator med Azure IoT Edge Runtime installerat
> * Förbered IoT Edge-installationen för att vara värd för modulen för video analys och Anslut till IoT Central

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra självstudien för att [skapa ett video analys program i azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) eller [skapa en video analys i Azure IoT Central- &trade; ](tutorial-video-analytics-create-app-openvino.md) självstudier.

Du behöver också en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en kostnads fri på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Distribuera Azure IoT Edge

Om du vill skapa en virtuell Azure-dator med de senaste IoT Edge Runtime-och Live Video Analytics-modulerna installerade, väljer du följande knapp:

[![Knappen Distribuera till Azure för iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Använd informationen i följande tabell för att slutföra det **anpassade distributions** formuläret:

| Fält | Värde |
| ----- | ----- |
| Prenumeration | Välj din Azure-prenumeration. |
| Resursgrupp | *lva-RG* – resurs gruppen du skapade i föregående självstudie. |
| Region       | *East US* |
| DNS-etikett-prefix | Välj ett unikt DNS-prefix för den virtuella datorn. Måste vara alla alfabetiska tecken, inga siffror eller specialtecken. |
| Administratörens användar namn | *AzureUser* |
| Adminlösenord | Ange ett lösen ord. Anteckna lösen ordet i *scratchpad.txt* -filen. du kan använda det senare. |
| Omfångs-ID | Det **omfångs-ID** du antecknade i *scratchpad.txt* -filen i den föregående själv studie kursen när du lade till gateway-enheten. |
| Enhets-ID | *Gateway – 001* – den gateway-enhet som du skapade i föregående självstudie. |
| Enhets nyckel | **Enhetens primära nyckel** du antecknade i *scratchpad.txt* -filen i den föregående själv studie kursen när du lade till gateway-enheten. |
| IoT Central app-värd | **URL:** en för programmet som du antecknade i *scratchpad.txt* -filen i föregående självstudie. Till exempel *Traders.azureiotcentral.com*. |
| API-token för IoT Central app | **Operatörens API-token** du antecknade i *scratchpad.txt* -filen i föregående självstudie. |
| Etablerings nyckel för IoT Central Device | Den **primära nyckeln för SAS-IoT-enheter** som du antecknade i *scratchpad.txt* -filen i föregående självstudie. |
| Storlek på virtuell dator | *Standard_DS1_v2* |
| Ubuntu OS-version | *18,04 – LTS* |
| Location | *[resourceGroup (). location]* |

Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Det tar vanligt vis cirka tre minuter innan distributionen har slutförts. När distributionen är klar navigerar du till resurs gruppen **lva-RG** i Azure Portal.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Se till att IoT Edge runtime läser in modulerna

I Azure Portal navigerar du till resurs gruppen **lva-RG** och väljer den virtuella datorn. I avsnittet support och **fel sökning** väljer du **seriell konsol**.

Hämta en prompt genom att trycka på **RETUR** `login:` . Använd *AzureUser* som användar namn och lösen ord som du valde när du skapade den virtuella datorn.

Kör följande kommando för att kontrol lera versionen av IoT Edge Runtime. Vid tidpunkten för skrivning är versionen 1.0.9:

```bash
sudo iotedge --version
```

Visa en lista med IoT Edge moduler med hjälp av kommandot:

```bash
sudo iotedge list
```

Distributionen konfigurerade följande fem IoT Edge moduler att köra:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

Distributionen skapade en anpassad IoT Edge miljö med de moduler som krävs för live video analys. Distributionen uppdaterade standard **config. yaml** för att säkerställa att IoT Edge runtime använde IoT Device Provisioning-tjänsten för att ansluta till IoT Central. Distributionen skapade också en fil med namnet **state.js** i mappen **/data/Storage** för att tillhandahålla ytterligare konfigurations data till modulerna. Mer information finns i själv studie kursen [skapa en IoT Edge instans för video analys (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) .

Information om hur du felsöker IoT Edge-enheten finns i [felsöka IoT Edge-enheten](../../iot-edge/troubleshoot.md)

## <a name="use-the-rtsp-simulator"></a>Använda RTSP-simulatorn

Om du inte har riktiga kamera enheter för att ansluta till din IoT Edge-enhet kan du använda de två simulerade kamera enheterna i program mal len video analys. I det här avsnittet visas hur du använder en simulerad video ström i din IoT Edge-enhet.

Dessa instruktioner använder [Live555 Media-servern](http://www.live555.com/mediaServer/) som en RTSP-Simulator i en Docker-behållare.

> [!NOTE]
> Referenser till program vara från tredje part i denna lagrings platsen är endast för informations-och bekvämlighets skull. Microsoft varken rekommenderar eller tillhandahåller rättigheter för program vara från tredje part. Mer information finns i [Live555 Media Server](http://www.live555.com/mediaServer/).

Använd följande kommando för att köra verktyget **rtspvideo** i en Docker-behållare på din IoT Edge virtuella dator. Docker-behållaren skapar en Background RTSP-ström:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Använd följande kommando för att Visa Docker-behållare:

```bash
sudo docker ps
```

Listan innehåller en behållare som heter **live555**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med programmet kan du ta bort alla resurser som du har skapat på följande sätt:

1. I IoT Central programmet navigerar du till sidan **program** i avsnittet **Administration** . Välj sedan **Ta bort**.
1. Ta bort resurs gruppen **lva-RG** i Azure Portal.
1. På den lokala datorn stoppar du Docker-behållaren för **amp-visningsprogrammet** .

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat IoT Edge runtime, LVA-modulerna och Live555s simulerings ström i en virtuell Linux-dator som körs på Azure.

Följ nästa självstudie om du vill hantera kamerorna

> [!div class="nextstepaction"]
> [Övervaka och hantera ett program för video analys – identifiering av objekt och motion](./tutorial-video-analytics-manage.md)