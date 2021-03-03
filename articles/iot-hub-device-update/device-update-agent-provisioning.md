---
title: Etablering av enhets uppdatering för Azure IoT Hub agent | Microsoft Docs
description: Etablering av enhets uppdatering för Azure IoT Hub agent
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 79bac3f057412f3973121f48cd735f72d0a97d04
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680015"
---
# <a name="device-update-agent"></a>Enhets uppdaterings agent

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad. 

* Avbildnings uppdateringar ger en högre säkerhets nivå i enhetens slut tillstånd. Det är vanligt vis enklare att replikera resultaten av en avbildnings uppdatering mellan en för produktions miljö och en produktions miljö, eftersom den inte innebär samma utmaningar som paket och deras beroenden. På grund av deras atomiska natur kan en enkel-/B-redundansrelation också användas. 
* Paketbaserade uppdateringar är riktade uppdateringar som bara ändrar en specifik komponent eller ett program på enheten. Därmed kan det leda till lägre konsumtion av bandbredden och minska tiden för att ladda ned och installera uppdateringen. Paket uppdateringar tillåter normalt mindre stillestånd av enheter när en uppdatering tillämpas och du slipper att skapa avbildningar. 

Följ länkarna nedan om hur du skapar, kör och ändrar enhets uppdaterings agenten.

## <a name="build-the-device-update-agent"></a>Bygg enhets uppdaterings agenten

Följ anvisningarna för att [bygga](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) enhets uppdaterings agenten från källan.

## <a name="run-the-device-update-agent"></a>Kör enhets uppdaterings agenten

När agenten har skapats, är det dags att [köra](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) agenten.

## <a name="modifying-the-device-update-agent"></a>Ändra enhets uppdaterings agenten

Nu ska du göra ändringarna som krävs för att införliva agenten i din avbildning.  Se hur du [ändrar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.m) enhets uppdaterings agenten för vägledning.

### <a name="troubleshooting-guide"></a>Felsökningsguide

Om du stöter på problem granskar du enhets uppdateringen för IoT Hub [fel söknings guide](troubleshoot-device-update.md) för att förhindra eventuella problem och samla in nödvändig information för att ge Microsoft.

## <a name="next-steps"></a>Nästa steg

Använd följande färdiga avbildningar och binärfiler för en enkel demonstration av enhets uppdatering för IoT Hub.  

[Avbildnings uppdatering: Komma igång med Raspberry Pi 3 B + referens yocto image](device-update-raspberry-pi.md)

[Avbildnings uppdatering: Komma igång med hjälp av Ubuntu (18,04 x64) Simulator referens agent](device-update-simulator.md)

[Paket uppdatering: Komma igång med Ubuntu Server 18,04 x64 Package agent](device-update-ubuntu-agent.md)

