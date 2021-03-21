---
title: Ansluta en RuuviTag i Azure IoT Central | Microsoft Docs
description: Lär dig hur du ansluter en RuuviTag-miljö sensor till ditt IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 7cf0d31895eaeaa41c23f9f07664b27bed1d3126
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006395"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Ansluta en RuuviTag-sensor till ditt Azure IoT Central-program

*Den här artikeln gäller lösnings byggare.*

I den här artikeln beskrivs hur du kan ansluta en RuuviTag-sensor till ditt Microsoft Azure IoT Central program med hjälp av Solution Builder.

Vad är en Ruuvi-tagg?

RuuviTag är en avancerad sensor för öppen källkod som är utformad för att uppfylla behoven hos företags kunder, utvecklare, tillverkare, studenter och hobbyer. Enheten ställs in så att den fungerar så snart du tar den i sin låda och är redo att distribuera den där du behöver den. Det är en Bluetooth LE-Beacon med en miljö sensor och en accelerometer inbyggd.

RuuviTag kommunicerar via Bell (Bluetooth Low Energy) och kräver en gateway-enhet för att kommunicera med Azure IoT Central. Se till att du har en gateway-enhet, t. ex. Rigado kaskad 500, för att aktivera en RuuviTag för att ansluta till IoT Central.

Följ [anvisningarna här](./howto-connect-rigado-cascade-500.md) om du vill konfigurera en Rigado kaskad 500-gateway-enhet.

## <a name="prerequisites"></a>Förutsättningar

För att ansluta RuuviTag-sensorer behöver du följande resurser:

* En RuuviTag-sensor. Mer information finns på [RuuviTag](https://ruuvi.com/).
* En Rigado kaskad 500-enhet eller en annan Bell-Gateway. Mer information finns på [Rigado](https://www.rigado.com/).
* Ett Azure IoT Central-program. Mer information finns i avsnittet [skapa ett nytt program](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Lägg till en mall för RuuviTag-enhet

Om du vill publicera en RuuviTag-sensor i din Azure IoT Central-programinstans måste du konfigurera en motsvarande enhets mall i ditt program.

Så här lägger du till en RuuviTag enhets mal len:

1. Gå till fliken ***enhets mallar** _ i den vänstra rutan, Välj _* + ny * *: ![ Skapa ny enhets mall ](./media/howto-connect-ruuvi/devicetemplate-new.png) sidan ger dig ett alternativ till ***skapa en anpassad mall**_ eller _ *_Använd en förkonfigurerad enhets mall_**
1. Välj enhets mal len RuuviTag i listan med förkonfigurerade enhetsspecifika mallar enligt nedan:  ![ Välj enhets mal len för RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Välj ***Nästa: anpassa*** för att fortsätta till nästa steg.
1. På nästa skärm väljer du ***skapa*** för att publicera enhets mal len C500 i ditt IoT Central-program.

## <a name="connect-a-ruuvitag-sensor"></a>Ansluta en RuuviTag-sensor

Som tidigare nämnts måste du konfigurera en gateway-enhet för att ansluta RuuviTag med ditt IoT Central-program. Stegen nedan förutsätter att du har konfigurerat en Rigado kaskad 500-gateway-enhet.  

1. Slå på din Rigado kaskad 500-enhet och Anslut den till din nätverks anslutning (via Ethernet eller trådlöst)
1. Stäng av RuuviTag och hämta fliken plast för att skydda anslutningen till batteriet.
1. Placera RuuviTagen nära en Rigado kaskad 500-gateway som redan har kon figurer ATS i IoT Central-programmet.
1. På bara några sekunder bör din RuuviTag visas i listan över enheter i IoT Central.  
    ![Lista över RuuviTag-enheter](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Du kan nu använda den här RuuviTag i ditt IoT Central-program.  

## <a name="create-a-simulated-ruuvitag"></a>Skapa en simulerad RuuviTag

Om du inte har någon fysisk RuuviTag-enhet kan du skapa en simulerad RuuviTag sensor som används för testning i ditt Azure IoT Central-program.

Så här skapar du en simulerad RuuviTag:

1. Välj **enheter > RuuviTag**.
1. Välj **+ Ny**.
1. Ange ett unikt **enhets-ID** och ett eget **enhets namn**.  
1. Aktivera **simulerad** inställning.
1. Välj **Skapa**.  

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Läs om [enhets anslutning i Azure IoT Central](./concepts-get-connected.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
