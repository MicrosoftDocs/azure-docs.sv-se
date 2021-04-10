---
title: Översätt Modbus-protokoll med gatewayer – Azure IoT Edge | Microsoft Docs
description: Tillåt att enheter som använder Modbus TCP kan kommunicera med Azure IoT Hub genom att skapa en IoT Edge-gatewayenhet
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103487742"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Ansluta Modbus TCP-enheter via en IoT Edge enhets-Gateway

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Om du vill ansluta IoT-enheter som använder Modbus TCP-eller RTU-protokoll till en Azure IoT-hubb kan du använda en IoT Edge enhet som en gateway. Gatewayenheten läser data från Modbus-enheterna och kommunicerar sedan dessa data till molnet med ett protokoll som stöds.

![Modbus-enheter ansluter till IoT Hub via IoT Edge Gateway](./media/deploy-modbus-gateway/diagram.png)

Den här artikeln beskriver hur du skapar en egen containeravbildning för en Modbus-modul (eller så kan du använda ett fördefinierat exempel) och sedan distribuera den till IoT Edge-enheten som fungerar som gateway.

Den här artikeln förutsätter att du använder Modbus TCP-protokollet. Mer information om hur du konfigurerar modulen så att den stöder Modbus-RTU finns i [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) -projekt på GitHub.

## <a name="prerequisites"></a>Förutsättningar

* En Azure IoT Edge-enhet. En genom gång av hur du konfigurerar en sådan finns i [distribuera Azure IoT Edge på Windows](quickstart.md) eller [Linux](quickstart-linux.md).
* Primärnyckelns anslutningssträng för IoT Edge-enheten.
* En fysisk eller simulerad Modbus-enhet som stöder Modbus TCP. Du måste känna till dess IPv4-adress.

## <a name="prepare-a-modbus-container"></a>Förbereda en Modbus-container

Om du vill testa Modbus-gatewayens funktioner har Microsoft en exempelmodul som du kan använda. Du kan komma åt modulen från Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)eller med avbildnings-URI: n `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Om du vill skapa en egen modul och anpassa den för din miljö finns det ett projekt med öppen källkod [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) på GitHub. Följ vägledningarna i projektet för att skapa en egen containeravbildning. Information om hur du skapar en behållar avbildning finns i [utveckla C#-moduler i Visual Studio](./how-to-visual-studio-develop-module.md) eller [utveckla moduler i Visual Studio Code](how-to-vs-code-develop-module.md). Dessa artiklar innehåller instruktioner om hur du skapar nya moduler och publicerar behållar avbildningar till ett register.

## <a name="try-the-solution"></a>Testa lösningen

Det här avsnittet beskriver hur du distribuerar Microsofts exempel-Modbus-modul till din IoT Edge-enhet.

1. Gå till IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. Gå till **IoT Edge** och klicka på IoT Edge enheten.

3. Välj **Ange moduler**.

4. I avsnittet **IoT Edge modules** lägger du till modulen Modbus:

   1. Klicka på list rutan **Lägg till** och välj **Marketplace-modul**.
   2. Sök efter `Modbus` och välj **Modbus TCP-modulen** av Microsoft.
   3. Modulen konfigureras automatiskt för IoT Hub och visas i listan med IoT Edge moduler. Vägarna konfigureras också automatiskt. Välj **Granska + skapa**.
   4. Granska distributions manifestet och välj **skapa**.

5. Välj modulen Modbus, `ModbusTCPModule` i listan och välj fliken **dubbla inställningar** . Den obligatoriska JSON-filen för modulens dubbla önskade egenskaper fylls i automatiskt.

6. Leta efter egenskapen **SlaveConnection** i JSON och ange dess värde till IPv4-adressen för din Modbus-enhet.

7. Välj **Uppdatera**.

8. Välj **Granska + skapa**, granska distributionen och välj sedan **skapa**.

9. Återgå till informationssidan om enheten och välj **Uppdatera**. Du bör se att den nya `ModbusTCPModule` modulen körs tillsammans med IoT Edge Runtime.

## <a name="view-data"></a>Visa data

Visa data som kommer genom Modbus-modulen:

```cmd/sh
iotedge logs modbus
```

Du kan också Visa telemetri som enheten skickar genom att använda [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (tidigare Azure IoT Toolkit-tillägget).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur IoT Edge enheter kan fungera som gatewayer finns i [skapa en IoT Edge enhet som fungerar som en transparent Gateway](./how-to-create-transparent-gateway.md).
* Mer information om hur IoT Edge moduler fungerar finns i [förstå Azure IoT Edge moduler](iot-edge-modules.md).