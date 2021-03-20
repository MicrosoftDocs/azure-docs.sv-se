---
title: Använd Azure-enheten för att styra MXChip IoT DevKit User lampa | Microsoft Docs
description: I den här självstudien får du lära dig hur du övervakar DevKit-tillstånd och styr användar INDIKATORn med Azure IoT Hub enhets dubbla.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148789"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Du kan använda det här exemplet för att övervaka MXChip IoT DevKit WiFi-information och sensor tillstånd och för att styra färgen på användaren med hjälp av Azure IoT Hub enhets dubbla.

## <a name="what-you-learn"></a>Detta får du får lära dig

- Så här övervakar du MXChip IoT DevKit sensor tillstånd.

- Så här använder du Azure-enheten för att styra färgen på DevKit RGB-LED.

## <a name="what-you-need"></a>Detta behöver du

- Konfigurera utvecklings miljön genom att följa [komma igång guide](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

- I GitBash-terminalfönstret (eller något annat kommando rads gränssnitt för git) skriver du följande kommandon:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

1. Klicka på den nedrullningsbara menyn **aktiviteter** i Visual Studio Code och välj **Kör aktivitet...**  -  **Cloud-etableringen**.

2. Din förloppet visas under fliken **Terminal** i **välkomst** panelen.

3. När du tillfrågas om meddelandet *vilken prenumeration vill du välja väljer du* en prenumeration.

4. Välj eller Välj en resurs grupp. 
 
   > [!NOTE]
   > Om du redan har ett kostnads fritt IoT Hub kan du hoppa över det här steget.

5. När du tillfrågas om meddelandet *vad IoT Hub vill du välja* eller skapa en IoT Hub.

6. Något liknar *Function-appen: Function-appens namn: xxx* visas. Anteckna namnet på appens funktion. den kommer att användas i ett senare steg.

7. Vänta tills distribution av Azure Resource Manager mal len har slutförts, vilket anges när distributionen av Message *Resource Manager-mall: klar* visas.

## <a name="deploy-function-app"></a>Distribuera Funktionsapp

1. Klicka på den nedrullningsbara menyn **aktiviteter** i Visual Studio Code och välj **Kör aktivitet...**  -  **Cloud-Deploy**.

2. Vänta på att funktionen för att ladda appens kod ska kunna slutföras. meddelande *funktionens app distribueras: färdig* visas.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurera IoT Hub enhets anslutnings sträng i DevKit

1. Anslut din MXChip IoT-DevKit till din dator.

2. Klicka på den nedrullningsbara menyn **aktiviteter** i Visual Studio Code och välj **Kör aktivitet...**  -  **konfiguration – enhets anslutning**

3. Tryck på och håll ned knapp **a** på MXChip IoT-DevKit, tryck på **återställnings** knappen och släpp sedan knappen **A** för att göra DekKit att ange konfigurations läge.

4. Vänta tills konfigurations processen för anslutnings strängen har slutförts.

## <a name="upload-arduino-code-to-devkit"></a>Ladda upp Arduino-kod till DevKit

Med din MXChip IoT-DevKit ansluten till datorn:

1. Klicka på den nedrullningsbara menyn **aktiviteter** i Visual Studio Code och välj **Kör skapa uppgift...** Arduino-skissen kompileras och överförs till DevKit.

2. När skissen har laddats upp visas ett meddelande om att *Ladda upp skissen & upload: lyckades* .

## <a name="monitor-devkit-state-in-browser"></a>Övervaka DevKit tillstånd i webbläsare

1. I en webbläsare öppnar du `DevKitState\web\index.html` filen – som du skapade i steget vad du behöver.

2. Följande webb sida visas:![Ange namnet på Function-appen.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Mata in namnet på den funktions app som du skrev ned tidigare.

4. Klicka på knappen **Anslut**

5. Inom några sekunder uppdateras sidan och visar DevKit status för WiFi-anslutning och tillståndet för var och en av de inbyggda sensorerna.

## <a name="control-the-devkits-user-led"></a>Styr DevKit användar indikator

1. Klicka på bilden för användar LAMPAn på webb sidan.

2. Inom några sekunder uppdateras skärmen och visar den aktuella färg statusen för användar INDIKATORn.

3. Försök att ändra färg värde för RGB-LYSDIODen genom att klicka på olika platser i kontrollerna för RGB-skjutreglage.

## <a name="example-operation"></a>Exempel åtgärd

![Exempel på test procedur](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Du kan se rå data för enhet dubbla i Azure Portal: IoT Hub- \> IoT-enheter – \> *\<your device\>*  - \> enhets dubbla.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att:
- Anslut en MXChip IoT DevKit-enhet till din Azure IoT-lösning för övervakning av fjärrstyrning.
- Använd Azure IoT-enheten för att förstå och kontrol lera färgen på DevKit RGB-LED.

Här är det föreslagna nästa steg: [Översikt över Azure IoT Remote Monitoring Solution Accelerator](/azure/iot-suite/)